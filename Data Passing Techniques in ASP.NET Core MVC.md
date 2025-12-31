# Data Passing Techniques in ASP.NET Core MVC

### ASP.NET Core MVC မှာ Data တွေဘယ်လိုသယ်မလဲ?

ကျွန်တော်တို့ရဲ့ ASP.NET Core MVC application တွေမှာ Controller ကနေ View ကို (ဒါမှမဟုတ်) request တစ်ခုကနေ နောက်တစ်ခုကို Data တွေ သယ်သွားဖို့အတွက် နည်းလမ်းအမျိုးမျိုး ရှိပါတယ်။ ဒီထဲက အသုံးအများဆုံးဖြစ်တဲ့ ViewData, ViewBag, TempData နဲ့ Session တို့ရဲ့ ကွာခြားချက်တွေနဲ့ အသုံးပြုပုံတွေကို လေ့လာကြည့်လိုက်ရအောင်ပါ။

### **1. ViewData**

### **ViewData ဆိုတာဘာလဲ?**

ViewData ဆိုတာက Controller ကနေ View ကို Data အနည်းငယ်ပို့ချင်တဲ့အခါ သုံးတဲ့ Dictionary object (Key-Value pair) တစ်ခု ဖြစ်ပါတယ်ခင်ဗျာ။

### **Key Features**

- **Object Type:** `ViewDataDictionary` အမျိုးအစားဖြစ်ပါတယ်။
- **Lifetime:** Request တစ်ခုစာ (Current Request) ပဲ သက်တမ်းရှိပါတယ်။ Redirect လုပ်လိုက်ရင် သူ့ထဲက Data ပျက်သွားမှာဖြစ်ပါတယ်။
- **Type Casting:** View ဘက်မှာ Data ကိုပြန်သုံးတဲ့အခါမှာ မူလ Data အမျိုးအစားကို Type Casting (ဥပမာ `as string`) ပြန်လုပ်ပေးဖို့ လိုအပ်ပါတယ်။

### **ဘယ်လိုအသုံးပြုမလဲ?**

ကျွန်တော်တို့ရဲ့ Controller နဲ့ View မှာ ဘယ်လိုအသုံးပြုလဲဆိုတာကို ရေးပြပါမယ်။

**ကျွန်တော်တို့ရဲ့ Controller (HomeController.cs)**

```csharp
public class HomeController : Controller
{
    public IActionResult Index()
    {
        // ViewData ထဲကို Key-Value pair အနေနဲ့ Data ထည့်ပါမယ်။
        ViewData["PageTitle"] = "Our Products";
        ViewData["AvailableProducts"] = new List<string> { "Laptop", "Mouse", "Keyboard" };

        return View();
    }
}
```

**ကျွန်တော်တို့ရဲ့ View (Index.cshtml)**

```csharp
@{
    // ViewData ကနေ Data ကိုပြန်ယူပြီး မူလအမျိုးအစားကို Type Casting လုပ်ပါမယ်။
    var title = ViewData["PageTitle"] as string;
    var products = ViewData["AvailableProducts"] as List<string>;
}

<h2>@title</h2>
<ul>
    @foreach (var product in products)
    {
        <li>@product</li>
    }
</ul>
```

**Note:** View မှာ Data ကို ပြန်သုံးတဲ့အခါ မူလ Data type ကို မှန်မှန်ကန်ကန် Type Casting လုပ်ပေးဖို့ လိုအပ်ပါတယ်။

### **2. ViewBag**

### **ViewBag ဆိုတာဘာလဲ?**

ViewBag ဆိုတာက ViewData ရဲ့ Dynamic wrapper တစ်ခုဖြစ်ပြီး Controller ကနေ View ကို Data ပို့ဖို့သုံးတာပါပဲ။ သူက C# 4.0 မှာပါတဲ့ `dynamic` feature ကို အသုံးချထားတာပါ။

### **Key Features**

- **Object Type:** `dynamic` အမျိုးအစားဖြစ်ပါတယ်။
- **Lifetime:** ViewData လိုပဲ Request တစ်ခုစာပဲ သက်တမ်းရှိပါတယ်။ Redirect လုပ်ရင် Data တွေ ပျက်သွားမှာပါ။
- **Type Casting:** Dynamic property ဖြစ်တဲ့အတွက် View ဘက်မှာ Type Casting လုပ်ပေးစရာမလိုဘဲ တိုက်ရိုက်ခေါ်သုံးနိုင်တာက သူ့ရဲ့ အားသာချက်ပါပဲ။

### **ဘယ်လိုအသုံးပြုမလဲ?**

Controller နဲ့ View မှာ ဘယ်လိုအသုံးပြုလဲဆိုတာကို ရေးပြပါမယ်။

**ကျွန်တော်တို့ရဲ့ Controller (HomeController.cs)**

```csharp
public class HomeController : Controller
{
    public IActionResult Index()
    {
        // ViewBag ထဲကို property အနေနဲ့ Data တိုက်ရိုက်ထည့်ပါမယ်။
        ViewBag.PageTitle = "Our Products";
        ViewBag.AvailableProducts = new List<string> { "Laptop", "Mouse", "Keyboard" };

        return View();
    }
}
```

**ကျွန်တော်တို့ရဲ့ View (Index.cshtml)**

```csharp
<h2>@ViewBag.PageTitle</h2>
<ul>
    @foreach (var product in ViewBag.AvailableProducts)
    {
        <li>@product</li>
    }
</ul>
```

**Note:** ViewBag နဲ့ ViewData ဟာ နောက်ကွယ်မှာ collection တစ်ခုတည်းကို သုံးတာဖြစ်လို့ performance အရ ကွာခြားမှုမရှိပါဘူး။ Code readability အရ ကိုယ်ကြိုက်နှစ်သက်ရာကို ရွေးချယ်သုံးစွဲနိုင်ပါတယ်။

### **3. TempData**

### **TempData ဆိုတာဘာလဲ?**

TempData ဆိုတာက Request တစ်ခုကနေ နောက်ထပ် Request တစ်ခုကို Data သယ်ချင်တဲ့အခါ (ဥပမာ- Redirect လုပ်တဲ့အခါ) သုံးပါတယ်။ သူက Data တွေကို Session ဒါမှမဟုတ် Cookie မှာ ခဏသိမ်းထားပေးတာပါ။

### **Key Features**

- **Object Type:** `ITempDataDictionary` အမျိုးအစားဖြစ်ပါတယ်။
- **Lifetime:** လက်ရှိ request နဲ့ နောက်လာမယ့် request တစ်ခု (one subsequent request) အထိပဲ သက်တမ်းရှိပါတယ်။ Data ကို ဖတ်ပြီးတာနဲ့ အလိုအလျောက် ပျက်သွားပါတယ်။
- **Type Casting:** ViewData လိုပဲ View ဘက်မှာ Type Casting ပြန်လုပ်ပေးဖို့ လိုအပ်ပါတယ်။
- **Use Case:** Form submit လုပ်ပြီးတဲ့အခါ "Successfully created!" ဆိုတဲ့ message မျိုးကို နောက် page တစ်ခုမှာပြချင်ရင် အရမ်းအသုံးဝင်ပါတယ်။

### **ဘယ်လိုအသုံးပြုမလဲ?**

Product အသစ်တစ်ခု ဆောက်ပြီး redirect လုပ်တဲ့ ဥပမာကို ရေးပြပါမယ်။

**ကျွန်တော်တို့ရဲ့ Controller (ProductController.cs)**

```csharp
public class ProductController : Controller
{
    [HttpPost]
    public IActionResult Create(Product product)
    {
        // ဒီနေရာမှာ Product ကို Database ထဲ သိမ်းတဲ့ code တွေရှိပါမယ်။
        // ပြီးရင် TempData ထဲကို အောင်မြင်ကြောင်း message ထည့်ပါမယ်။
        TempData["StatusMessage"] = "New product created successfully!";

        // Index action ကို Redirect လုပ်လိုက်ပါမယ်။
        return RedirectToAction("Index");
    }

    public IActionResult Index()
    {
        return View();
    }
}
```

**ကျွန်တော်တို့ရဲ့ View (Index.cshtml)**

```csharp
@if(TempData["StatusMessage"] != null)
{
    <div class="alert alert-success">
        @TempData["StatusMessage"]
    </div>
}

<h3>Product List</h3>
```

**Note:** TempData ကို ဖတ်ပြီးနောက်မှာလည်း ဆက်ပြီးသိမ်းထားချင်တယ်ဆိုရင် `.Keep()` ဒါမှမဟုတ် `.Peek()` method တွေကို သုံးနိုင်ပါတယ်။

### **4. Session**

### **Session ဆိုတာဘာလဲ?**

Session ဆိုတာက User တစ်ယောက်အတွက် သီးသန့် Data တွေကို အချိန်အကြာကြီး (ဥပမာ- user logout မလုပ်မချင်း) သိမ်းထားချင်တဲ့အခါ သုံးပါတယ်။ Data တွေကို Server ပေါ်မှာ သိမ်းဆည်းထားတာပါ။

### **Key Features**

- **Lifetime:** User တစ်ယောက်ရဲ့ session တစ်ခုလုံး (browser မပိတ်မချင်း သို့မဟုတ် session timeout မဖြစ်မချင်း) သက်တမ်းရှိပါတယ်။
- **Data Storage:** Data တွေကို Server memory မှာ (default အနေနဲ့) သိမ်းတာဖြစ်လို့ Data အများကြီးထည့်ရင် Server performance ကို ထိခိုက်နိုင်ပါတယ်။
- **Use Case:** Shopping cart, login ဝင်ထားတဲ့ user ရဲ့ information တွေလိုမျိုး request တိုင်းမှာ လိုအပ်နိုင်တဲ့ Data တွေကို သိမ်းဖို့ အကောင်းဆုံးဖြစ်ပါတယ်။

### **Setting Up Session in Our Project**

ASP.NET Core မှာ Session ကို အသုံးမပြုခင်မှာ service container မှာ register အရင်လုပ်ပေးပြီး middleware pipeline မှာ ထည့်ပေးဖို့ လိုအပ်ပါတယ်။

`Program.cs` file မှာ အောက်ပါအတိုင်း ပြင်ဆင်ပုံကို ရေးပြပါမယ်။

```csharp
var builder = WebApplication.CreateBuilder(args);

// 1. Add services to the container.
builder.Services.AddControllersWithViews();

// Session အတွက် လိုအပ်တဲ့ service တွေကို register လုပ်ပါမယ်။
builder.Services.AddDistributedMemoryCache(); // Session data တွေကို memory မှာသိမ်းဖို့ပါ။

builder.Services.AddSession(options =>
{
    // Session ရဲ့ timeout အချိန်ကို သတ်မှတ်နိုင်ပါတယ်။
    options.IdleTimeout = TimeSpan.FromMinutes(20);
    options.Cookie.HttpOnly = true;
    options.Cookie.IsEssential = true;
});

var app = builder.Build();

// Configure the HTTP request pipeline.
if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Home/Error");
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();

app.UseRouting();

app.UseAuthorization();

// 2. Session middleware ကို request pipeline မှာ ထည့်ပေးဖို့ လိုအပ်ပါတယ်။
// Note: UseSession() ကို UseRouting() နဲ့ UseEndpoints() (or MapControllerRoute) ကြားမှာ ခေါ်သင့်ပါတယ်။
app.UseSession();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

app.Run();
```

**Note:** `AddDistributedMemoryCache()` က Session Data တွေကို web server ရဲ့ memory ထဲမှာ သိမ်းဆည်းဖို့ဖြစ်ပါတယ်။ နောက်ပိုင်းတွေမှာ Redis လိုမျိုး distributed cache တွေနဲ့လည်း ပြောင်းသုံးနိုင်ပါတယ်။

### **ဘယ်လိုအသုံးပြုမလဲ?**

Shopping cart ထဲ ပစ္စည်းထည့်တဲ့ ဥပမာကို ရေးပြပါမယ်။

```csharp
public class CartController : Controller
{
    public IActionResult AddToCart(int productId)
    {
        // ဒီနေရာမှာ productId နဲ့ ပတ်သက်တဲ့ product information တွေကို Database ကနေ ယူပါမယ်။

        // Session ထဲမှာ "Cart" ဆိုတဲ့ key နဲ့ သိမ်းပါမယ်။
        HttpContext.Session.SetString("CartItem", "Product Name");
        HttpContext.Session.SetInt32("CartItemCount", 1);

        return RedirectToAction("Index", "Product");
    }
}
```

**Layout Page မှာ Session Data ကို ပြန်သုံးပုံ (`_Layout.cshtml`)**

```csharp
@using Microsoft.AspNetCore.Http

<p>
    Items in cart: @Context.Session.GetInt32("CartItemCount")
</p>
```

### **Quick Comparison Summary**

| Feature | ViewData | ViewBag | TempData | Session |
| --- | --- | --- | --- | --- |
| **Object Type** | `ViewDataDictionary` | `dynamic` | `ITempDataDictionary` | `ISession` |
| **Lifetime** | Current Request | Current Request | Current & Next Request | Entire Session |
| **Type Casting** | လိုအပ်ပါတယ် | မလိုအပ်ပါ | လိုအပ်ပါတယ် | လိုအပ်ပါတယ် |
| **Redirect** | Data ပျက်သွားပါတယ် | Data ပျက်သွားပါတယ် | Data မပျက်ပါဘူး | Data မပျက်ပါဘူး |
| **Best For** | Controller to View (Simple Data) | Controller to View (Simple Data) | Redirect Scenarios (Status Messages) | User-specific Data (Login, Cart) |