# ApexCharts & HighCharts in ASP.NET Core MVC

### What is the Northwind Database?

**Northwind Database** ဆိုတာ Microsoft က ဖန်တီးခဲ့တဲ့ classic sample Database တစ်ခုဖြစ်ပါတယ်။ သူက "Northwind Traders" လို့ခေါ်တဲ့ company တစ်ခုရဲ့ အရောင်းအဝယ် Data တွေကို ကိုယ်စားပြုပါတယ်။ Customer၊ Order နဲ့ Product table တွေပါဝင်တဲ့အတွက် အရောင်းရဆုံး product တွေကို ရှာဖွေခြင်းလိုမျိုး လက်တွေ့ကျတဲ့ query တွေရေးဖို့ လေ့ကျင့်နိုင်တာကြောင့် Tutorial တွေအတွက် အလွန်အသုံးဝင်ပါတယ်။

---

### Step 1: Database Setup

ပထမဆုံးအနေနဲ့ ကျွန်တော်တို့ရဲ့ SQL Server မှာ Northwind Database ရှိဖို့ လိုအပ်ပါတယ်။

1. **Download** the `instnwnd.sql` script from [Microsoft's GitHub repository](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases/northwind-pubs).
2. SQL Server Management Studio (SSMS) မှာ အဲ့ဒီ script ကို **Execute** လုပ်ပြီး `Northwind` Database ကို ဆောက်ပါမယ်။

---

### Step 2: Project and Solution Setup

အခု Visual Studio မှာ Solution နဲ့ Project တွေကို ဆောက်ပါမယ်။

1. **Create a Blank Solution:** `NorthwindCharts` ဆိုတဲ့နာမည်နဲ့ `Blank Solution` တစ်ခု ဆောက်ပါမယ်။
2. **Create the Database Project:** `NorthwindCharts.Database` ဆိုတဲ့ နာမည်နဲ့ `Class Library` project တစ်ခု ဆောက်ပါမယ်။
3. **Create the MVC Project:** `NorthwindCharts.Mvc` ဆိုတဲ့ နာမည်နဲ့ `ASP.NET Core Web App (Model-View-Controller)` project တစ်ခု ဆောက်ပါမယ်။

---

### Step 3: EF Core Scaffolding

ကျွန်တော်တို့ `dotnet ef` CLI command ကိုသုံးပြီး Database ကနေ C# class တွေကို generate လုပ်ပါမယ်။

1. **Install NuGet Packages:** `NorthwindCharts.Database` project မှာ အောက်က package တွေကို Install လုပ်ပါ။
    - `Microsoft.EntityFrameworkCore.SqlServer`
    - `Microsoft.EntityFrameworkCore.Tools`
    - `Microsoft.EntityFrameworkCore.Design`
2. **Run `dotnet ef` CLI Command:** `dotnet-ef` tool ကို install လုပ်ပြီးသားဖြစ်ဖို့ လိုအပ်ပါတယ်။ ကျွန်တော်တို့ရဲ့ Solution Folder ထဲကို terminal ကနေ သွားပြီး အောက်က command ကို run ပါမယ်။
    
    ```bash
    dotnet ef dbcontext scaffold "Server=.;Database=Northwind;User ID=sa;Password=sasa@123;TrustServerCertificate=True;" Microsoft.EntityFrameworkCore.SqlServer -o AppDbContextModels -c AppDbContext -f
    ```
    

---

### Step 4: Dependency Injection

ကျွန်တော်တို့ရဲ့ `DbContext` ကို MVC project ကနေ အလွယ်တကူ ခေါ်သုံးလို့ရအောင် `Program.cs` မှာ service တစ်ခုအနေနဲ့ မှတ်ပုံတင် (Register) လုပ်ပါမယ်။

1. **Add Project Reference:** `NorthwindCharts.Mvc` project ကနေ `NorthwindCharts.Database` project ကို reference လုပ်ပါ။
2. **Register DbContext & Add Connection String:** `NorthwindCharts.Mvc` project ရဲ့ `Program.cs` file မှာ `DbContext` ကို register လုပ်ပြီး `appsettings.json` မှာ Connection String ကို ထည့်ပေးပါ။
    - **In `Program.cs`:**
        
        ```csharp
        using Microsoft.EntityFrameworkCore;
        using NorthwindCharts.Database.Models;
        
        var builder = WebApplication.CreateBuilder(args);
        
        // Add services to the container.
        builder.Services.AddControllersWithViews();
        
        builder.Services.AddDbContext<AppDbContext>(options =>
        {
            string connectionString = builder.Configuration.GetConnectionString("DbConnection");
            options.UseSqlServer(connectionString);
        });
        
        var app = builder.Build();
        
        // Configure the HTTP request pipeline.
        if (!app.Environment.IsDevelopment())
        {
            app.UseExceptionHandler("/Home/Error");
            // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
            app.UseHsts();
        }
        
        app.UseHttpsRedirection();
        app.UseStaticFiles();
        
        app.UseRouting();
        
        app.UseAuthorization();
        
        app.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
        
        app.Run();
        ```
        
    - **In `appsettings.json`:**
        
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
            "DbConnection": "Server=.;Database=Northwind;User ID=sa;Password=sasa@123;TrustServerCertificate=True;"
          }
        }
        ```
        

---

### Step 5: Understanding Charts and DTOs

Code မရေးခင်မှာ chart တွေ ဘယ်လိုအလုပ်လုပ်လဲဆိုတာကို ရိုးရှင်းတဲ့ ဥပမာလေးနဲ့ လေ့လာပြီး၊ ကျွန်တော်တို့ရဲ့ Data ကိုင်တွယ်ပုံကို ပိုကောင်းအောင်လုပ်ဖို့ DTO ကို အသုံးပြုပါမယ်။

### How Charts Work with Data

ApexCharts လို charting library တွေက ရှုပ်ထွေးတဲ့ Data တွေကို နားမလည်ပါဘူး။ သူတို့က သတ်မှတ်ထားတဲ့ ရိုးရှင်းတဲ့ format နဲ့ပဲ Data ကို လက်ခံပါတယ်။ Pie chart တစ်ခုအတွက် အဓိကအားဖြင့် အရာနှစ်ခု လိုအပ်ပါတယ်-

1. **Labels Array:** Chart အစိတ်ပိုင်းလေးတွေရဲ့ နာမည်တွေ (ဥပမာ- product အမည်များ)။
2. **Series Array:** အစိတ်ပိုင်းတစ်ခုချင်းစီရဲ့ တန်ဖိုးကိန်းဂဏန်းများ (ဥပမာ- ရောင်းရအရေအတွက်)။

အောက်မှာ static "dummy" data ကိုသုံးထားတဲ့ ဥပမာကို ရေးပြပါမယ်။

```jsx
// Dummy data
var labels = ['Product A', 'Product B', 'Product C'];
var series = [150, 200, 120];

var options = {
    chart: { type: 'pie' },
    labels: labels, // ဒီနေရာမှာ ကျွန်တော်တို့ရဲ့ product နာမည်တွေ လာပါမယ်။
    series: series    // ဒီနေရာမှာ ကျွန်တော်တို့ရဲ့ အရောင်းအရေအတွက်တွေ လာပါမယ်။
};

var chart = new ApexCharts(document.querySelector("#chart"), options);
chart.render();
```

**ကျွန်တော်တို့ရဲ့ ရည်ရွယ်ချက်ကတော့** ဒီ static `labels` နဲ့ `series` array တွေကို ကျွန်တော်တို့ရဲ့ Database ကနေရလာတဲ့ dynamic Data နဲ့ အစားထိုးဖို့ပါပဲ။

### Using a DTO (Data Transfer Object)

ကျွန်တော်တို့ရဲ့ database query ကနေ ရှုပ်ထွေးတဲ့ object တွေပြန်မလာစေဘဲ၊ chart အတွက်လိုအပ်တဲ့ Data တွေကိုပဲ သီးသန့်သယ်ဆောင်ပေးမယ့် class လေးတစ်ခုကို ဆောက်ပါမယ်။ ဒါကို **Data Transfer Object (DTO)** လို့ခေါ်ပါတယ်။ ဒါက ကျွန်တော်တို့ရဲ့ code ကို ပိုပြီးရှင်းလင်းစေပြီး နားလည်ရလွယ်ကူစေပါတယ်။

1. `NorthwindCharts.Mvc` project မှာ `Dtos` ဆိုတဲ့ Folder အသစ်တစ်ခု ဆောက်ပါ။
2. အဲ့ဒီ Folder ထဲမှာ `BestSellingProductDto.cs` ဆိုတဲ့ class file အသစ်ဆောက်ပြီး အောက်က code ကို ရေးပြပါမယ်။
    
    ```jsx
    namespace NorthwindCharts.Dtos
    {
        public class BestSellingProductDto
        {
            public string ProductName { get; set; }
            public int TotalQuantity { get; set; }
        }
    }
    ```
    

---

### Step 6: Create the Dashboard Controller (with Raw SQL)

အခု ကျွန်တော်တို့ Controller ကို ဆောက်ပါမယ်။ LINQ အစား ပိုရှင်းလင်းပြီး performance ပိုကောင်းနိုင်တဲ့ raw SQL query ကို `FromSqlRaw` နဲ့ အသုံးပြုသွားပါမယ်။

### Tables We Will Use

ကျွန်တော်တို့ရဲ့ Data ကိုရဖို့အတွက် table နှစ်ခုကို အသုံးပြုဖို့ လိုအပ်ပါတယ်-

1. `[Order Details]`: ဒီ table က order တစ်ခုချင်းစီမှာ ရောင်းချခဲ့တဲ့ product တွေရဲ့ **`Quantity`** (အရေအတွက်) ပါဝင်တဲ့အတွက် အလွန်အရေးကြီးပါတယ်။
2. `Products`: ဒီ table မှာတော့ `[Order Details]` table ထဲက `ProductID` နဲ့ ချိတ်ဆက်ပြီး Product ရဲ့ နာမည် **`ProductName`** ကို ရယူဖို့ သုံးပါမယ်။

ကျွန်တော်တို့က ဒီ table နှစ်ခုကို **JOIN** လုပ်ပြီး product နာမည်နဲ့ ရောင်းရအရေအတွက်ကို ချိတ်ဆက်ပေးမှာ ဖြစ်ပါတယ်။

### Controller Code

`Controllers` Folder ထဲမှာ `DashboardController.cs` ကို ဆောက်ပြီး အောက်က code ကို ရေးပြပါမယ်။

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using NorthwindCharts.Mvc.Dtos; // ကျွန်တော်တို့ရဲ့ DTO ကို import လုပ်ပါမယ်။
using NorthwindCharts.Database.Models;

namespace NorthwindCharts.Mvc.Controllers
{
    public class DashboardController : Controller
    {
        private readonly AppDbContext _db;

        public DashboardController(AppDbContext db)
        {
            _db = db;
        }

        public IActionResult Index()
        {
            return View();
        }

        [HttpGet]
        public async Task<IActionResult> GetBestSellingProducts()
        {
            // ရောင်းရအရေအတွက်အလိုက် အရောင်းရဆုံး product ၅ ခုကို ဆွဲထုတ်မယ့် Raw SQL Query ပါ။
            var sqlQuery = @"
                SELECT TOP 5
                    P.ProductName,
                    SUM(OD.Quantity) AS TotalQuantity
                FROM
                    [Order Details] OD
                JOIN
                    Products P ON OD.ProductID = P.ProductID
                GROUP BY
                    P.ProductName
                ORDER BY
                    TotalQuantity DESC";
            
            // FromSqlRaw ကိုသုံးပြီး Query ကရလာတဲ့ Data တွေကို ကျွန်တော်တို့ရဲ့ DTO ထဲကို ထည့်ပါမယ်။
            var topProducts = await _db.Database
                                       .SqlQueryRaw<BestSellingProductDto>(sqlQuery)
                                       .ToListAsync();

            return Json(topProducts);
        }
    }
}
```

---

### Step 7: Update the Layout File

ပိုပြီး စနစ်တကျရှိစေဖို့ chart library JavaScript file တွေကို main layout file မှာ ထည့်သွင်းပါမယ်။ ဒါမှ ဒီ library တွေကို လိုအပ်တဲ့ page တိုင်းက အလွယ်တကူ သုံးနိုင်မှာ ဖြစ်ပါတယ်။

`Views/Shared/_Layout.cshtml` ကိုဖွင့်ပြီး `@await RenderSectionAsync("scripts", required: false)` ဆိုတဲ့ line ရဲ့ **အပေါ်မှာ** `<script>` tag နှစ်ခုကို ထပ်ထည့်ပေးပါ။

```csharp
<!DOCTYPE **html**>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"] - NorthwindCharts.Mvc</title>
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="~/css/site.css" asp-append-version="true" />
    <link rel="stylesheet" href="~/NorthwindCharts.Mvc.styles.css" asp-append-version="true" />
</head>
<body>
    <header>
        <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
            <div class="container-fluid">
                <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">NorthwindCharts.Mvc</a>
                <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target=".navbar-collapse" aria-controls="navbarSupportedContent"
                        aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
                <div class="navbar-collapse collapse d-sm-inline-flex justify-content-between">
                    <ul class="navbar-nav flex-grow-1">
                        <li class="nav-item">
                            <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                        </li>
                    </ul>
                </div>
            </div>
        </nav>
    </header>
    <div class="container">
        <main role="main" class="pb-3">
            @RenderBody()
        </main>
    </div>

    <footer class="border-top footer text-muted">
        <div class="container">
            &copy; 2025 - NorthwindCharts.Mvc - <a asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
        </div>
    </footer>
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    
    <script src="https://cdn.jsdelivr.net/npm/apexcharts"></script>
    <script src="https://code.highcharts.com/highcharts.js"></script>
    
    <script src="https://code.highcharts.com/modules/exporting.js"></script>
    <script src="https://code.highcharts.com/modules/accessibility.js"></script>

    <script src="~/js/site.js" asp-append-version="true"></script>
    
    @await RenderSectionAsync("scripts", required: false)
</body>
</html>
```

---

### Step 8: Create the Dashboard View

နောက်ဆုံးအနေနဲ့ chart တွေကိုပြမယ့် view ကို ဆောက်ပါမယ်။ ဒီနေရာမှာ `@section scripts` block အကြောင်းကိုလည်း ရှင်းပြသွားပါမယ်။

### What is `@section scripts`?

`@section scripts` ဆိုတာ Razor view (ဥပမာ `Index.cshtml`) မှာသုံးတဲ့ အထူး block တစ်ခုဖြစ်ပါတယ်။ ဒီ block ထဲမှာ ထည့်သွင်းလိုက်တဲ့ HTML ဒါမှမဟုတ် JavaScript code တွေက `_Layout.cshtml` file ထဲက `@RenderSection("scripts")` လို့ခေါ်ထားတဲ့ နေရာမှာ အလိုအလျောက် သွားရောက်ပေါ်နေမှာ ဖြစ်ပါတယ်။ ဒါက page တစ်ခုချင်းစီအတွက် သီးသန့် script တွေထည့်သွင်းတဲ့ standard နည်းလမ်းဖြစ်ပြီး၊ jQuery လို main library တွေ load ဖြစ်ပြီးမှသာ page-specific script တွေ load ဖြစ်တာကို သေချာစေပါတယ်။

ပြီးရင် `Views/Dashboard/Index.cshtml` ကို အောက်က code နဲ့ ဆောက်ပါမယ်။

```csharp
@{
    ViewData["Title"] = "Sales Dashboard";
}

<h1 class="mb-4">Sales Dashboard</h1>

<div class="row">
    <div class="col-md-6">
        <div class="card">
            <div class="card-header">
                Top 5 Best-Selling Products (ApexCharts)
            </div>
            <div class="card-body">
                <div id="apexPieChart"></div>
            </div>
        </div>
    </div>
    <div class="col-md-6">
        <div class="card">
            <div class="card-header">
                Top 5 Best-Selling Products (HighCharts)
            </div>
            <div class="card-body">
                <figure class="highcharts-figure">
                    <div id="highchartsPieChart"></div>
                    <p class="highcharts-description" style="text-align: center; font-size: 0.8em; margin-top: 1em;">
                        This chart shows a comparison of the top 5 products based on quantity sold.
                    </p>
                </figure>
            </div>
        </div>
    </div>
</div>

@section scripts {
<script>
    $(document).ready(function () {
        // ကျွန်တော်တို့ရဲ့ controller endpoint ကနေ Data ကို သွားယူပါမယ်။
        $.ajax({
            url: "/dashboard/getbestsellingproducts",
            type: "get",
            success: function (data) {
                renderApexChart(data);
                renderHighCharts(data);
            },
            error: function (xhr) {
                console.error("failed to load chart data:", xhr.responseText);
            }
        });
    });

    function renderApexChart(data) {
        // Data ကို ApexCharts format ဖြစ်အောင် ပြောင်းပါမယ်။
        const chartLabels = data.map(item => item.productName);
        const chartSeries = data.map(item => item.totalQuantity);

        const options = {
          series: chartSeries,
          chart: {
            width: 380,
            type: 'pie',
          },
          labels: chartLabels,
          responsive: [{
            breakpoint: 480,
            options: {
              chart: {
                width: 200
              },
              legend: {
                position: 'bottom'
              }
            }
          }]
        };

        const chart = new ApexCharts(document.querySelector("#apexPieChart"), options);
        chart.render();
    }

    function renderHighCharts(data) {
        // Data ကို HighCharts format ဖြစ်အောင် ပြောင်းပါမယ်။
        const chartSeries = data.map((item, index) => ({
            name: item.productName,
            y: item.totalQuantity,
            // Make the first slice selected and popped out
            sliced: index === 0,
            selected: index === 0
        }));

        Highcharts.chart('highchartsPieChart', {
            chart: {
                type: 'pie'
            },
            title: {
                text: 'Top 5 Best Sellers'
            },
            tooltip: {
                pointFormat: '<b>{point.y}</b> units sold'
            },
            plotOptions: {
                pie: {
                    allowPointSelect: true,
                    cursor: 'pointer',
                    dataLabels: {
                        enabled: true,
                        format: '<b>{point.name}</b>: {point.percentage:.1f} %'
                    }
                }
            },
            series: [
                {
                    name: 'Quantity',
                    colorByPoint: true,
                    data: chartSeries
                }
            ]
        });
    }
</script>
}
```

---

### Step 9: Add Navigation Link

`Views/Shared/_Layout.cshtml` ကိုဖွင့်ပြီး navigation bar မှာ အောက်က link ကို ထည့်ပေးပါ။

```csharp
<li class="nav-item">
    <a class="nav-link text-dark" asp-area="" asp-controller="Dashboard" asp-action="Index">Dashboard</a>
</li>
```

ပြီးရင်တော့ ကျွန်တော်တို့ရဲ့ project ကို Run ပြီး စမ်းသပ်ကြည့်နိုင်ပါပြီ။ Dashboard page မှာ Raw SQL query နဲ့ Database ကနေ dynamic ဆွဲထုတ်ထားတဲ့ Data တွေနဲ့ ပြသထားတဲ့ pie chart နှစ်ခုကို တွေ့မြင်ရမှာဖြစ်ပါတယ်ခင်ဗျာ။