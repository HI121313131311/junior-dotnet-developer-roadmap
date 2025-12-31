# Using Dapper in ASP.NET Core Web API

**Dapper** ဆိုတာကတော့ code နည်းနည်းနဲ့ Database နဲ့ အလွယ်တကူ ချိတ်ဆက်အလုပ်လုပ်နိုင်အောင် ကူညီပေးတဲ့ high-performance micro-ORM (Object-Relational Mapper) တစ်ခု ဖြစ်ပါတယ်။ သူ့ရဲ့ အဓိက အားသာချက်ကတော့ အလွန်မြန်ဆန်ပြီး သုံးရလွယ်ကူတာပါပဲ။ 

---

### 1. Project Setup and NuGet Package Installation

ပထမဆုံးအနေနဲ့၊ ကျွန်တော်တို့ရဲ့ ASP.NET Core Web API project မှာ Dapper ကိုသုံးပြီး SQL Server Database နဲ့ ချိတ်ဆက်ဖို့အတွက် လိုအပ်တဲ့ package တွေကို install လုပ်ဖို့ လိုအပ်ပါတယ်။

**Package Manager Console** ကိုဖွင့်ပြီး အောက်က command တွေကို run ပေးပါ။

```bash
Install-Package Dapper
Install-Package Microsoft.Data.SqlClient
```

---

### 2. Add Connection String in `appsettings.json`

ပြီးရင် ကျွန်တော်တို့ရဲ့ `appsettings.json` file မှာ Database Connection String ကို ထည့်ပေးဖို့ လိုအပ်ပါတယ်။ ဒါမှ ကျွန်တော်တို့ရဲ့ application က ဘယ် Database ကို သွားချိတ်ဆက်ရမလဲဆိုတာ သိမှာပါ။

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
    "DbConnection": "Server=.; Database=DotNetTrainingBatch2; User ID=sa; Password=sasa@123; TrustServerCertificate=True;"
  }
}
```

---

### 3. Create Models

Data တွေကို Database ကနေ လက်ခံဖို့နဲ့ User ဆီကနေ လက်ခံဖို့အတွက် Model Class တွေ ဆောက်ပါမယ်။ ဒီ class တွေက Database table column တွေနဲ့ ကိုက်ညီနေဖို့ လိုအပ်ပါတယ်။

```csharp
// Product Data ကို Database ကနေ ပြန်လာတဲ့ ပုံစံအတိုင်း လက်ခံဖို့အတွက်ပါ။
public class ProductModel
{
    public int ProductId { get; set; }
    public string ProductCode { get; set; }
    public string ProductName { get; set; }
    public decimal Price { get; set; }
    public bool DeleteFlag { get; set; }
}

// User ကနေ Product အသစ်ဆောက်တာ၊ ပြင်တာတွေအတွက် Request Body ကို လက်ခံဖို့ပါ။
public class ProductRequestModel
{
    public string ProductCode { get; set; }
    public string ProductName { get; set; }
    public decimal Price { get; set; }
}
```

---

### 4. Create API Controller with Dapper

အားလုံးပြင်ဆင်ပြီးရင် ကျွန်တော်တို့ရဲ့ အဓိကအပိုင်းဖြစ်တဲ့ API Controller ကို Dapper သုံးပြီး ဆောက်ပါမယ်။

- `Controllers` Folder မှာ `ProductController.cs` ဆိုတဲ့ file အသစ်တစ်ခု ဆောက်ပါ။
- အောက်မှာရှိတဲ့ code တွေကို copy/paste လုပ်ပြီး ထည့်လိုက်ပါ။ Code line တိုင်းအတွက် ဘာလုပ်တယ်ဆိုတာကို comment မှာ ရေးပြီး ရှင်းပြထားပါတယ်။

```csharp
using Dapper;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Data.SqlClient;
using System.Data;

namespace DotNetTrainingBatch2.DapperWebApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class ProductController : ControllerBase
    {
        private readonly IConfiguration _configuration;

        // IConfiguration ကို Inject လုပ်ပြီး Connection String ကို ယူသုံးပါမယ်။
        public ProductController(IConfiguration configuration)
        {
            _configuration = configuration;
        }

        [HttpGet]
        public IActionResult GetProducts()
        {
            // using statement က connection ကို အလိုအလျောက် ဖွင့်၊ ပိတ် လုပ်ပေးသွားပါမယ်။
            using (IDbConnection db = new SqlConnection(_configuration.GetConnectionString("DbConnection")))
            {
                // Dapper ရဲ့ Query<T> method က query ကို run ပြီး C# List<T> အဖြစ်ကိုယ့်ဘာသာ map လုပ်ပေးပါတယ်။
                var lst = db.Query<ProductModel>("select * from Tbl_Product;");
                return Ok(lst);
            }
        }

        [HttpGet("{id}")]
        public IActionResult GetProductById(int id)
        {
            using (IDbConnection db = new SqlConnection(_configuration.GetConnectionString("DbConnection")))
            {
                // QueryFirstOrDefault က record တစ်ကြောင်းပဲယူချင်တဲ့အခါသုံးပါတယ်။ Data မရှိရင် null ပြန်ပေးပါတယ်။
                // Parameter ကို anonymous object အနေနဲ့ လွယ်လွယ်ကူကူ ထည့်လို့ရပါတယ်။
                var item = db.QueryFirstOrDefault<ProductModel>("select * from Tbl_Product where ProductId = @ProductId;", new
                {
                    ProductId = id
                });
                
                if (item is null)
                {
                    return NotFound();
                }
                return Ok(item);
            }
        }

        [HttpPost]
        public IActionResult CreateProduct(ProductRequestModel requestModel)
        {
            string query = @"INSERT INTO [dbo].[Tbl_Product]
           ([ProductCode]
           ,[ProductName]
           ,[Price]
           ,[DeleteFlag])
     VALUES
           (@ProductCode
           ,@ProductName
           ,@Price
           ,0)";
            using (IDbConnection db = new SqlConnection(_configuration.GetConnectionString("DbConnection")))
            {
                // Execute method က INSERT, UPDATE, DELETE query တွေအတွက်သုံးပြီး affected rows အရေအတွက်ကို return ပြန်ပေးပါတယ်။
                int result = db.Execute(query, requestModel);
                return Ok(result > 0 ? "Saving Successful." : "Saving Failed.");
            }
        }

        [HttpPatch("{id}")]
        public IActionResult UpdateProduct(int id, ProductRequestModel requestModel)
        {
            string query = @"UPDATE [dbo].[Tbl_Product]
   SET [ProductCode] = @ProductCode
      ,[ProductName] = @ProductName
      ,[Price] = @Price
 WHERE ProductId = @ProductId";
            using (IDbConnection db = new SqlConnection(_configuration.GetConnectionString("DbConnection")))
            {
                // Anonymous object အသစ်တစ်ခုထဲမှာ requestModel နဲ့ id ကိုပေါင်းပြီး parameter အဖြစ် ထည့်ပေးလိုက်ပါတယ်။
                int result = db.Execute(query, new
                {
                    requestModel.ProductCode,
                    requestModel.ProductName,
                    requestModel.Price,
                    ProductId = id
                });
                return Ok(result > 0 ? "Updating Successful." : "Updating Failed.");
            }
        }

        [HttpDelete("{id}")]
        public IActionResult DeleteProduct(int id)
        {
            string query = @"UPDATE [dbo].[Tbl_Product]
   SET DeleteFlag = 1
 WHERE ProductId = @ProductId";
            using (IDbConnection db = new SqlConnection(_configuration.GetConnectionString("DbConnection")))
            {
                int result = db.Execute(query, new { ProductId = id });
                return Ok(result > 0 ? "Deleting Successful." : "Deleting Failed.");
            }
        }
    }
}
```

ဒါဆိုရင်တော့ ကျွန်တော်တို့ရဲ့ `DotNetTrainingBatch.DapperWebApi` project မှာ Dapper ကိုအသုံးပြုပြီး CRUD operations တွေ အားလုံးကို အောင်မြင်စွာ implement လုပ်ပြီးသွားပါပြီ။