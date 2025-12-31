# Using EF Core (DB First) in ASP.NET Core Web API

EF Core ရဲ့ Database-First ချဉ်းကပ်မှုမှာ အဓိကအကျဆုံး command ကတော့ ရှိပြီးသား Database Schema ကို C# code တွေအဖြစ် generate လုပ်ပေးတဲ့ `dotnet ef dbcontext scaffold` ဖြစ်ပါတယ်။ Database ကိုယ်တိုင်ကို တည်ဆောက်ခြင်း (CREATE DATABASE) ကတော့ များသောအားဖြင့် SQL Server Management Studio (SSMS) လို tool မျိုးနဲ့ သီးသန့်လုပ်ဆောင်ရလေ့ရှိပါတယ်။

---

### Step 1: Create the Database and Table (Manual Step)

ဒီအဆင့်ကတော့ CLI နဲ့တိုက်ရိုက်မသက်ဆိုင်ဘဲ ကြိုတင်လုပ်ဆောင်ထားရမယ့် တစ်ခုတည်းသော အပိုင်းဖြစ်ပါတယ်။ SQL Server Management Studio (SSMS) သို့မဟုတ် နှစ်သက်ရာ SQL tool ကိုသုံးပြီး Database နဲ့ Table ကို အရင်တည်ဆောက်ထားဖို့ လိုအပ်ပါတယ်။

```csharp
-- 1. Database ကို အရင်ဆောက်ပါမယ်။
CREATE DATABASE MiniPOS;
GO
-- 2. ဆောက်ထားတဲ့ Database ကို သုံးပြီး Table ဆောက်ပါမယ်။
USE MiniPOS;
GO
CREATE TABLE [dbo].[Tbl_Product](	[ProductId] [int] IDENTITY(1,1) NOT NULL,	[ProductCode] [varchar](50) NOT NULL,	[ProductName] [nvarchar](100) NOT NULL,	[Price] [decimal](18, 2) NOT NULL,	[DeleteFlag] [bit] NOT NULL CONSTRAINT [DF_Tbl_Product_DeleteFlag] DEFAULT ((0)), CONSTRAINT [PK_Tbl_Product] PRIMARY KEY CLUSTERED ([ProductId] ASC)
);
GO
```

---

### Step 2: Creating the Project Structure in Visual Studio

အခု Visual Studio ကိုအသုံးပြုပြီး ကျွန်တော်တို့ရဲ့ Solution နဲ့ Project တွေကို ဆောက်ပါမယ်။

1. **Create a Blank Solution**
    - Visual Studio ကိုဖွင့်ပြီး **Create a new project** ကိုနှိပ်ပါ။
    - Search bar မှာ **Blank Solution** လို့ရိုက်ရှာပြီး Next ကိုနှိပ်ပါ။
    - Solution name ကို `MiniPOS` လို့ပေးပြီး သိမ်းဆည်းမယ့်နေရာ (Location) ကိုရွေးကာ **Create** ကိုနှိပ်ပါ။
2. **Add the Database Class Library Project**
    - Solution Explorer မှာ ကျွန်တော်တို့ရဲ့ Solution (`Solution 'MiniPOS'`) ပေါ်မှာ Right-click နှိပ်ပြီး **Add > New Project...** ကိုရွေးပါ။
    - Search bar မှာ **Class Library** လို့ရိုက်ရှာပြီး C# template ကိုရွေးကာ Next နှိပ်ပါ။
    - Project name ကို `MiniPOS.Database` လို့ပေးပြီး **Next** နှိပ်ပါ။
    - Framework ကို (.NET 8.0 or your preferred version) ရွေးပြီး **Create** ကိုနှိပ်ပါ။
3. **Add the Web API Project**
    - Solution Explorer မှာ ကျွန်တော်တို့ရဲ့ Solution ပေါ်မှာပဲ Right-click နှိပ်ပြီး **Add > New Project...** ကိုရွေးပါ။
    - Search bar မှာ **ASP.NET Core Web API** လို့ရိုက်ရှာပြီး C# template ကိုရွေးကာ Next နှိပ်ပါ။
    - Project name ကို `MiniPOS.WebAPI` လို့ပေးပြီး **Next** နှိပ်ပါ။
    - Framework ကိုရွေးပြီး ကျန်တဲ့ settings တွေကို default အတိုင်းထားကာ **Create** ကိုနှိပ်ပါ။

ဒါဆိုရင် ကျွန်တော်တို့ရဲ့ Solution မှာ Project နှစ်ခုနဲ့ အခြေခံ Structure အဆင်သင့်ဖြစ်သွားပါပြီ။

---

### Step 3: Scaffold the Database using dotnet CLI

ဒီအဆင့်မှာတော့ ကျွန်တော်တို့ရဲ့ `MiniPOS.Database` Project ထဲကို အဆင့် ၁ မှာ ဆောက်ခဲ့တဲ့ Database ကနေ EF Core Models တွေနဲ့ DbContext ကို generate လုပ်ပါမယ်။

1. Install Required Packages
    
    MiniPOS.Database project ထဲကို လိုအပ်တဲ့ EF Core NuGet package တွေကို install လုပ်ပါမယ်။
    
    `dotnet add package Microsoft.EntityFrameworkCore`
    
    `dotnet add package Microsoft.EntityFrameworkCore.Design`
    
    `dotnet add package Microsoft.EntityFrameworkCore.SqlServer`
    
    `dotnet add package Microsoft.EntityFrameworkCore.Tools`
    
    Install လုပ်လို့ ပြီးတာနဲ့ Build အရင်လုပ်ပါ မဟုတ်ပဲ Scaffold Run လိုက်ရင် [`Microsoft.EntityFrameworkCore.Design`](http://Microsoft.EntityFrameworkCore.Design) Package မရှိဘူးဆိုတဲ့ Error ကို ဖြေရှင်းချင်လို့ပါ
    
2. Run the Scaffold 
    
    `MiniPOS.Database` project မှာ Right-click နှိပ်ပြီး Open in Terminal ကိုနှိပ်ပါ။  dotnet ef dbcontext scaffold command ကို run ပါမယ်။ ဒါဟာ Database-First ရဲ့ အဓိက command ဖြစ်ပါတယ်။
    
    ```csharp
    dotnet ef dbcontext scaffold "Server=.;Database=MiniPOS;User ID=sa;Password=sasa@123;TrustServerCertificate=True;" Microsoft.EntityFrameworkCore.SqlServer -o AppDbContextModels -c AppDbContext -f
    ```
    
    ဒီ command ကို run လိုက်တာနဲ့ `MiniPOS.Database` project ထဲမှာ `Data` Folder အသစ်တစ်ခု၊ `AppDbContext.cs` နဲ့ `TblProduct.cs` file တွေကို အလိုအလျောက် generate လုပ်ပေးသွားမှာ ဖြစ်ပါတယ်။
    

---

### Step 4: Referencing the Database Library

အခု ကျွန်တော်တို့ရဲ့ `MiniPOS.WebAPI` Project ကနေ `MiniPOS.Database` Project ကို လှမ်းအသုံးပြုနိုင်ဖို့အတွက် project reference ထည့်ပေးဖို့ လိုအပ်ပါတယ်။

- Solution Explorer မှာ `MiniPOS.WebAPI` project အောက်က **Dependencies** ပေါ်မှာ Right-click နှိပ်ပြီး **Add Project Reference...** ကိုရွေးပါ။
- ပေါ်လာတဲ့ window မှာ `MiniPOS.Database` ကို အမှန်ခြစ်ပေးပြီး **OK** ကိုနှိပ်ပါ။

---

### Step 5: Registering the DbContext in Our Web API

ကျွန်တော်တို့ရဲ့ API Project တစ်ခုလုံးမှာ `AppDbContext` ကို အလွယ်တကူ ခေါ်သုံးနိုင်အောင် (Dependency Injection) `Program.cs` file မှာ service အနေနဲ့ register လုပ်ပေးရပါမယ်။

1. Add Connection String in appsettings.json
    
    ကျွန်တော်တို့ရဲ့ MiniPOS.WebAPI Project ထဲက appsettings.json file မှာ Database Connection String ကို အခုလိုထည့်ပေးပါမယ်။
    
    ```json
    {
      "Logging": {
        "LogLevel": {
          "Default": "Information",
          "Microsoft.AspNetCore": "Warning"
        }
      },
      "AllowedHosts": "*",
      "ConnectionStrings": {
        "DbConnection": "Server=.;Database=MiniPOS;User ID=sa;Password=sasa@123;TrustServerCertificate=True;"
      }
    }
    ```
    
2. Register DbContext Service in Program.cs
    
    `MiniPOS.WebAPI` Project ရဲ့ `Program.cs` file ကိုဖွင့်ပြီး `builder.Services.AddControllers();` ဆိုတဲ့ line ရဲ့အပေါ်မှာ အောက်က code တွေကို ထပ်ထည့်ပေးပါမယ်။
    
    ```csharp
    // ... other using statements
    using Microsoft.EntityFrameworkCore;
    using MiniPOS.Database.Data; // ကျွန်တော်တို့ရဲ့ DbContext ရှိတဲ့နေရာကို using ထည့်ပေးဖို့ လိုအပ်ပါတယ်
    
    var builder = WebApplication.CreateBuilder(args);
    
    // Add services to the container.
    builder.Services.AddDbContext<AppDbContext>(options =>
    {
        options.UseSqlServer(builder.Configuration.GetConnectionString("DbConnection"));
    },
    ServiceLifetime.Transient,
    ServiceLifetime.Transient);
    
    builder.Services.AddControllers();
    // ... rest of the file
    ```
    
    **Note**
    
    `MiniPOS.WebAPI` project မှာ `Microsoft.EntityFrameworkCore.SqlServer` package ကို ထည့်ပေးဖို့ လိုအပ်ပါတယ်။ `Manage NuGet Packages...` ကနေ install လုပ်နိုင်ပါတယ်။
    

---

### Step 6: Creating the API Controller with EF Core

အားလုံးပြင်ဆင်ပြီးပြီဆိုတော့ ကျွန်တော်တို့ရဲ့ API Controller ကို EF Core သုံးပြီး ဆောက်ပါမယ်။ ဒီ Controller က `AppDbContext` ကို inject လုပ်ပြီး Database operations (CRUD) တွေကို လုပ်ဆောင်မှာဖြစ်ပါတယ်။

`MiniPOS.WebAPI/Controllers` Folder မှာ `ProductController.cs` ဆိုတဲ့ file အသစ်တစ်ခု ဆောက်ပြီး အောက်က code တွေကို ရေးပြပါမယ်။ Code line တိုင်းအတွက် ဘာလုပ်တယ်ဆိုတာကို comment မှာ ရှင်းပြထားပါတယ်။

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using MiniPOS.Database.Data; // ကျွန်တော်တို့ရဲ့ DbContext နဲ့ Model တွေရှိတဲ့ namespace
using System.Linq;
using System.Threading.Tasks;

namespace MiniPOS.WebAPI.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class ProductController : ControllerBase
    {
        private readonly AppDbContext _context;

        // Program.cs မှာ register လုပ်ထားတဲ့ AppDbContext ကို Constructor ကနေ inject လုပ်ပြီး ယူသုံးပါမယ်။
        public ProductController(AppDbContext context)
        {
            _context = context;
        }

        [HttpGet]
        public async Task<IActionResult> GetProducts()
        {
            // EF Core ကနေ Data အားလုံးကို ToListAsync() နဲ့ ဆွဲထုတ်ပြီး return ပြန်ပေးပါမယ်။
            var lst = await _context.TblProducts.Where(x => x.DeleteFlag == false).ToListAsync();
            return Ok(lst);
        }

        [HttpGet("{id}")]
        public async Task<IActionResult> GetProductById(int id)
        {
            // FirstOrDefaultAsync() method နဲ့ ID ကိုက်ညီတဲ့ Data တစ်ကြောင်းကိုပဲ ရှာပါမယ်။
            var item = await _context.TblProducts.FirstOrDefaultAsync(x => x.ProductId == id && x.DeleteFlag == false);
            if (item is null)
            {
                return NotFound("No Data Found.");
            }
            return Ok(item);
        }

        [HttpPost]
        public async Task<IActionResult> CreateProduct(TblProduct product)
        {
            // Request ကနေပါလာတဲ့ Product object ကို DbContext ထဲကိုထည့်ပါမယ်။
            await _context.TblProducts.AddAsync(product);
            // SaveChangesAsync() က Database မှာ အမှန်တကယ် သိမ်းဆည်းပေးတဲ့ command ဖြစ်ပါတယ်။
            var result = await _context.SaveChangesAsync();

            return Ok(result > 0 ? "Saving Successful." : "Saving Failed.");
        }

        [HttpPatch("{id}")]
        public async Task<IActionResult> UpdateProduct(int id, TblProduct product)
        {
            // အရင်ဆုံး ပြင်ချင်တဲ့ Data ရှိမရှိ ID နဲ့ ရှာပါမယ်။
            var item = await _context.TblProducts.FirstOrDefaultAsync(x => x.ProductId == id && x.DeleteFlag == false);
            if (item is null)
            {
                return NotFound("No Data Found.");
            }

            // ရှိတယ်ဆိုရင် Request မှာပါလာတဲ့ Data အသစ်တွေနဲ့ အစားထိုးပါမယ်။
            item.ProductCode = product.ProductCode;
            item.ProductName = product.ProductName;
            item.Price = product.Price;

            // Database မှာ ပြန်သိမ်းပါမယ်။
            var result = await _context.SaveChangesAsync();
            return Ok(result > 0 ? "Updating Successful." : "Updating Failed.");
        }

        [HttpDelete("{id}")]
        public async Task<IActionResult> DeleteProduct(int id)
        {
            // ဖျက်ချင်တဲ့ Data ကို ID နဲ့ ရှာပါမယ်။
             var item = await _context.TblProducts.FirstOrDefaultAsync(x => x.ProductId == id && x.DeleteFlag == false);
            if (item is null)
            {
                return NotFound("No Data Found.");
            }
            
            // Data ကို အမှန်တကယ်မဖျက်ဘဲ DeleteFlag ကိုပဲ true ပြောင်းပါမယ် (Soft Delete)
            item.DeleteFlag = true;
            
            // Database မှာ ပြန်သိမ်းပါမယ်။
            var result = await _context.SaveChangesAsync();
            return Ok(result > 0 ? "Deleting Successful." : "Deleting Failed.");
        }
    }
}
```

ဒီအဆင့်ထိဆိုရင်တော့ ကျွန်တော်တို့ရဲ့ EF Core Database-First application ကို Database တည်ဆောက်တာကနေစပြီး API အလုပ်လုပ်တဲ့အထိ အောင်မြင်စွာ တည်ဆောက်ပြီးဖြစ်ပါတယ်။ ဒီလိုစနစ်တကျ Layer ခွဲပြီးတည်ဆောက်ထားတာကြောင့် နောက်ပိုင်းတွေမှာ ပြုပြင်ထိန်းသိမ်းရတာ အလွန်လွယ်ကူမှာ ဖြစ်ပါတယ်။