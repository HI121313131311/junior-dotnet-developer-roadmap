# Integrating AdminLTE v3 with ASP.NET Core MVC

ကျွန်တော်တို့ရဲ့ ASP.NET Core project ကို AdminLTE v3 template နဲ့ ပေါင်းစပ်အသုံးပြုပုံကို အောက်မှာတစ်ဆင့်ချင်း ရေးပြထားပါတယ်ခင်ဗျာ။

### Step 1: Create a New Solution and Projects

ပထမဆုံးအနေနဲ့ ကျွန်တော်တို့ရဲ့ project တွေအားလုံးကို စုစည်းထားမယ့် Solution အသစ်တစ်ခုနဲ့ လိုအပ်တဲ့ project နှစ်ခုကို ဆောက်ပါမယ်။

### 1.1 Create the Main MVC Project

1. Visual Studio ကိုဖွင့်ပြီး **Create a new project** ကို ရွေးပါ။
2. **ASP.NET Core Web App (Model-View-Controller)** ကိုရွေးပြီး Next ကိုနှိပ်ပါ။
3. Project name ကို `SLHDotNetTrainingBatch0.ThemeMvcApp` လို့ပေးပါ။ Solution name ကိုတော့ `SLHDotNetTrainingBatch0.ThemeMvcApp` လို့ ပေးပြီး Next ကိုနှိပ်ပါ။
4. **Framework** ကို `.NET 8.0` ကို ရွေးပြီး Create ကိုနှိပ်လိုက်ပါ။

### 1.2 Create the Database Class Library Project

အခု ကျွန်တော်တို့ရဲ့ Database နဲ့ပတ်သက်တဲ့ code တွေ သီးသန့်ထားဖို့ Class Library project တစ်ခုကို အဲ့ဒီ Solution ထဲမှာပဲ ဆက်ပြီးဆောက်ပါမယ်။

1. Solution Explorer မှာ Solution (`SLHDotNetTrainingBatch0.ThemeMvcApp`) ပေါ်မှာ Right-click နှိပ်ပြီး `Add` > `New Project...` ကိုရွေးပါ။
2. **Class Library** ကိုရွေးပြီး Next ကိုနှိပ်ပါ။
3. Project name ကို `SLHDotNetTrainingBatch0.ThemeMvcApp.Database` လို့ပေးပြီး Next ကိုနှိပ်ပါ။
4. **Framework** ကို `.NET 8.0` ကို ရွေးပြီး Create ကိုနှိပ်လိုက်ပါ။

### Step 2: Add Project Reference

ကျွန်တော်တို့ရဲ့ main MVC project ကနေ Database class library project ကို လှမ်းအသုံးပြုနိုင်ဖို့အတွက် project reference ထည့်ဖို့လိုအပ်ပါတယ်။

1. Solution Explorer မှာ `SLHDotNetTrainingBatch0.ThemeMvcApp` project အောက်က **Dependencies** ပေါ်မှာ Right-click နှိပ်ပြီး `Add Project Reference...` ကိုရွေးပါ။
2. ပေါ်လာတဲ့ window မှာ `SLHDotNetTrainingBatch0.ThemeMvcApp.Database` ကို အမှန်ခြစ်ပေးပြီး OK ကိုနှိပ်ပါ။

### Step 3: Download and Add AdminLTE Files

ပြီးရင်တော့ ကျွန်တော်တို့ AdminLTE v3 ရဲ့ source file တွေကို download လုပ်ပြီး ကျွန်တော်တို့ရဲ့ MVC project ထဲကို ထည့်ပါမယ်။

1. [AdminLTE GitHub releases page](https://github.com/ColorlibHQ/AdminLTE/releases/download/v3.2.0/AdminLTE-3.2.0.zip) ကိုသွားပြီး zip file ကို download လုပ်ပါ။
2. Download လုပ်လို့ရလာတဲ့ zip file ကို computer ထဲမှာ extract လုပ်ထားလိုက်ပါ။
3. ကျွန်တော်တို့ရဲ့ `SLHDotNetTrainingBatch0.ThemeMvcApp` project ထဲက `wwwroot/lib/` folder အောက်မှာ `adminlte` ဆိုပြီး folder အသစ်တစ်ခု ဆောက်လိုက်ပါ။
4. Extract လုပ်ထားတဲ့ AdminLTE folder ထဲက `dist` နဲ့ `plugins` folder နှစ်ခုလုံးကို copy ကူးပြီး `wwwroot/lib/adminlte/` folder အသစ်ထဲကို ထည့်လိုက်ပါ။

### Step 4: Setup the Main UI Layout (`_Layout.cshtml`)

ဒီအဆင့်မှာ ကျွန်တော်တို့ရဲ့ `SLHDotNetTrainingBatch0.ThemeMvcApp` project ထဲက `Views/Shared/_Layout.cshtml` file ကို AdminLTE template အတိုင်းဖြစ်အောင် ပြင်ဆင်ပါမယ်။

`_Layout.cshtml` file ထဲက ရှိသမျှ code အားလုံးကို ဖျက်ပြီး အောက်က code နဲ့ အစားထိုးလိုက်ပါ။

```csharp
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>@ViewData["Title"] - Our AdminApp</title>

    <!-- Google Font: Source Sans Pro -->
    <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Source+Sans+Pro:300,400,400i,700&display=fallback">
    <!-- Font Awesome -->
    <link rel="stylesheet" href="~/lib/adminlte/plugins/fontawesome-free/css/all.min.css">
    <!-- Ionicons -->
    <link rel="stylesheet" href="https://code.ionicframework.com/ionicons/2.0.1/css/ionicons.min.css">
    <!-- Tempusdominus Bootstrap 4 -->
    <link rel="stylesheet" href="~/lib/adminlte/plugins/tempusdominus-bootstrap-4/css/tempusdominus-bootstrap-4.min.css">
    <!-- iCheck -->
    <link rel="stylesheet" href="~/lib/adminlte/plugins/icheck-bootstrap/icheck-bootstrap.min.css">
    <!-- JQVMap -->
    <link rel="stylesheet" href="~/lib/adminlte/plugins/jqvmap/jqvmap.min.css">
    <!-- Theme style -->
    <link rel="stylesheet" href="~/lib/adminlte/dist/css/adminlte.min.css">
    <!-- overlayScrollbars -->
    <link rel="stylesheet" href="~/lib/adminlte/plugins/overlayScrollbars/css/OverlayScrollbars.min.css">
    <!-- Daterange picker -->
    <link rel="stylesheet" href="~/lib/adminlte/plugins/daterangepicker/daterangepicker.css">
    <!-- summernote -->
    <link rel="stylesheet" href="~/lib/adminlte/plugins/summernote/summernote-bs4.min.css">
</head>
<body class="hold-transition sidebar-mini layout-fixed">
    <div class="wrapper">
        <!-- Preloader -->
        <div class="preloader flex-column justify-content-center align-items-center">
            <img class="animation__shake" src="~/lib/adminlte/dist/img/AdminLTELogo.png" alt="AdminLTELogo" height="60" width="60">
        </div>

        <!-- Navbar -->
        <nav class="main-header navbar navbar-expand navbar-white navbar-light">
            <!-- Left navbar links -->
            <ul class="navbar-nav">
                <li class="nav-item">
                    <a class="nav-link" data-widget="pushmenu" href="#" role="button"><i class="fas fa-bars"></i></a>
                </li>
                <li class="nav-item d-none d-sm-inline-block">
                    <a asp-controller="Home" asp-action="Index" class="nav-link">Home</a>
                </li>
            </ul>

            <!-- Right navbar links -->
            <ul class="navbar-nav ml-auto">
                <li class="nav-item">
                    <a class="nav-link" data-widget="fullscreen" href="#" role="button">
                        <i class="fas fa-expand-arrows-alt"></i>
                    </a>
                </li>
            </ul>
        </nav>
        <!-- /.navbar -->

        <!-- Main Sidebar Container -->
        <aside class="main-sidebar sidebar-dark-primary elevation-4">
            <!-- Brand Logo -->
            <a asp-controller="Home" asp-action="Index" class="brand-link">
                <img src="~/lib/adminlte/dist/img/AdminLTELogo.png" alt="AdminLTE Logo" class="brand-image img-circle elevation-3" style="opacity: .8">
                <span class="brand-text font-weight-light">Our App</span>
            </a>

            <!-- Sidebar -->
            <div class="sidebar">
                <!-- Sidebar user panel (optional) -->
                <div class="user-panel mt-3 pb-3 mb-3 d-flex">
                    <div class="image">
                        <img src="~/lib/adminlte/dist/img/user2-160x160.jpg" class="img-circle elevation-2" alt="User Image">
                    </div>
                    <div class="info">
                        <a href="#" class="d-block">User Name</a>
                    </div>
                </div>

                <!-- Sidebar Menu -->
                <nav class="mt-2">
                    <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview" role="menu" data-accordion="false">
                        <li class="nav-item">
                            <a asp-controller="Home" asp-action="Index" class="nav-link">
                                <i class="nav-icon fas fa-tachometer-alt"></i>
                                <p>Dashboard</p>
                            </a>
                        </li>
                        <li class="nav-item">
                            <a asp-controller="Student" asp-action="Index" class="nav-link">
                                <i class="nav-icon fas fa-user-graduate"></i>
                                <p>Student Management</p>
                            </a>
                        </li>
                    </ul>
                </nav>
                <!-- /.sidebar-menu -->
            </div>
            <!-- /.sidebar -->
        </aside>

        <!-- Content Wrapper. Contains page content -->
        <div class="content-wrapper">
            <!-- Content Header (Page header) -->
            <div class="content-header">
                <div class="container-fluid">
                    <div class="row mb-2">
                        <div class="col-sm-6">
                            <h1 class="m-0">@ViewData["Title"]</h1>
                        </div>
                        <div class="col-sm-6">
                            <ol class="breadcrumb float-sm-right">
                                <li class="breadcrumb-item"><a asp-controller="Home" asp-action="Index">Home</a></li>
                                <li class="breadcrumb-item active">@ViewData["Title"]</li>
                            </ol>
                        </div>
                    </div>
                </div>
            </div>
            <!-- /.content-header -->

            <!-- Main content -->
            <main role="main" class="content">
                <section class="content">
                    <div class="container-fluid">
                        @RenderBody()
                    </div>
                </section>
            </main>
            <!-- /.content -->
        </div>
        <!-- /.content-wrapper -->

        <footer class="main-footer">
            <strong>Copyright &copy; 2024-2025 <a href="#">Our Company</a>.</strong>
            All rights reserved.
        </footer>
    </div>
    <!-- ./wrapper -->
    <!-- jQuery -->
    <script src="~/lib/adminlte/plugins/jquery/jquery.min.js"></script>
    <!-- jQuery UI 1.11.4 -->
    <script src="~/lib/adminlte/plugins/jquery-ui/jquery-ui.min.js"></script>
    <!-- Resolve conflict in jQuery UI tooltip with Bootstrap tooltip -->
    <script>
      $.widget.bridge('uibutton', $.ui.button)
    </script>
    <!-- Bootstrap 4 -->
    <script src="~/lib/adminlte/plugins/bootstrap/js/bootstrap.bundle.min.js"></script>
    <!-- daterangepicker -->
    <script src="~/lib/adminlte/plugins/moment/moment.min.js"></script>
    <script src="~/lib/adminlte/plugins/daterangepicker/daterangepicker.js"></script>
    <!-- Tempusdominus Bootstrap 4 -->
    <script src="~/lib/adminlte/plugins/tempusdominus-bootstrap-4/js/tempusdominus-bootstrap-4.min.js"></script>
    <!-- Summernote -->
    <script src="~/lib/adminlte/plugins/summernote/summernote-bs4.min.js"></script>
    <!-- overlayScrollbars -->
    <script src="~/lib/adminlte/plugins/overlayScrollbars/js/jquery.overlayScrollbars.min.js"></script>
    <!-- AdminLTE App -->
    <script src="~/lib/adminlte/dist/js/adminlte.js"></script>

    @await RenderSectionAsync("Scripts", required: false)
</body>
</html>
```

### Step 5: Setup Database and Data Access Layer

UI layout အကြမ်းထည်ပြီးပြီဆိုရင် ကျွန်တော်တို့ရဲ့ Application အတွက် Database ချိတ်ဆက်ဖို့နဲ့ Data access layer ကို EF Core သုံးပြီး ပြင်ဆင်ပါမယ်။

### 5.1 Create Database and Table

SQL Server Management Studio (SSMS) ကိုအသုံးပြုပြီး `SLHDotNetTrainingInPersonBatch0` ဆိုတဲ့ Database အသစ်တစ်ခုဆောက်ပါမယ်။ ပြီးရင် အဲ့ဒီ Database ထဲမှာ `Tbl_Student` ဆိုတဲ့ Table ကို အောက်က script ကိုသုံးပြီး ဆောက်ပါမယ်။

```sql
CREATE TABLE [dbo].[Tbl_Student](
	[StudentId] [int] IDENTITY(1,1) NOT NULL,
	[StudentNo] [varchar](10) NOT NULL,
	[StudentName] [varchar](50) NOT NULL,
	[FatherName] [nvarchar](50) NOT NULL,
	[DateOfBirth] [datetime] NOT NULL,
	[Gender] [varchar](10) NOT NULL,
	[Address] [varchar](250) NOT NULL,
	[MobileNo] [varchar](50) NOT NULL,
	[DeleteFlag] [bit] NOT NULL,
 CONSTRAINT [PK_Tbl_Student] PRIMARY KEY CLUSTERED
(
	[StudentId] ASC
)
)
GO
```

### 5.2 Install EF Core Packages and Tools

ကျွန်တော်တို့ရဲ့ project တွေမှာ EF Core သုံးနိုင်ဖို့အတွက် လိုအပ်တဲ့ NuGet package တွေကို install လုပ်ပါမယ်။

1. **Install EF Core Global Tool:** Terminal သို့မဟုတ် Command Prompt ကိုဖွင့်ပြီး အောက်ပါ command ကို run ပါမယ်။ (အရင်က install လုပ်ပြီးသားဆိုရင် ဒီအဆင့်ကို ကျော်သွားလို့ရပါတယ်)
    
    ```bash
    dotnet tool install --global dotnet-ef
    ```
    
2. **Install NuGet Packages:** `SLHDotNetTrainingBatch0.ThemeMvcApp.Database` project ထဲမှာ EF Core နဲ့ သက်ဆိုင်တဲ့ package တွေအားလုံးကို ထည့်သွင်းပါမယ်။
    - Solution Explorer မှာ `SLHDotNetTrainingBatch0.ThemeMvcApp.Database` project ကို Right-click နှိပ်ပြီး `Manage NuGet Packages...` ကိုရွေးပါ။
    - Browse tab မှာ အောက်က package တွေကို တစ်ခုချင်းစီ ရှာပြီး Install လုပ်ပါမယ်။
        - `Microsoft.EntityFrameworkCore`
        - `Microsoft.EntityFrameworkCore.SqlServer`
        - `Microsoft.EntityFrameworkCore.Design`
        - `Microsoft.EntityFrameworkCore.Tools`
3. **Build the Solution:** Package တွေအားလုံးထည့်ပြီးရင် Solution တစ်ခုလုံးကို Build လုပ်ဖို့လိုအပ်ပါတယ်။

### 5.3 Generate Models from Database (Scaffolding)

ပြီးရင် ကျွန်တော်တို့ရဲ့ Database schema ကနေ Model တွေနဲ့ DbContext ကို generate လုပ်ဖို့ command ကို run ပါမယ်။

**Using Terminal / Command Prompt:**
Solution ရဲ့ root directory မှာ Terminal ကိုဖွင့်ပြီး အောက်က command ကို run ပါ။

```bash
dotnet ef dbcontext scaffold "Server=.;Database=SLHDotNetTrainingInPersonBatch0;User ID=sa;Password=sasa@123;TrustServerCertificate=True;" Microsoft.EntityFrameworkCore.SqlServer --output-dir AppDbContextModels --context AppDbContext --project SLHDotNetTrainingBatch0.ThemeMvcApp.Database --startup-project SLHDotNetTrainingBatch0.ThemeMvcApp
```

**Using Package Manager Console:**
Visual Studio ရဲ့ Package Manager Console ကို အသုံးပြုမယ်ဆိုရင်တော့ Default project ကို `SLHDotNetTrainingBatch0.ThemeMvcApp` ထားပြီး အောက်ပါ command ကို run ပါမယ်:

```bash
scaffold-dbcontext "Server=.;Database=SLHDotNetTrainingInPersonBatch0;User ID=sa;Password=sasa@123;TrustServerCertificate=True;" Microsoft.EntityFrameworkCore.SqlServer -o AppDbContextModels -c AppDbContext -p SLHDotNetTrainingBatch0.ThemeMvcApp.Database -s SLHDotNetTrainingBatch0.ThemeMvcApp
```

ဒီ command က `SLHDotNetTrainingBatch0.ThemeMvcApp.Database` project ထဲမှာ `AppDbContextModels` ဆိုတဲ့ Folder အသစ်ဆောက်ပြီး `AppDbContext.cs` နဲ့ `TblStudent.cs` file တွေကို generate လုပ်ပေးသွားမှာဖြစ်ပါတယ်။

### Step 6: Implement Student CRUD Operations

Data access layer အဆင်သင့်ဖြစ်ပြီဆိုတော့ ကျွန်တော်တို့ `Tbl_Student` table အတွက် CRUD လုပ်ဆောင်ချက်တွေကို Controller နဲ့ View တွေဆောက်ပြီး အကောင်အထည်ဖော်ပါမယ်။

### 6.1 Register AppDbContext in `Program.cs`

ကျွန်တော်တို့ရဲ့ `SLHDotNetTrainingBatch0.ThemeMvcApp` project ထဲက `Program.cs` file မှာ `AppDbContext` ကို register လုပ်ပေးဖို့ လိုအပ်ပါတယ်။

```csharp
using Microsoft.EntityFrameworkCore;
using SLHDotNetTrainingBatch0.ThemeMvcApp.Database.AppDbContextModels;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllersWithViews();

// Add DbContext configuration
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")), ServiceLifetime.Transient);

var app = builder.Build();
//... the rest of the file

```

ပြီးရင် `appsettings.json` file ထဲမှာ ကျွန်တော်တို့ရဲ့ Database connection string ကို ထည့်ပါမယ်။

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=SLHDotNetTrainingInPersonBatch0;User ID=sa;Password=sasa@123;TrustServerCertificate=True;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### 6.2 Create the Student Controller

`SLHDotNetTrainingBatch0.ThemeMvcApp` project ထဲက `Controllers` folder မှာ `StudentController.cs` file အသစ်ဆောက်ပြီး အောက်က code တွေကို ရေးပြပါမယ်။

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using SLHDotNetTrainingBatch0.ThemeMvcApp.Database.AppDbContextModels;

namespace SLHDotNetTrainingBatch0.ThemeMvcApp.Controllers
{
    public class StudentController : Controller
    {
        private readonly AppDbContext _context;

        public StudentController(AppDbContext context)
        {
            _context = context;
        }

        // GET: Student
        public async Task<IActionResult> Index()
        {
            var students = await _context.TblStudents.Where(x => x.DeleteFlag == false).ToListAsync();
            return View(students);
        }

        // GET: Student/Create
        public IActionResult Create()
        {
            return View();
        }

        // POST: Student/Create
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<IActionResult> Create([Bind("StudentNo,StudentName,FatherName,DateOfBirth,Gender,Address,MobileNo")] TblStudent tblStudent)
        {
            if (ModelState.IsValid)
            {
                tblStudent.DeleteFlag = false;
                _context.Add(tblStudent);
                await _context.SaveChangesAsync();
                return RedirectToAction(nameof(Index));
            }
            return View(tblStudent);
        }

        // GET: Student/Edit/5
        public async Task<IActionResult> Edit(int? id)
        {
            if (id == null) return NotFound();
            var tblStudent = await _context.TblStudents.FindAsync(id);
            if (tblStudent == null || tblStudent.DeleteFlag == true) return NotFound();
            return View(tblStudent);
        }

        // POST: Student/Edit/5
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<IActionResult> Edit(int id, [Bind("StudentId,StudentNo,StudentName,FatherName,DateOfBirth,Gender,Address,MobileNo,DeleteFlag")] TblStudent tblStudent)
        {
            if (id != tblStudent.StudentId) return NotFound();
            if (ModelState.IsValid)
            {
                _context.Update(tblStudent);
                await _context.SaveChangesAsync();
                return RedirectToAction(nameof(Index));
            }
            return View(tblStudent);
        }

        // GET: Student/Delete/5
        public async Task<IActionResult> Delete(int? id)
        {
            if (id == null) return NotFound();
            var tblStudent = await _context.TblStudents.FirstOrDefaultAsync(m => m.StudentId == id && m.DeleteFlag == false);
            if (tblStudent == null) return NotFound();
            return View(tblStudent);
        }

        // POST: Student/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public async Task<IActionResult> DeleteConfirmed(int id)
        {
            var tblStudent = await _context.TblStudents.FindAsync(id);
            if (tblStudent != null)
            {
                tblStudent.DeleteFlag = true; // Soft Delete
                _context.Update(tblStudent);
                await _context.SaveChangesAsync();
            }
            return RedirectToAction(nameof(Index));
        }
    }
}
```

### 6.3 Create the Student Views

`SLHDotNetTrainingBatch0.ThemeMvcApp` project ထဲက `Views` folder မှာ `Student` folder အသစ်ဆောက်ပြီး အောက်က View file တွေကို ဆောက်ပါမယ်။

**1. `Index.cshtml` (Student List View)**

```csharp
@model IEnumerable<SLHDotNetTrainingBatch0.ThemeMvcApp.Database.AppDbContextModels.TblStudent>

@{
    ViewData["Title"] = "Student List";
}

<p>
    <a asp-action="Create" class="btn btn-success">Create New</a>
</p>
<div class="card">
    <div class="card-body">
        <table class="table table-bordered table-striped">
            <thead>
                <tr>
                    <th>No.</th>
                    <th>Student No</th>
                    <th>Student Name</th>
                    <th>Father Name</th>
                    <th>Date of Birth</th>
                    <th>Gender</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
                @foreach (var (item, index) in Model.Select((value, i) => (value, i))) {
                <tr>
                    <td>@(index + 1)</td>
                    <td>@Html.DisplayFor(modelItem => item.StudentNo)</td>
                    <td>@Html.DisplayFor(modelItem => item.StudentName)</td>
                    <td>@Html.DisplayFor(modelItem => item.FatherName)</td>
                    <td>@item.DateOfBirth.ToString("dd-MM-yyyy")</td>
                    <td>@Html.DisplayFor(modelItem => item.Gender)</td>
                    <td>
                        <a asp-action="Edit" asp-route-id="@item.StudentId" class="btn btn-warning btn-sm">Edit</a>
                        <a asp-action="Delete" asp-route-id="@item.StudentId" class="btn btn-danger btn-sm">Delete</a>
                    </td>
                </tr>
                }
            </tbody>
        </table>
    </div>
</div>
```

**2. `Create.cshtml` (Create View)**

```csharp
@model SLHDotNetTrainingBatch0.ThemeMvcApp.Database.AppDbContextModels.TblStudent

@{
    ViewData["Title"] = "Create Student";
}

<div class="card card-primary">
    <div class="card-header"><h3 class="card-title">Add New Student</h3></div>
    <form asp-action="Create">
        <div class="card-body">
            <div asp-validation-summary="ModelOnly" class="text-danger"></div>
            <div class="form-group"><label asp-for="StudentNo"></label><input asp-for="StudentNo" class="form-control" /><span asp-validation-for="StudentNo" class="text-danger"></span></div>
            <div class="form-group"><label asp-for="StudentName"></label><input asp-for="StudentName" class="form-control" /><span asp-validation-for="StudentName" class="text-danger"></span></div>
            <div class="form-group"><label asp-for="FatherName"></label><input asp-for="FatherName" class="form-control" /><span asp-validation-for="FatherName" class="text-danger"></span></div>
            <div class="form-group"><label asp-for="DateOfBirth"></label><input asp-for="DateOfBirth" class="form-control" type="date" /><span asp-validation-for="DateOfBirth" class="text-danger"></span></div>
            <div class="form-group"><label asp-for="Gender"></label><input asp-for="Gender" class="form-control" /><span asp-validation-for="Gender" class="text-danger"></span></div>
            <div class="form-group"><label asp-for="Address"></label><input asp-for="Address" class="form-control" /><span asp-validation-for="Address" class="text-danger"></span></div>
            <div class="form-group"><label asp-for="MobileNo"></label><input asp-for="MobileNo" class="form-control" /><span asp-validation-for="MobileNo" class="text-danger"></span></div>
        </div>
        <div class="card-footer">
            <button type="submit" class="btn btn-primary">Create</button>
            <a asp-action="Index" class="btn btn-secondary">Back to List</a>
        </div>
    </form>
</div>
```

**3. `Edit.cshtml` (Edit View)**

```csharp
@model SLHDotNetTrainingBatch0.ThemeMvcApp.Database.AppDbContextModels.TblStudent

@{
    ViewData["Title"] = "Edit Student";
}

<div class="card card-warning">
    <div class="card-header"><h3 class="card-title">Edit Student</h3></div>
    <form asp-action="Edit">
        <div class="card-body">
            <input type="hidden" asp-for="StudentId" />
            <input type="hidden" asp-for="DeleteFlag" />
            <div class="form-group"><label asp-for="StudentNo"></label><input asp-for="StudentNo" class="form-control" /><span asp-validation-for="StudentNo" class="text-danger"></span></div>
            <div class="form-group"><label asp-for="StudentName"></label><input asp-for="StudentName" class="form-control" /><span asp-validation-for="StudentName" class="text-danger"></span></div>
            <div class="form-group"><label asp-for="FatherName"></label><input asp-for="FatherName" class="form-control" /><span asp-validation-for="FatherName" class="text-danger"></span></div>
            <div class="form-group"><label asp-for="DateOfBirth"></label><input asp-for="DateOfBirth" class="form-control" type="date" value="@Model.DateOfBirth.ToString("yyyy-MM-dd")" /><span asp-validation-for="DateOfBirth" class="text-danger"></span></div>
            <div class="form-group"><label asp-for="Gender"></label><input asp-for="Gender" class="form-control" /><span asp-validation-for="Gender" class="text-danger"></span></div>
            <div class="form-group"><label asp-for="Address"></label><input asp-for="Address" class="form-control" /><span asp-validation-for="Address" class="text-danger"></span></div>
            <div class="form-group"><label asp-for="MobileNo"></label><input asp-for="MobileNo" class="form-control" /><span asp-validation-for="MobileNo" class="text-danger"></span></div>
        </div>
        <div class="card-footer">
            <button type="submit" class="btn btn-warning">Save Changes</button>
            <a asp-action="Index" class="btn btn-secondary">Back to List</a>
        </div>
    </form>
</div>
```

**4. `Delete.cshtml` (Delete View)**

```csharp
@model SLHDotNetTrainingBatch0.ThemeMvcApp.Database.AppDbContextModels.TblStudent

@{
    ViewData["Title"] = "Delete Student";
}

<div class="card card-danger">
    <div class="card-header"><h3 class="card-title">Confirm Deletion</h3></div>
    <div class="card-body">
        <h3>Are you sure you want to delete this student?</h3>
        <hr />
        <dl class="row">
            <dt class="col-sm-2">@Html.DisplayNameFor(model => model.StudentNo)</dt>
            <dd class="col-sm-10">@Html.DisplayFor(model => model.StudentNo)</dd>
            <dt class="col-sm-2">@Html.DisplayNameFor(model => model.StudentName)</dt>
            <dd class="col-sm-10">@Html.DisplayFor(model => model.StudentName)</dd>
        </dl>
    </div>
    <div class="card-footer">
        <form asp-action="Delete">
            <input type="hidden" asp-for="StudentId" />
            <input type="submit" value="Delete" class="btn btn-danger" />
            <a asp-action="Index" class="btn btn-secondary">Cancel</a>
        </form>
    </div>
</div>
```

### Step 7: Run Our Application

ပြီးရင်တော့ ကျွန်တော်တို့ရဲ့ project ကို run ကြည့်လို့ရပါပြီ။ Visual Studio မှာ `SLHDotNetTrainingBatch0.ThemeMvcApp` ကို Startup Project အဖြစ် သတ်မှတ်ပြီး `F5` ကိုနှိပ်ပြီး run လိုက်ပါ။

Application တက်လာတဲ့အခါမှာ ဘေးဘက် sidebar မှာ "Student Management" link ကိုနှိပ်ပြီး ကျောင်းသား CRUD လုပ်ဆောင်ချက်တွေကို စတင်အသုံးပြုနိုင်ပြီ ဖြစ်ပါတယ်ခင်ဗျာ။