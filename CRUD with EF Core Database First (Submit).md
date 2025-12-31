# CRUD with EF Core Database First (Submit)

### Step 1: Database and Table Setup

ပထမဆုံးအနေနဲ့ ကျွန်တော်တို့ရဲ့ application က ချိတ်ဆက် အသုံးပြုမယ့် Database နဲ့ Table ကို ဆောက်ဖို့ လိုအပ်ပါတယ်။ SQL Server Management Studio (SSMS) ကိုသုံးပြီး အောက်က query ကို run ပြီး ဆောက်လိုက်ပါမယ်။

```sql
- 1. Create the Database
CREATE DATABASE DotNetTrainingBatch0Db;
GO
-- 2. Use the new Database
USE DotNetTrainingBatch0Db;
GO
-- 3. Create the Blogs table
CREATE TABLE [dbo].[Tbl_Blog](	[BlogId] [int] IDENTITY(1,1) NOT NULL,	[BlogTitle] [varchar](255) NOT NULL,	[BlogAuthor] [varchar](255) NOT NULL,	[BlogContent] [varchar](max) NOT NULL, CONSTRAINT [PK_Tbl_Blog] PRIMARY KEY CLUSTERED
(	[BlogId] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON, OPTIMIZE_FOR_SEQUENTIAL_KEY = OFF) ON [PRIMARY]
) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
GO
```

**Note:** ဒီအဆင့်မှာ ကျွန်တော်တို့က Database မှာ table ကို အရင်ဆောက်တာဖြစ်တဲ့အတွက် ဒါကို **Database-First Approach** လို့ခေါ်တာ ဖြစ်ပါတယ်။

---

### Step 2: Project and Solution Setup

အခု Visual Studio မှာ Solution နဲ့ Project တွေကို ဆောက်ပါမယ်။

1. **Create a Blank Solution:** Visual Studio ကိုဖွင့်ပြီး `Blank Solution` တစ်ခုကို `DotNetTrainingBatch0` ဆိုတဲ့နာမည်နဲ့ ဆောက်ပါမယ်။
2. **Create the Database Project:** Solution ပေါ်မှာ Right-click နှိပ်ပြီး `Add` -> `New Project...` ကိုရွေးပါ။ `Class Library` ကိုရွေးပြီး `DotNetTrainingBatch0.Database` ဆိုတဲ့ နာမည်ပေးပါ။ ဒါက ကျွန်တော်တို့ရဲ့ Database နဲ့ သက်ဆိုင်တဲ့ C# Model တွေနဲ့ DbContext ကို ထားမယ့်နေရာပါ။
3. **Create the MVC Project:** Solution ပေါ်မှာပဲ Right-click ထပ်နှိပ်ပြီး `Add` -> `New Project...` ကိုရွေးပါ။ `ASP.NET Core Web App (Model-View-Controller)` ကိုရွေးပြီး `DotNetTrainingBatch0.Mvc` ဆိုတဲ့ နာမည်ပေးပါ။

---

### Step 3: EF Core Scaffolding

ဒီအဆင့်က Database-First ရဲ့ အဓိက အပိုင်းဖြစ်ပါတယ်။ ကျွန်တော်တို့ အပေါ်မှာဆောက်ခဲ့တဲ့ `Tbl_Blog` table ကနေ C# class တွေ (Models) နဲ့ Database ချိတ်ဆက်ဖို့ `DbContext` class ကို EF Core command သုံးပြီး အလိုအလျောက် generate လုပ်ပါမယ်။

1. **Install NuGet Packages:** `DotNetTrainingBatch0.Database` project မှာ အောက်က package တွေကို Install လုပ်ဖို့ လိုအပ်ပါတယ်။
    - `Microsoft.EntityFrameworkCore.SqlServer`
    - `Microsoft.EntityFrameworkCore.Tools`
2. Run `dotnet ef` CLI Command:
    
    `dotnet ef` command ကို အသုံးမပြုခင်မှာ `dotnet-ef` tool ကို ကိုယ့်စက်ထဲမှာ install လုပ်ထားဖို့ လိုအပ်ပါတယ်။ Command Prompt သို့မဟုတ် Terminal ကိုဖွင့်ပြီး အောက်က command ကို တစ်ကြိမ် run ပေးပါ။
    
    ```bash
    dotnet tool install --global dotnet-ef
    ```
    
    ပြီးရင်၊ ကျွန်တော်တို့ရဲ့ Solution Folder (`DotNetTrainingBatch0` folder) ထဲကို terminal ကနေ သွားပြီး အောက်က command ကို run ပါမယ်။
    
    ```bash
    dotnet ef dbcontext scaffold "Server=.;Database=DotNetTrainingBatch0Db;Trusted_Connection=True;TrustServerCertificate=True;" Microsoft.EntityFrameworkCore.SqlServer --project DotNetTrainingBatch0.Database -o Models --force
    ```
    
    **Command Breakdown:**
    
    - `dotnet ef dbcontext scaffold`: ဒါက Database ကနေ C# class တွေ generate လုပ်ဖို့အတွက်သုံးတဲ့ CLI command ပါ။
    - `"..."`: ဒါက Connection String ပါ။ ကိုယ့်စက်ရဲ့ setting အလိုက် ပြင်ဆင်ဖို့ လိုအပ်ပါတယ်။
    - `Microsoft.EntityFrameworkCore.SqlServer`: ဒါက ကျွန်တော်တို့သုံးမယ့် Database Provider ပါ။
    - `-project DotNetTrainingBatch0.Database`: ဒီ command ရဲ့ output file တွေကို `DotNetTrainingBatch0.Database` project ထဲမှာ ထည့်ဖို့ သတ်မှတ်ပေးတာပါ။
    - `o Models`: Generate လုပ်လိုက်တဲ့ file တွေကို `Models` ဆိုတဲ့ Folder အောက်မှာ ထားခိုင်းတာပါ။
    - `-force`: `Models` Folder ထဲမှာ file အဟောင်းတွေရှိနေခဲ့ရင် အသစ်နဲ့ အစားထိုး overwrite လုပ်သွားဖို့အတွက် သုံးပါတယ်။
    
    ပြီးရင် ကျွန်တော်တို့ရဲ့ `DotNetTrainingBatch0.Database` project ထဲမှာ `Models` Folder အသစ်နဲ့ အထဲမှာ `DotNetTrainingBatch0DbContext.cs` နဲ့ `TblBlog.cs` ဆိုပြီး file နှစ်ခု ရောက်လာတာကို တွေ့ရမှာပါ။
    

---

### Step 4: Dependency Injection

အခု ကျွန်တော်တို့ရဲ့ `DbContext` ကို MVC project ကနေ အလွယ်တကူ ခေါ်သုံးလို့ရအောင် `Program.cs` မှာ service တစ်ခုအနေနဲ့ မှတ်ပုံတင် (Register) လုပ်ပါမယ်။

1. **Add Project Reference:** `DotNetTrainingBatch0.Mvc` project က `DotNetTrainingBatch0.Database` project ကို သိဖို့အတွက် reference ထည့်ဖို့ လိုအပ်ပါတယ်။
2. **Register DbContext:** `DotNetTrainingBatch0.Mvc` project ရဲ့ `Program.cs` file ကိုဖွင့်ပြီး အောက်က code ကို `builder.Services.AddControllersWithViews();` ရဲ့ အပေါ်မှာ ထည့်ရေးပါမယ်။
    
    ```csharp
    // Add using statements at the top of Program.cs
    using Microsoft.EntityFrameworkCore;
    using DotNetTrainingBatch0.Database.Models;
    
    // ... other code
    
    // This line registers the DbContext
    builder.Services.AddDbContext<DotNetTrainingBatch0DbContext>(options =>
    {
        string connectionString = builder.Configuration.GetConnectionString("DbConnection");
        options.UseSqlServer(connectionString);
    },
    ServiceLifetime.Transient,
    ServiceLifetime.Transient);
    
    builder.Services.AddControllersWithViews();
    ```
    
3. **Add Connection String:** `appsettings.json` file ထဲမှာ အောက်က connection string ကို ထည့်ပေးပါ။
    
    ```json
    "ConnectionStrings": {
      "DbConnection": "Server=.;Database=DotNetTrainingBatch0Db;User ID=sa;Password=sasa@123;TrustServerCertificate=True;"
    }
    ```
    

---

### Step 5: CRUD Operations (Controller and Views)

အခု ကျွန်တော်တို့ရဲ့ Blog တွေကို စီမံခန့်ခွဲဖို့ Controller နဲ့ View တွေကို ဆောက်ပါမယ်။

1. **Create BlogController:** `DotNetTrainingBatch0.Mvc` project ရဲ့ `Controllers` Folder ပေါ်မှာ Right-click နှိပ်ပြီး `Add` -> `Controller...` ကိုရွေးပါ။ `MVC Controller - Empty` ကို ရွေးပြီး `BlogController.cs` ဆိုတဲ့ နာမည်ပေးပါ။
2. **Inject DbContext:** Controller ထဲမှာ `DbContext` ကို သုံးနိုင်ဖို့ constructor injection ကို အောက်ကလို ရေးပြပါမယ်။
    
    ```csharp
    // Add these using statements at the top
    using DotNetTrainingBatch0.Database.Models;
    using Microsoft.EntityFrameworkCore;
    
    public class BlogController : Controller
    {
        private readonly DotNetTrainingBatch0DbContext _db;
    
        public BlogController(DotNetTrainingBatch0DbContext db)
        {
            _db = db;
        }
    
        // CRUD actions will go here...
    }
    ```
    

### Read (List) Operation

Blog စာရင်းကိုပြမယ့် `Index` page ကို အရင်ဆောက်ပါမယ်။

1. **Index Action:** `BlogController` ထဲမှာ အောက်က `Index` method ကို ရေးပါမယ်။
    
    ```csharp
    public async Task<IActionResult> Index()
    {
        var blogs = await _db.TblBlogs
                             .AsNoTracking() // Improves performance for read-only queries
                             .OrderByDescending(x => x.BlogId)
                             .ToListAsync();
        return View(blogs);
    }
    ```
    
2. **Index View:** `Index` method နာမည်ပေါ်မှာ Right-click နှိပ်ပြီး `Add View...` -> `Razor View - Empty` ကို ရွေးပါ။ `Index.cshtml` ဆိုတဲ့ view file တစ်ခု ဆောက်ပြီး အောက်က code တွေကို ရေးပြပါမယ်။
    
    ```csharp
    @model List<TblBlog>
    
    <h1>Blog List</h1>
    
    <a href="/blog/create" class="btn btn-success">New Blog</a>
    
    <table class="table table-striped">
        <thead>
            <tr>
                <th>ID</th>
                <th>Title</th>
                <th>Author</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody>
            @foreach(var item in Model)
            {
                <tr>
                    <td>@item.BlogId</td>
                    <td>@item.BlogTitle</td>
                    <td>@item.BlogAuthor</td>
                    <td>
                        <a href="/blog/edit/@item.BlogId" class="btn btn-warning btn-sm">Edit</a>
                        <a href="/blog/delete/@item.BlogId" class="btn btn-danger btn-sm">Delete</a>
                    </td>
                </tr>
            }
        </tbody>
    </table>
    ```
    

### Create Operation

Blog အသစ်ဖန်တီးဖို့ Page ကို ဆောက်ပါမယ်။

1. **Create Actions:** `BlogController` ထဲမှာ အောက်က methods နှစ်ခုကို ထပ်ထည့်ပါမယ်။ တစ်ခုက form ကိုပြဖို့ (GET) နဲ့ နောက်တစ်ခုက form ကပို့လိုက်တဲ့ Data ကို သိမ်းဖို့ (POST) ပါ။
    
    ```csharp
    // Shows the create form
    [HttpGet]
    public IActionResult Create()
    {
        return View();
    }
    
    // Saves the new blog data
    [HttpPost]
    public async Task<IActionResult> Save(TblBlog blog)
    {
        await _db.TblBlogs.AddAsync(blog);
        await _db.SaveChangesAsync();
        return RedirectToAction("Index");
    }
    ```
    
2. **Create View:** `Views/Blog` Folder အောက်မှာ `Create.cshtml` ဆိုတဲ့ file အသစ်ဆောက်ပြီး အောက်က code ကို ရေးပြပါမယ်။
    
    ```csharp
    <form action="/blog/save" method="post">
        <div class="mb-3">
            <label class="form-label">Title</label>
            <input type="text" class="form-control" name="BlogTitle">
        </div>
        <div class="mb-3">
            <label class="form-label">Author</label>
            <input type="text" class="form-control" name="BlogAuthor">
        </div>
        <div class="mb-3">
            <label class="form-label">Content</label>
            <textarea class="form-control" name="BlogContent" rows="3"></textarea>
        </div>
        <button type="submit" class="btn btn-primary">Save</button>
    </form>
    ```
    

### Update Operation

ရှိပြီးသား Blog ကို ပြင်ဆင်ဖို့ Page ဆောက်ပါမယ်။

1. **Edit/Update Actions:** `BlogController` ထဲမှာ အောက်က methods နှစ်ခုကို ထပ်ထည့်ပါမယ်။ တစ်ခုက Data အဟောင်းကို form မှာပြဖို့ (GET) နဲ့ နောက်တစ်ခုက ပြင်လိုက်တဲ့ Data အသစ်ကို သိမ်းဖို့ (POST) ပါ။
    
    ```csharp
    // Shows the blog to be edited
    [HttpGet]
    public async Task<IActionResult> Edit(int id)
    {
        var blog = await _db.TblBlogs.FirstOrDefaultAsync(x => x.BlogId == id);
        if (blog is null)
        {
            return RedirectToAction("Index");
        }
        return View(blog);
    }
    
    // Updates the blog data
    [HttpPost]
    public async Task<IActionResult> Update(int id, TblBlog blog)
    {
        var item = await _db.TblBlogs.FirstOrDefaultAsync(x => x.BlogId == id);
        if (item is null)
        {
            return RedirectToAction("Index");
        }
    
        item.BlogTitle = blog.BlogTitle;
        item.BlogAuthor = blog.BlogAuthor;
        item.BlogContent = blog.BlogContent;
    
        await _db.SaveChangesAsync();
        return RedirectToAction("Index");
    }
    ```
    
2. **Edit View:** `Views/Blog` Folder အောက်မှာ `Edit.cshtml` file ဆောက်ပြီး အောက်က code ကို ရေးပြပါမယ်။
    
    ```csharp
    @model TblBlog
    
    <form action="/blog/update/@Model.BlogId" method="post">
        <div class="mb-3">
            <label class="form-label">Title</label>
            <input type="text" class="form-control" name="BlogTitle" value="@Model.BlogTitle">
        </div>
        <div class="mb-3">
            <label class="form-label">Author</label>
            <input type="text" class="form-control" name="BlogAuthor" value="@Model.BlogAuthor">
        </div>
        <div class="mb-3">
            <label class="form-label">Content</label>
            <textarea class="form-control" name="BlogContent" rows="3">@Model.BlogContent</textarea>
        </div>
        <button type="submit" class="btn btn-primary">Update</button>
    </form>
    ```
    

### Delete Operation

Blog ကို ဖျက်ဖို့အတွက် Action ဆောက်ပါမယ်။

1. **Delete Action:** `BlogController` မှာ အောက်က method ကို ရေးပါမယ်။
    
    ```csharp
    [HttpGet]
    public async Task<IActionResult> Delete(int id)
    {
        var item = await _db.TblBlogs.FirstOrDefaultAsync(x => x.BlogId == id);
        if (item is null)
        {
            return RedirectToAction("Index");
        }
    
        _db.TblBlogs.Remove(item);
        await _db.SaveChangesAsync();
    
        return RedirectToAction("Index");
    }
    ```
    

**Note:** `Index.cshtml` မှာ Delete button ကို ကျွန်တော်တို့ ထည့်ပြီးသားဖြစ်တဲ့အတွက် ဒီ action ကို တိုက်ရိုက်ခေါ်သုံးနိုင်မှာ ဖြစ်ပါတယ်။

---

### Step 6: Add Navigation Link

နောက်ဆုံးအနေနဲ့ ကျွန်တော်တို့ရဲ့ Blog page ကို အလွယ်တကူ သွားလို့ရအောင် Navigation Bar မှာ link ထည့်ပါမယ်။ `Views/Shared/_Layout.cshtml` file ကို ဖွင့်ပြီး `<ul>` tag ထဲမှာ အောက်က `<li>` element ကို ထည့်ပေးပါ။

```csharp
<li class="nav-item">
    <a class="nav-link text-dark" asp-area="" asp-controller="Blog" asp-action="Index">Blog</a>
</li>
```

ပြီးရင်တော့ Project ကို Run ပြီး စမ်းသပ်ကြည့်နိုင်ပါပြီ။ Blog CRUD အားလုံး အလုပ်လုပ်တာကို တွေ့ရမှာဖြစ်ပါတယ်။