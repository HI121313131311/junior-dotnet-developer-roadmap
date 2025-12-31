# ASP.NET Core Logging with Serilog

## 1. ကျွန်တော်တို့ရဲ့ project မှာ Serilog packages တွေ install လုပ်ပါမယ်။

```bash
dotnet add package Serilog.AspNetCore
dotnet add package Serilog.Sinks.File
dotnet add package Serilog.Sinks.MSSqlServer
```

### 1.1. `Serilog.AspNetCore`

ဒီ package က Serilog ကို ကျွန်တော်တို့ရဲ့ ASP.NET Core application နဲ့ ချိတ်ဆက်ပေးတဲ့ အဓိက package ဖြစ်ပါတယ်။ သူက ASP.NET Core ရဲ့ built-in logging system နေရာမှာ Serilog ကို အစားထိုးဝင်ရောက်စေပြီး application တစ်ခုလုံးက log တွေကို Serilog ကနေတစ်ဆင့် စီမံခန့်ခွဲနိုင်အောင် လုပ်ဆောင်ပေးပါတယ်။ ဥပမာ - HTTP request logs တွေလိုမျိုး framework က ထုတ်ပေးတဲ့ log တွေကိုပါ ဖမ်းယူပေးနိုင်ပါတယ်. `Program.cs` မှာ `builder.Host.UseSerilog()` ဆိုတဲ့ method ကို သုံးနိုင်တာက ဒီ package ကြောင့် ဖြစ်ပါတယ်။

### 1.2. `Serilog.Sinks.File`

"Sink" ဆိုတာက Serilog ကနေ log message တွေကို ဘယ်နေရာမှာ သိမ်းဆည်းမလဲဆိုတာကို သတ်မှတ်ပေးတဲ့ အရာကို ခေါ်တာပါ။ ဒီ `Serilog.Sinks.File` package ကတော့ log တွေကို text file တွေအနေနဲ့ သိမ်းဆည်းပေးတဲ့ sink တစ်ခု ဖြစ်ပါတယ်။ ကျွန်တော်တို့ရဲ့ `Program.cs` ထဲမှာ `.WriteTo.File(...)` ဆိုပြီး configure လုပ်ထားတဲ့အတွက် application က log တွေကို သတ်မှတ်ထားတဲ့ `logs` folder ထဲမှာ text file တွေနဲ့ သိမ်းဆည်းပေးမှာ ဖြစ်ပါတယ်။

### 1.3. `Serilog.Sinks.MSSqlServer`

ဒီ package ကတော့ နောက်ထပ် sink တစ်မျိုးဖြစ်ပြီး log တွေကို Microsoft SQL Server database ထဲမှာရှိတဲ့ table တစ်ခုထဲကို သိမ်းဆည်းပေးပါတယ်။ ကျွန်တော်တို့ `Program.cs` မှာ `.WriteTo.MSSqlServer(...)` နဲ့ configure လုပ်ထားတဲ့အတွက် application က log data တွေကို connection string မှာ ပေးထားတဲ့ database ရဲ့ `Logs` ဆိုတဲ့ table ထဲမှာ အလိုအလျောက် သွားသိမ်းပေးမှာ ဖြစ်ပါတယ်။ ဒီ sink ကိုသုံးခြင်းအားဖြင့် log တွေကို database ထဲမှာ စနစ်တကျ သိမ်းဆည်းပြီး နောက်ပိုင်းတွေမှာ အလွယ်တကူ ပြန်လည်ရှာဖွေ နိုင်မှာ ဖြစ်ပါတယ်။

---

## 2. Program.cs မှာ Serilog Configuration

ကျွန်တော်တို့ရဲ့ Program.cs file မှာ Serilog ကို configure လုပ်ပါမယ်။

```csharp
using Serilog;

var builder = WebApplication.CreateBuilder(args);

// Serilog Configuration လုပ်ပါမယ်
Log.Logger = new LoggerConfiguration()
    .WriteTo.Console()
    .WriteTo.File("logs/log-.txt",
        rollingInterval: RollingInterval.Hour,
        fileSizeLimitBytes: 10485760, // 10MB
        retainedFileCountLimit: 24)
    .WriteTo.MSSqlServer(
        connectionString: builder.Configuration.GetConnectionString("DefaultConnection"),
        tableName: "Logs",
        autoCreateSqlTable: true)
    .CreateLogger();

builder.Host.UseSerilog();

// Add services to the container
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// Configure the HTTP request pipeline
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();

try
{
    Log.Information("Application starting up");
    app.Run();
}
catch (Exception ex)
{
    Log.Fatal(ex, "Application start-up failed");
}
finally
{
    Log.CloseAndFlush();
}
```

## 3. appsettings.json Configuration

ကျွန်တော်တို့ရဲ့ appsettings.json file မှာ database connection string ထည့်သွင်းပါမယ်။

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=LoggingDb;User ID=sa;Password=sasa@123;TrustServerCertificate=true;MultipleActiveResultSets=true;"
  },
  "AllowedHosts": "*"
}
```

## 4. Controller မှာ ILogger နဲ့ Log အသုံးပြုခြင်း

Controller ထဲမှာ ILogger interface နဲ့ static Log class နှစ်မျိုးလုံး အသုံးပြုပါမယ်။

```csharp
using Microsoft.AspNetCore.Mvc;
using Serilog;

[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    private readonly ILogger<ProductsController> _logger;

    public ProductsController(ILogger<ProductsController> logger)
    {
        _logger = logger;
    }

    [HttpGet]
    public IActionResult GetProducts()
    {
        // ILogger ကို အသုံးပြုပြီး log ရေးပါမယ်
        _logger.LogInformation("GetProducts method called");

        var products = new List<string> { "Product1", "Product2", "Product3" };

        // Static Log class ကို အသုံးပြုပြီး log ရေးပါမယ်
        Log.Information("Retrieved {ProductCount} products", products.Count);

        return Ok(products);
    }

    [HttpPost]
    public IActionResult CreateProduct([FromBody] string productName)
    {
        _logger.LogInformation("Creating product: {ProductName}", productName);

        if (string.IsNullOrEmpty(productName))
        {
            Log.Warning("Empty product name provided");
            return BadRequest("Product name cannot be empty");
        }

        // Product creation logic
        _logger.LogInformation("Product created successfully: {ProductName}", productName);

        return Ok($"Product {productName} created");
    }

    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
        try
        {
            Log.Information("Getting product with ID: {ProductId}", id);

            if (id <= 0)
            {
                _logger.LogWarning("Invalid product ID: {ProductId}", id);
                return BadRequest("Invalid product ID");
            }

            var product = $"Product{id}";
            _logger.LogInformation("Product found: {ProductName}", product);

            return Ok(product);
        }
        catch (Exception ex)
        {
            Log.Error(ex, "Error getting product with ID: {ProductId}", id);
            return StatusCode(500, "Internal server error");
        }
    }
}
```

## 5. Service Class မှာ ILogger အသုံးပြုခြင်း

Business logic service class တစ်ခုထဲမှာ ILogger ကို အသုံးပြုပါမယ်။

```csharp
public class OrderService
{
    private readonly ILogger<OrderService> _logger;

    public OrderService(ILogger<OrderService> logger)
    {
        _logger = logger;
    }

    public void ProcessOrder(int orderId, string customerName)
    {
        _logger.LogInformation("Processing order {OrderId} for customer {CustomerName}",
            orderId, customerName);

        try
        {
            // Order processing logic
            _logger.LogDebug("Validating order items");

            // Simulate business logic
            if (orderId > 1000)
            {
                _logger.LogWarning("Large order ID detected: {OrderId}", orderId);
            }

            // Process order
            _logger.LogInformation("Order {OrderId} processed successfully", orderId);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Failed to process order {OrderId}", orderId);
            throw;
        }
    }

    public void CancelOrder(int orderId, string reason)
    {
        Log.Warning("Cancelling order {OrderId}. Reason: {CancellationReason}",
            orderId, reason);

        // Cancellation logic
        Log.Information("Order {OrderId} cancelled successfully", orderId);
    }
}
```

## 6. Static Utility Class မှာ Log အသုံးပြုခြင်း

Static utility class တစ်ခုထဲမှာ static Log class ကို အသုံးပြုပါမယ်။

```csharp
public static class EmailService
{
    public static void SendEmail(string to, string subject)
    {
        Log.Information("Sending email to {Recipient} with subject: {EmailSubject}",
            to, subject);

        try
        {
            // Email sending logic
            Log.Debug("Building email message");

            // Simulate email sending
            Log.Information("Email sent successfully to {Recipient}", to);
        }
        catch (Exception ex)
        {
            Log.Error(ex, "Failed to send email to {Recipient}", to);
            throw;
        }
    }
}
```

## 7. Testing Controller ဖန်တီးခြင်း

Logging system test လုပ်ဖို့ controller တစ်ခု ဖန်တီးပါမယ်။

```csharp
[ApiController]
[Route("api/[controller]")]
public class TestController : ControllerBase
{
    private readonly ILogger<TestController> _logger;

    public TestController(ILogger<TestController> logger)
    {
        _logger = logger;
    }

    [HttpGet("info")]
    public IActionResult TestInfoLog()
    {
        _logger.LogInformation("This is an information level log from ILogger");
        Log.Information("This is an information level log from static Log");
        return Ok("Information log test completed");
    }

    [HttpGet("warning")]
    public IActionResult TestWarningLog()
    {
        _logger.LogWarning("This is a warning level log from ILogger");
        Log.Warning("This is a warning level log from static Log");
        return Ok("Warning log test completed");
    }

    [HttpGet("error")]
    public IActionResult TestErrorLog()
    {
        try
        {
            throw new InvalidOperationException("This is a test exception for error logging");
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "This is an error level log with exception from ILogger");
            Log.Error(ex, "This is an error level log with exception from static Log");
        }

        return Ok("Error log test completed");
    }

    [HttpGet("mixed")]
    public IActionResult TestMixedLogging()
    {
        var orderService = new OrderService(
            LoggerFactory.Create(builder => builder.AddSerilog()).CreateLogger<OrderService>());

        orderService.ProcessOrder(1001, "John Doe");

        EmailService.SendEmail("test@example.com", "Test Email");

        return Ok("Mixed logging test completed");
    }
}
```

## 8. Application Run and Test

ကျွန်တော်တို့ရဲ့ application ကို run ပြီး test လုပ်ကြည့်ပါမယ်။

```bash
dotnet run
```

**Test Steps -**

1. Browser ကနေ `https://localhost:7000/api/test/info` ကို သွားပါမယ်
2. `https://localhost:7000/api/test/warning` ကို သွားပါမယ်
3. `https://localhost:7000/api/test/error` ကို သွားပါမယ်
4. `https://localhost:7000/api/test/mixed` ကို သွားပါမယ်
5. `https://localhost:7000/api/products` ကို သွားပါမယ်

## 9. Log Results ကြည့်ရှုခြင်း

### Console Output ကြည့်ခြင်း

Application console မှာ real-time log messages တွေ မြင်ရပါမယ်။

### Text File Logs ကြည့်ခြင်း

`logs` folder ထဲက `log-YYYYMMDD-HH.txt` file တွေကို ဖွင့်ကြည့်ပါမယ်။

### Database Logs ကြည့်ခြင်း

SQL Server Management Studio ကနေ Logs table ထဲမှာ data တွေ ကြည့်ပါမယ်။

```sql
SELECT * FROM Logs ORDER BY TimeStamp DESC;
```

## 10. Log File Structure

ကျွန်တော်တို့ရဲ့ text log file တွေမှာ ဒီလို format နဲ့ log တွေ ရေးသားပါမယ်။

```verilog
[14:30:15 INF] Application starting up
[14:30:16 INF] GetProducts method called
[14:30:16 INF] Retrieved 3 products
[14:30:20 WRN] Empty product name provided
[14:30:25 ERR] Error getting product with ID: -1
[14:30:30 INF] Processing order 1001 for customer John Doe
[14:30:30 WRN] Large order ID detected: 1001
```

## 11. File Rolling Configuration

ကျွန်တော်တို့ရဲ့ file configuration မှာ -

- `RollingInterval.Hour` - တစ်နာရီတစ်ခါ new file create လုပ်ပါမယ်
- `fileSizeLimitBytes: 10485760` - File size 10MB ထက်ကျော်ရင် new file create လုပ်ပါမယ်
- `retainedFileCountLimit: 24` - Old file ၂၄ ခုထက်မပိုပါ

ဒီ configuration နဲ့ဆိုရင် log file တွေ အရမ်းကြီးမသွားပါဘူး။ ကြည့်ရှုရလွယ်ကူပါတယ်။

ကျွန်တော်တို့ရဲ့ .NET 8 application မှာ ILogger interface နဲ့ static Log class နှစ်မျိုးလုံးကို အသုံးပြုပြီး console, text file, နဲ့ database ဆိုတဲ့ output (၃) မျိုးစလုံးကို logging လုပ်နိုင်ပါပြီ။