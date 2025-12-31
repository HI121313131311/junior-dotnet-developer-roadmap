# Introduction to Logging in ASP.NET Core

Logging ဆိုတာက ကျွန်တော်တို့ရဲ့ application တွေမှာ ဖြစ်ပျက်နေတဲ့ အဖြစ်အပျက်တွေ (events)၊ အမှားတွေ (errors) နဲ့ တခြားအရေးကြီးတဲ့ အချက်အလက်တွေကို မှတ်တမ်းတင်တဲ့ လုပ်ငန်းစဉ်တစ်ခု ဖြစ်ပါတယ်။ ဒီလိုမှတ်တမ်းတင်ထားခြင်းအားဖြင့် application မှာ ပြဿနာတစ်ခုခုဖြစ်လာတဲ့အခါ အလွယ်တကူ ပြန်လည်စစ်ဆေးနိုင်ပြီး အမှားရှာရတာ ပိုမိုမြန်ဆန်စေပါတယ်။ ASP.NET Core မှာ logging system က built-in ပါဝင်ပြီးသားဖြစ်လို့ အလွယ်တကူ အသုံးပြုနိုင်ပါတယ်။

---

### **Application Behavior ကို နားလည်ခြင်း**

ကျွန်တော်တို့ရဲ့ application ဘယ်လို run နေသလဲ၊ ဘာ errors တွေဖြစ်နေသလဲဆိုတာ logging မရှိရင် မသိနိုင်ပါဘူး။

### **Error Tracking နဲ့ Debugging**

Production environment မှာ problem တစ်ခုခုဖြစ်ရင် logging ကနေတဆင့် error ရဲ့ root cause ကို အလွယ်တကူရှာဖွေနိုင်ပါတယ်။

### **Performance Monitoring**

ကျွန်တော်တို့ရဲ့ application ရဲ့ performance ကောင်းမွန်နေရဲ့လားဆိုတာ logging ကနေ monitor လုပ်နိုင်ပါတယ်။

---

# Logging သုံးခြင်းကနေ ကျွန်တော်တို့ရဲ့ application အတွက် ဒီ benefits တွေရရှိပါတယ်။

- **Problem Identification** - Issues တွေကို မြန်မြန်ဆန်ဆန် identify လုပ်နိုင်ပါတယ်
- **Performance Insights** - Application performance ကို understand လုပ်နိုင်ပါတယ်
- **Audit Trail** - User activities တွေကို track လုပ်နိုင်ပါတယ်
- **Maintenance** - Application maintain လုပ်ရတာ easy ဖြစ်ပါတယ်

---

# Understanding Log Levels

ASP.NET Core logging မှာ အဖြစ်အပျက်တွေကို အရေးကြီးတဲ့ အဆင့်အလိုက် ခွဲခြားမှတ်တမ်းတင်နိုင်ဖို့ Log Levels တွေ သတ်မှတ်ထားပါတယ်။ Log Level တွေကို အသုံးပြုပြီး ကျွန်တော်တို့ လိုအပ်တဲ့ level အလိုက် log တွေကို filter လုပ်နိုင်ပါတယ်။ Log level တွေက အနိမ့်ဆုံးကနေ အမြင့်ဆုံးထိ အစဉ်လိုက်ရှိပါတယ်။

- **Trace (Level 0)**
    - ဒါကတော့ အသေးစိတ်အကျဆုံး logging level ဖြစ်ပါတယ်။ Application ရဲ့ လုပ်ဆောင်ချက်တိုင်းကို ခြေရာခံချင်တဲ့အခါမျိုးမှာ အသုံးပြုပါတယ်။
- **Debug (Level 1)**
    - Development ပြုလုပ်နေစဉ်အတွင်းမှာ debugging လုပ်ဖို့အတွက် အသုံးဝင်တဲ့ အချက်အလက်တွေကို မှတ်တမ်းတင်ဖို့ အသုံးပြုပါတယ်။
- **Information (Level 2)**
    - Application ရဲ့ ပုံမှန်အလုပ်လုပ်ဆောင်မှုတွေကို ခြေရာခံဖို့အတွက် အသုံးပြုပါတယ်။ ဥပမာ - request တစ်ခုဝင်လာတာမျိုး၊ service တစ်ခုစတင်တာမျိုးတွေပါ။
- **Warning (Level 3)**
    - မမျှော်လင့်ထားတဲ့ ဒါမှမဟုတ် ပုံမှန်မဟုတ်တဲ့ အဖြစ်အပျက်တွေ ဖြစ်ပေါ်လာပေမဲ့ application ရဲ့ လုပ်ဆောင်ချက်ကို မရပ်တန့်သွားစေတဲ့ အခြေအနေတွေအတွက် အသုံးပြုပါတယ်။
- **Error (Level 4)**
    - Application ရဲ့ လက်ရှိလုပ်ဆောင်မှုကို မအောင်မြင်စေတဲ့ error တွေနဲ့ exception တွေကို မှတ်တမ်းတင်ဖို့ အသုံးပြုပါတယ်။ ဒါပေမဲ့ application တစ်ခုလုံးကိုတော့ crash မဖြစ်စေပါဘူး။
- **Critical (Level 5)**
    - Application တစ်ခုလုံးကို crash ဖြစ်သွားစေနိုင်တဲ့ ဒါမှမဟုတ် ချက်ချင်း အရေးယူဆောင်ရွက်ဖို့လိုအပ်တဲ့ ပြင်းထန်တဲ့ error တွေအတွက် အသုံးပြုပါတယ်။
- **None (Level 6)**
    - Logging ကို လုံးဝပိတ်ထားချင်တဲ့အခါ အသုံးပြုပါတယ်။

---

### Configuring Logging in Our Project

ကျွန်တော်တို့ရဲ့ ASP.NET Core project မှာ logging ကို `appsettings.json` file ကနေ အဓိက configure လုပ်ပါတယ်။ ဘယ် Log Level ကစပြီး မှတ်တမ်းတင်မလဲဆိုတာကို သတ်မှတ်နိုင်ပါတယ်။

ဥပမာအနေနဲ့ `appsettings.Development.json` မှာ အောက်ပါအတိုင်း ရေးပြပါမယ်။

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  }
}
```

ဒီ configuration ရဲ့ အဓိပ္ပာယ်ကတော့:

- `"Default": "Information"`: ကျွန်တော်တို့ရဲ့ project တစ်ခုလုံးအတွက် ပုံမှန်အားဖြင့် `Information` level နဲ့အထက် (Information, Warning, Error, Critical) log တွေကို မှတ်တမ်းတင်ပါမယ်လို့ သတ်မှတ်တာဖြစ်ပါတယ်။
- `"Microsoft.AspNetCore": "Warning"`: ဒါပေမဲ့ `Microsoft.AspNetCore` နဲ့စတဲ့ category (namespace) တွေကလာတဲ့ log တွေကိုတော့ `Warning` level နဲ့အထက်ကိုပဲ မှတ်တမ်းတင်ပါမယ်လို့ သီးသန့် သတ်မှတ်လိုက်တာ ဖြစ်ပါတယ်။ ဒါကြောင့် ASP.NET Core framework ကထုတ်တဲ့ Information log တွေက ကျွန်တော်တို့ရဲ့ log output မှာ ပေါ်လာတော့မှာ မဟုတ်ပါဘူး။

---

### How to Use Logging

Logging ကိုအသုံးပြုဖို့အတွက် `ILogger` interface ကို ကျွန်တော်တို့ရဲ့ class တွေမှာ Dependency Injection (DI) ကတစ်ဆင့် ထည့်သွင်းအသုံးပြုရမှာ ဖြစ်ပါတယ်။ Controller တစ်ခုမှာ ဘယ်လိုအသုံးပြုလဲဆိုတာကို ရေးပြပါမယ်။

```csharp
using Microsoft.AspNetCore.Mvc;

namespace OurWebApp.Controllers;

[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    private readonly ILogger<WeatherForecastController> _logger;

    public WeatherForecastController(ILogger<WeatherForecastController> logger)
    {
        _logger = logger;
    }

    [HttpGet(Name = "GetWeatherForecast")]
    public IEnumerable<WeatherForecast> Get()
    {
        _logger.LogInformation("Weather forecast data ကို တောင်းဆိုနေပါတယ်...");

        try
        {
            // လုပ်ဆောင်ချက်တစ်ခုခု လုပ်ဆောင်သည်ဟု ယူဆပါ
            if (DateTime.Now.Second % 2 != 0) // ဥပမာ အမှားတစ်ခုခုဖြစ်စေရန်
            {
                throw new InvalidOperationException("ဒေတာကို ရယူရာတွင် အမှားဖြစ်ပွားခဲ့သည်.");
            }

            var forecast = Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = DateOnly.FromDateTime(DateTime.Now.AddDays(index)),
                TemperatureC = Random.Shared.Next(-20, 55),
                Summary = "Sample"
            })
            .ToArray();
            
            _logger.LogInformation("Weather forecast data ပေးပို့ခြင်း အောင်မြင်ပါတယ်.");
            
            return forecast;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Weather forecast data ရယူရာတွင် အမှားတစ်ခုခု ဖြစ်ပွားခဲ့ပါတယ်.");
            // အမှားကို ပြန်လည်ကိုင်တွယ်ရန် နောက်ပိုင်းတွေမှာ ထပ်မံရေးသားနိုင်ပါတယ်
            throw; // Error ကို Global Exception Handler သို့ ပို့ပေးလိုက်ပါတယ်
        }
    }
}
```

ဒီ code မှာ `ILogger<WeatherForecastController>` ကို constructor ကတစ်ဆင့် inject လုပ်ပြီး `_logger` variable ထဲမှာ သိမ်းထားပါတယ်။ ပြီးရင် `Get` method ထဲမှာ `_logger.LogInformation()` နဲ့ `_logger.LogError()` ကို အသုံးပြုပြီး log တွေ မှတ်တမ်းတင်ထားတာကို တွေ့ရမှာပါ။

---

### **Logging Best Practices**

**Structured Logging သုံးပါ**

```csharp
// ❌ Avoid - ဒီလိုမျိုး string concatenation သုံးတာကို ရှောင်ပါ
_logger.LogInformation("User " + userName + " logged in at " + DateTime.Now);

// ✅ Recommended - Structured logging သုံးပါ
_logger.LogInformation("User {UserName} logged in at {LoginTime}", 
    userName, DateTime.Now);
```

**Appropriate Log Levels ရွေးချယ်ပါ**

```csharp
public class OrderService
{
    public void ProcessOrder(Order order)
    {
        // Critical - Application crash ဖြစ်နိုင်တဲ့ issues
        _logger.LogCritical("Database connection completely failed");
        
        // Error - Current operation fail ဖြစ်တဲ့ cases
        _logger.LogError("Failed to process order {OrderId}", order.Id);
        
        // Warning - Unexpected situation ဖြစ်ပေမယ့် application ဆက်အလုပ်လုပ်နိုင်တဲ့ cases
        _logger.LogWarning("Order amount {Amount} is unusually high", order.Amount);
        
        // Information - Normal workflow
        _logger.LogInformation("Order {OrderId} processed successfully", order.Id);
        
        // Debug - Development အတွက် detailed information
        _logger.LogDebug("Order processing details: {Details}", processingDetails);
        
        // Trace - Most detailed information
        _logger.LogTrace("Entering OrderService.ProcessOrder method");
    }
}
```

---

# Popular Third-Party Logging Providers

ASP.NET Core ရဲ့ built-in logging system က ကောင်းမွန်ပေမဲ့၊ ပိုပြီး feature စုံလင်တဲ့ logging ကိုအသုံးပြုချင်တယ်ဆိုရင်တော့ third-party logging framework တွေကို ထည့်သွင်းအသုံးပြုနိုင်ပါတယ်။ လူကြိုက်အများဆုံး framework တွေကတော့:

### 1. Serilog

Serilog က **structured logging** အတွက် နာမည်ကြီးပါတယ်. Structured logging ဆိုတာက log message တွေကို ရိုးရိုး text အဖြစ်မှတ်တမ်းမတင်ဘဲ JSON format လိုမျိုး key-value pair တွေနဲ့ စနစ်တကျ မှတ်တမ်းတင်တာဖြစ်ပါတယ်။ ဒါကြောင့် နောက်ပိုင်းတွေမှာ log တွေကို ပြန်လည်ရှာဖွေ၊ စစ်ဆေး၊ filter လုပ်တဲ့အခါမှာ အလွန်လွယ်ကူပါတယ်. Serilog မှာ log တွေကို ဘယ်နေရာမှာ သိမ်းမလဲဆိုတာကို သတ်မှတ်ပေးနိုင်တဲ့ **Sinks** တွေ အများကြီးရှိပါတယ်။ (ဥပမာ - Console, File, Database, Seq, Datadog)

### 2. NLog

NLog ကလည်း Serilog လိုပဲ configuration options တွေ အများကြီးပါဝင်တဲ့ framework တစ်ခုဖြစ်ပါတယ်။ သူကလည်း structured logging ကို support လုပ်ပါတယ်။ Log တွေကို သိမ်းဆည်းဖို့အတွက် **Targets** တွေ (Serilog ရဲ့ Sinks နဲ့တူတူပါပဲ) အများအပြားရှိပြီး လူသုံးများပါတယ်။ Configuration ကို XML file ဒါမှမဟုတ် C# code ကနေ ပြုလုပ်နိုင်ပါတယ်။

### 3. log4net

log4net ကတော့ Java ရဲ့ နာမည်ကြီး log4j ကနေ ဆင်းသက်လာတဲ့၊ .NET လောကမှာ သက်တမ်းအရှည်ဆုံး logging framework တွေထဲက တစ်ခုဖြစ်ပါတယ်။ သူ့ကို အဓိကအားဖြင့် XML file ကနေတစ်ဆင့် configure လုပ်ရပါတယ်။ အခုနောက်ပိုင်း framework တွေလောက် feature အသစ်အဆန်းတွေ မပါဝင်ပေမဲ့၊ ယုံကြည်စိတ်ချရတဲ့အတွက် project အဟောင်းတွေနဲ့ enterprise application တွေမှာ အသုံးပြုနေကြဆဲ ဖြစ်ပါတယ်။