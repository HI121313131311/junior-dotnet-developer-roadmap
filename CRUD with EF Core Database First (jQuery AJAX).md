# CRUD with EF Core Database First (jQuery AJAX)

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
CREATE TABLE [dbo].[Tbl_Blog]( [BlogId] [int] IDENTITY(1,1) NOT NULL, [BlogTitle] [varchar](255) NOT NULL, [BlogAuthor] [varchar](255) NOT NULL, [BlogContent] [varchar](max) NOT NULL, CONSTRAINT [PK_Tbl_Blog] PRIMARY KEY CLUSTERED
( [BlogId] ASC
)
)
GO
```

---

### Step 2: Project and Solution Setup

Visual Studio မှာ Solution နဲ့ Project တွေကို ဆောက်ပါမယ်။

1. **Create a Blank Solution:** `DotNetTrainingBatch0` ဆိုတဲ့နာမည်နဲ့ `Blank Solution` တစ်ခု ဆောက်ပါမယ်။
2. **Create the Database Project:** `DotNetTrainingBatch0.Database` ဆိုတဲ့ နာမည်နဲ့ `Class Library` project တစ်ခု ဆောက်ပါမယ်။
3. **Create the MVC Project:** `DotNetTrainingBatch0.Mvc` ဆိုတဲ့ နာမည်နဲ့ `ASP.NET Core Web App (Model-View-Controller)` project တစ်ခု ဆောက်ပါမယ်။

---

### Step 3: EF Core Scaffolding

ကျွန်တော်တို့ `dotnet ef` CLI command ကိုသုံးပြီး Database ကနေ C# class တွေကို generate လုပ်ပါမယ်။

1. **Install NuGet Packages:** `DotNetTrainingBatch0.Database` project မှာ အောက်က package တွေကို Install လုပ်ပါ။
    - `Microsoft.EntityFrameworkCore.SqlServer`
    - `Microsoft.EntityFrameworkCore.Tools`
2. Run dotnet ef CLI Command:Bash
    
    dotnet-ef tool ကို install လုပ်ပြီးသားဖြစ်ဖို့ လိုအပ်ပါတယ်။ Solution Folder ထဲကို terminal ကနေ သွားပြီး အောက်က command ကို run ပါမယ်။
    
    ```bash
    dotnet ef dbcontext scaffold "Server=.;Database=DotNetTrainingBatch0Db;Trusted_Connection=True;TrustServerCertificate=True;" Microsoft.EntityFrameworkCore.SqlServer --project DotNetTrainingBatch0.Database -o Models --force
    ```
    

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

### Step 5: AJAX-Powered CRUD Operations

ဒီအဆင့်မှာ Controller နဲ့ View တွေကို AJAX နဲ့ အလုပ်လုပ်အောင် ပြန်လည်ပြင်ဆင်ပါမယ်။ Controller action တွေက HTML view တွေအစား **JSON data** တွေကို အဓိက return လုပ်ပါမယ်။

### 1. Create the Blog Controller for AJAX

`BlogController.cs` ကို အောက်ပါအတိုင်း အသစ်ရေးသားပါမယ်။ Action method တွေက `JsonResult` ကို return လုပ်တာကို သတိပြုပါ။

```csharp
using DotNetTrainingBatch0.Database.Models;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

namespace DotNetTrainingBatch0.Mvc.Controllers
{
    public class BlogController : Controller
    {
        private readonly DotNetTrainingBatch0DbContext _db;

        public BlogController(DotNetTrainingBatch0DbContext db)
        {
            _db = db;
        }

        // Returns the main page view
        // Page ကိုပြသပေးမယ့် Action ပါ။
        public IActionResult Index()
        {
            return View();
        }

        // Returns blog data as JSON for AJAX call
        // AJAX ကနေ Data တောင်းရင် JSON နဲ့ ပြန်ပေးမယ့် Action ပါ။
        [HttpGet]
        public async Task<IActionResult> List()
        {
            var blogs = await _db.TblBlogs
                                 .AsNoTracking()
                                 .OrderByDescending(x => x.BlogId)
                                 .ToListAsync();
            return Json(blogs);
        }
        
        // Returns the create page view
        public IActionResult Create()
        {
            return View();
        }

        // Saves new blog data received from AJAX
        // AJAX ကနေ ပို့လိုက်တဲ့ Data ကို Database မှာ သိမ်းပါမယ်။
        [HttpPost]
        public async Task<IActionResult> Save(TblBlog blog)
        {
            await _db.TblBlogs.AddAsync(blog);
            var result = await _db.SaveChangesAsync();
            
            var response = new
            {
                IsSuccess = result > 0,
                Message = result > 0 ? "Saving Successful." : "Saving Failed."
            };
            return Json(response);
        }

        // Returns a single blog's data as JSON for populating the edit form
        // ပြင်မယ့် Blog ရဲ့ Data ကို JSON အနေနဲ့ ပြန်ပေးပါမယ်။
        [HttpGet]
        public async Task<IActionResult> Edit(int id)
        {
            var blog = await _db.TblBlogs.AsNoTracking().FirstOrDefaultAsync(x => x.BlogId == id);
            if (blog is null)
            {
                return Json(new { IsSuccess = false, Message = "Blog not found." });
            }
            return Json(blog);
        }
        
        // Returns the edit page view
        public IActionResult Edit(int id)
        {
            ViewBag.BlogId = id;
            return View();
        }

        // Updates blog data received from AJAX
        // AJAX ကနေ ပို့လိုက်တဲ့ ပြင်ဆင်ပြီးသား Data ကို သိမ်းပါမယ်။
        [HttpPost]
        public async Task<IActionResult> Update(int id, TblBlog blog)
        {
            var item = await _db.TblBlogs.FirstOrDefaultAsync(x => x.BlogId == id);
            if (item is null)
            {
                return Json(new { IsSuccess = false, Message = "Blog not found." });
            }

            item.BlogTitle = blog.BlogTitle;
            item.BlogAuthor = blog.BlogAuthor;
            item.BlogContent = blog.BlogContent;

            var result = await _db.SaveChangesAsync();
            var response = new
            {
                IsSuccess = result > 0,
                Message = result > 0 ? "Updating Successful." : "Updating Failed."
            };
            return Json(response);
        }

        // Deletes a blog based on ID from AJAX call
        // AJAX ကနေ ဖျက်ခိုင်းလိုက်တဲ့ Data ကို Database ကနေ ဖယ်ရှားပါမယ်။
        [HttpPost]
        public async Task<IActionResult> Delete(int id)
        {
            var item = await _db.TblBlogs.FirstOrDefaultAsync(x => x.BlogId == id);
            if (item is null)
            {
                return Json(new { IsSuccess = false, Message = "Blog not found." });
            }

            _db.TblBlogs.Remove(item);
            var result = await _db.SaveChangesAsync();
            
            var response = new
            {
                IsSuccess = result > 0,
                Message = result > 0 ? "Deleting Successful." : "Deleting Failed."
            };
            return Json(response);
        }
    }
}
```

### 2. Create the Index View for Listing Blogs

`Views/Blog/Index.cshtml` file ကို အောက်ပါအတိုင်း ရေးပါမယ်။ ဒီ View မှာ page load ဖြစ်တာနဲ့ `loadBlogs()` function ကို AJAX နဲ့ခေါ်ပြီး table ကိုဖြည့်ပါမယ်။

```csharp
@{
    ViewData["Title"] = "Blog List";
}

<h1>Blog List</h1>
<a href="/blog/create" class="btn btn-success mb-3">New Blog</a>

<table class="table table-striped">
    <thead>
        <tr>
            <th>ID</th>
            <th>Title</th>
            <th>Author</th>
            <th>Actions</th>
        </tr>
    </thead>
    <tbody id="blogTableBody">
        </tbody>
</table>

@section scripts {
<script>
    // This function runs when the page is ready
    $(document).ready(function () {
        loadBlogs();
    });

    // Function to load blogs from the server
    function loadBlogs() {
        $.ajax({
            url: "/Blog/List",
            type: "GET",
            success: function (response) {
                let tableRows = '';
                response.forEach(item => {
                    tableRows += `
                        <tr id="blog-row-${item.blogId}">
                            <td>${item.blogId}</td>
                            <td>${item.blogTitle}</td>
                            <td>${item.blogAuthor}</td>
                            <td>
                                <a href="/blog/editpage/${item.blogId}" class="btn btn-warning btn-sm">Edit</a>
                                <button type="button" class="btn btn-danger btn-sm" onclick="deleteBlog(${item.blogId})">Delete</button>
                            </td>
                        </tr>
                    `;
                });
                $('#blogTableBody').html(tableRows);
            },
            error: function (xhr) {
                console.error(xhr.responseText);
            }
        });
    }

    // Function to delete a blog
    function deleteBlog(id) {
        if (!confirm("Are you sure you want to delete this blog?")) {
            return;
        }

        $.ajax({
            url: `/Blog/Delete/${id}`,
            type: "POST",
            success: function (response) {
                alert(response.message);
                if (response.isSuccess) {
                    loadBlogs(); // Reload the table data
                }
            },
            error: function (xhr) {
                console.error(xhr.responseText);
            }
        });
    }
</script>
}
```

### 3. Create the View for Adding a New Blog

`Views/Blog/Create.cshtml` file ကို အောက်ပါအတိုင်း ဆောက်ပါမယ်။ Save button ကို နှိပ်ရင် AJAX call နဲ့ Data ပို့ပါမယ်။

```csharp
@{
    ViewData["Title"] = "New Blog";
}

<div class="mb-3">
    <label class="form-label">Title</label>
    <input type="text" class="form-control" id="txtTitle">
</div>
<div class="mb-3">
    <label class="form-label">Author</label>
    <input type="text" class="form-control" id="txtAuthor">
</div>
<div class="mb-3">
    <label class="form-label">Content</label>
    <textarea class="form-control" id="txtContent" rows="3"></textarea>
</div>
<a href="/blog" class="btn btn-secondary">Cancel</a>
<button type="button" class="btn btn-primary" id="btnSave">Save</button>

@section scripts {
<script>
    $('#btnSave').click(function () {
        const blogData = {
            BlogTitle: $('#txtTitle').val(),
            BlogAuthor: $('#txtAuthor').val(),
            BlogContent: $('#txtContent').val()
        };

        $.ajax({
            url: "/Blog/Save",
            type: "POST",
            data: blogData,
            success: function (response) {
                alert(response.message);
                if (response.isSuccess) {
                    window.location.href = "/blog";
                }
            },
            error: function (xhr) {
                console.error(xhr.responseText);
            }
        });
    });
</script>
}
```

### 4. Create the View for Editing a Blog

`Views/Blog/Edit.cshtml` file ကို အောက်ပါအတိုင်း ဆောက်ပါမယ်။ Page load ဖြစ်ချိန်မှာ ရှိပြီးသား Data ကို AJAX နဲ့ ဆွဲချပြီး form မှာ ဖြည့်ပါမယ်။ Update button က AJAX နဲ့ Data ပို့ပါမယ်။

```csharp
@{
    ViewData["Title"] = "Edit Blog";
}

<input type="hidden" id="hfBlogId" value="@ViewBag.BlogId" />

<div class="mb-3">
    <label class="form-label">Title</label>
    <input type="text" class="form-control" id="txtTitle">
</div>
<div class="mb-3">
    <label class="form-label">Author</label>
    <input type="text" class="form-control" id="txtAuthor">
</div>
<div class="mb-3">
    <label class="form-label">Content</label>
    <textarea class="form-control" id="txtContent" rows="3"></textarea>
</div>
<a href="/blog" class="btn btn-secondary">Cancel</a>
<button type="button" class="btn btn-primary" id="btnUpdate">Update</button>

@section scripts {
<script>
    $(document).ready(function () {
        loadBlogForEdit();
    });

    function loadBlogForEdit() {
        const blogId = $('#hfBlogId').val();
        $.ajax({
            url: `/Blog/Edit/${blogId}`,
            type: "GET",
            success: function (response) {
                $('#txtTitle').val(response.blogTitle);
                $('#txtAuthor').val(response.blogAuthor);
                $('#txtContent').val(response.blogContent);
            },
            error: function (xhr) {
                console.error(xhr.responseText);
            }
        });
    }

    $('#btnUpdate').click(function () {
        const blogId = $('#hfBlogId').val();
        const blogData = {
            BlogTitle: $('#txtTitle').val(),
            BlogAuthor: $('#txtAuthor').val(),
            BlogContent: $('#txtContent').val()
        };

        $.ajax({
            url: `/Blog/Update/${blogId}`,
            type: "POST",
            data: blogData,
            success: function (response) {
                alert(response.message);
                if (response.isSuccess) {
                    window.location.href = "/blog";
                }
            },
            error: function (xhr) {
                console.error(xhr.responseText);
            }
        });
    });
</script>
}
```

---

### Step 6: Add Navigation Link

`Views/Shared/_Layout.cshtml` file ကို ဖွင့်ပြီး Navigation Bar မှာ link ထည့်ပေးပါ။

```csharp
<li class="nav-item">
    <a class="nav-link text-dark" asp-area="" asp-controller="Blog" asp-action="Index">Blog (AJAX)</a>
</li>
```

ပြီးရင်တော့ Project ကို Run ပြီး စမ်းသပ်ကြည့်နိုင်ပါပြီ။ Blog တွေကို CRUD လုပ်တဲ့အခါ Page တစ်ခုလုံး refresh မဖြစ်ဘဲ Data တွေ ပြောင်းလဲသွားတာကို တွေ့ရမှာဖြစ်ပါတယ်။