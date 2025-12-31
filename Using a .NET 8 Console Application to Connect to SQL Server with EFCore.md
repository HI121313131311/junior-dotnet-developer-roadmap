# Using .NET 8 Console App with EF Core

## **SSMS** ကို အသုံးပြုပြီး **Database** နဲ့ **Table** **ဆောက်ပါမယ်**

ကျွန်တော်တို့ရဲ့ **.NET Application** နဲ့ချိတ်ဆက်အသုံးပြုဖို့အတွက် **SQL Server Management Studio (SSMS)** ကိုအသုံးပြုပြီး **Database** နဲ့ **Table** တစ်ခုကို ဘယ်လို **ဆောက်**ရမယ်ဆိုတာကို ရေးပြပါမယ်။ ပြီးရင် ကျွန်တော်တို့ရဲ့ **.NET 8 Console Application** မှာ **Microsoft.Data.SqlClient** ကိုအသုံးပြုပြီး ချိတ်ဆက်ပုံကိုပါ ဆက်ပြီး ရေးပြပါမယ်ဗျ။

### **SSMS** ကို **Login** ဝင်ခြင်း

ပထမဆုံးအနေနဲ့ **SSMS** ကိုဖွင့်ပြီး ကျွန်တော်တို့ရဲ့ **SQL Server** ကို **Login** ဝင်ဖို့ လိုအပ်ပါတယ်။ **SSMS** ကိုဖွင့်လိုက်ရင် **Connect to Server** **window** ပေါ်လာမှာပါ။ အဲဒီမှာ **Server type** မှာ **Database Engine** ကိုရွေးချယ်ပြီး **Server name** မှာ ကိုယ့်ရဲ့ **SQL Server** နာမည်ကို ရိုက်ထည့်ပါမယ်။ **Login** ဝင်တဲ့အခါမှာ အဆင်ပြေစေဖို့ **Authentication** ကို **SQL Server Authentication** ကိုရွေးချယ်ပြီး **User ID** နဲ့ **Password** ကိုထည့်ပြီး **Connect** **button** ကိုနှိပ်ပါမယ်။

### **Database** အသစ်တစ်ခု **ဆောက်ပါမယ်**

**Login** ဝင်ပြီးပြီဆိုရင် ကျွန်တော်တို့ရဲ့ **Application** အတွက် သီးသန့် **Database** အသစ်တစ်ခုကို **ဆောက်**ပါမယ်။ ဒီနေရာမှာ ကျွန်တော်တို့ရဲ့ **Database** နာမည်ကို `SLHDotNetTrainingInPersonBatch0` လို့ပေးပါမယ်။ **Object Explorer** **window** မှာ **Databases** **Folder**ပေါ်မှာ **Right-click** နှိပ်ပြီး **New Database...** ကိုရွေးချယ်ပါမယ်။ **Database name** **field** မှာ `SLHDotNetTrainingInPersonBatch0` လို့ ရိုက်ထည့်ပြီး **OK** **button** ကိုနှိပ်လိုက်ရင် ရပါပြီ။

### **Table** အသစ်တစ်ခု **ဆောက်ပါမယ်**

**Database** **ဆောက်**ပြီးပြီဆိုရင် အခုကျွန်တော်တို့ လိုအပ်တဲ့ **Table** ကို ဆက်ပြီး **ဆောက်**ပါမယ်။ **Object Explorer** မှာ ခုနက **ဆောက်**ထားတဲ့ `SLHDotNetTrainingInPersonBatch0` **Database** ရဲ့ အောက်မှာရှိတဲ့ **Tables** **Folder**ပေါ်မှာ **Right-click** နှိပ်ပြီး **New** > **Table...** ကိုရွေးချယ်ပါမယ်။ အောက်မှာပေးထားတဲ့ **code** တွေကို **Query window** ထဲမှာ ရိုက်ထည့်ပြီး **Run** လိုက်လို့ရပါတယ်၊ ဒါမှမဟုတ် **SSMS** ရဲ့ **Table Designer** ကို အသုံးပြုပြီးလည်း ရေးပြပါမယ်။

```csharp
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
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON, OPTIMIZE_FOR_SEQUENTIAL_KEY = OFF) ON [PRIMARY]
) ON [PRIMARY]
GO
```

အပေါ်က **code** တွေက **Tbl_Student** ဆိုတဲ့ **Table** ကို **Columns** တွေနဲ့ **Primary Key** ကိုသတ်မှတ်ပေးထားတာပါ။ ဒီ **code** တွေကို **New Query** **window** မှာ run ပြီး **Table** ကို **ဆောက်**နိုင်ပါတယ်။

---

### Setting Up EF Core Database-First in Our .NET Application

EF Core ရဲ့ Database-First ချဉ်းကပ်မှုကို အသုံးပြုပြီး ကျွန်တော်တို့ရဲ့ Database ထဲမှာရှိတဲ့ Table တွေအတိုင်း Model Class တွေနဲ့ DbContext ကို Auto-generate လုပ်ပါမယ်။ ဒီအဆင့်တွေကို မှန်မှန်ကန်ကန်လုပ်ဆောင်ဖို့အတွက် အောက်ပါအတိုင်း ပြင်ဆင်ဖို့လိုအပ်ပါတယ်ဗျ။

### Step 1: Install the EF Core Global Tool

ပထမဆုံးအနေနဲ့ ကျွန်တော်တို့ရဲ့ Terminal ကနေ EF Core command တွေကို run နိုင်ဖို့ `dotnet-ef` tool ကို install လုပ်ဖို့လိုအပ်ပါတယ်။
Terminal သို့မဟုတ် Command Prompt ကိုဖွင့်ပြီး အောက်ပါ command ကို run ပါမယ်။

```bash
dotnet tool install --global dotnet-ef
```

### Step 2: Install NuGet Packages

ပြီးရင် EF Core အတွက် လိုအပ်တဲ့ NuGet Package တွေကို ကျွန်တော်တို့ရဲ့ Project ထဲမှာ ထည့်သွင်းဖို့ လိုအပ်ပါတယ်။

1. Solution Explorer မှာ ကျွန်တော်တို့ရဲ့ Project ကို right-click ပြီး Manage NuGet Packages ကိုရွေးချယ်ပါ။
2. Browse tab မှာ အောက်ပါ package တွေကို တစ်ခုချင်းစီ ရှာပြီး Install လုပ်ပါမယ်။
    - `Microsoft.EntityFrameworkCore`
    - `Microsoft.EntityFrameworkCore.SqlServer`
    - `Microsoft.EntityFrameworkCore.Design`
    - `Microsoft.EntityFrameworkCore.Tools`

### Step 3: Build the Project

Package တွေအားလုံး ထည့်သွင်းပြီးပြီဆိုရင် အရေးကြီးဆုံးအဆင့်တစ်ခုကတော့ ကျွန်တော်တို့ရဲ့ Solution ကို Build လုပ်ဖို့ပါပဲ။ ဒီလို Build လုပ်ပေးလိုက်မှ EF Core tool တွေက ကျွန်တော်တို့ ထည့်သွင်းထားတဲ့ `Microsoft.EntityFrameworkCore.Design` package ကို မှန်မှန်ကန်ကန် ရှာတွေ့မှာဖြစ်ပြီး scaffolding command run တဲ့အခါမှာ error မတက်တော့မှာ ဖြစ်ပါတယ်။

Solution Explorer မှာ Solution ကို right-click နှိပ်ပြီး `Build Solution` ကို ရွေးချယ်နိုင်ပါတယ်ဗျ။

### Step 4: Generate Models from the Database (Scaffolding)

အခု ကျွန်တော်တို့ရဲ့ Project က အဆင်သင့်ဖြစ်ပြီဆိုတော့ Database schema ကိုဖတ်ပြီး C# class တွေ generate လုပ်ဖို့အတွက် scaffolding command ကို run နိုင်ပါပြီ။

Terminal မှာ အောက်ပါ `dotnet ef` command ကို အသုံးပြုနိုင်ပါတယ်:

```bash
dotnet ef dbcontext scaffold "Server=.;Database=SLHDotNetTrainingInPersonBatch0;User ID=sa;Password=sasa@123;TrustServerCertificate=True;" Microsoft.EntityFrameworkCore.SqlServer -o DataAccess -c AppDbContext -f
```

Visual Studio ရဲ့ Package Manager Console ကို အသုံးပြုမယ်ဆိုရင်တော့ အောက်ပါ command ကို run ပါမယ်:

PowerShell

```bash
scaffold-dbcontext "Server=.;Database=SLHDotNetTrainingInPersonBatch0;User ID=sa;Password=sasa@123;TrustServerCertificate=True;" Microsoft.EntityFrameworkCore.SqlServer -o DataAccess -c AppDbContext -f
```

ဒီ command ကို run ပြီးရင် ကျွန်တော်တို့ရဲ့ Project ထဲမှာ `DataAccess` ဆိုတဲ့ Folder အသစ်တစ်ခုကို ဆောက်ပေးပါမယ်။ အဲ့ဒီ Folder ထဲမှာ ကျွန်တော်တို့ သတ်မှတ်ထားတဲ့ `AppDbContext` class နဲ့ Database table တွေအားလုံးအတွက် Model class တွေကို generate လုပ်ပြီး ထည့်ပေးသွားမှာ ဖြစ်ပါတယ်။

---

### Creating the EF Core Service

အခု ကျွန်တော်တို့ generate လုပ်ထားတဲ့ `AppDbContext` ကို အသုံးပြုပြီး Database နဲ့ CRUD (Create, Read, Update, Delete) လုပ်ငန်းစဥ်တွေလုပ်ဆောင်ဖို့ Service class တစ်ခုကို ရေးပြပါမယ်။

```csharp
using SLHDotNetTrainingInPersonBatch0.ConsoleApp.DataAccess;
using System;
using System.Linq;

namespace SLHDotNetTrainingInPersonBatch0.ConsoleApp
{
    public class EfCoreCrudService
    {
        // Read all data from the database
        public void Read()
        {
            using var context = new AppDbContext();
            Console.WriteLine("--- Reading data using EF Core ---");
            var students = context.TblStudents.Where(x => x.DeleteFlag == 0).ToList();

            if (!students.Any())
            {
                Console.WriteLine("No students found.");
                return;
            }

            foreach (var student in students)
            {
                Console.WriteLine($"ID: {student.StudentId}, No: {student.StudentNo}, Name: {student.StudentName}");
            }
        }

        // Edit data by ID
        public void Edit(int studentId)
        {
            using var context = new AppDbContext();
            var student = context.TblStudents
                .FirstOrDefault(s => s.StudentId == studentId && s.DeleteFlag == 0);

            if (student is null)
            {
                Console.WriteLine($"Student with ID {studentId} not found.");
                return;
            }

            Console.WriteLine("--- Editing Student ---");
            Console.WriteLine($"ID: {student.StudentId}");
            Console.WriteLine($"Student No: {student.StudentNo}");
            Console.WriteLine($"Student Name: {student.StudentName}");
            Console.WriteLine("-----------------------");
        }

        // Create new data
        public void Create(string studentNo, string studentName, string fatherName, DateTime dateOfBirth, string gender, string address, string mobileNo)
        {
            using var context = new AppDbContext();
            var student = new TblStudent
            {
                StudentNo = studentNo,
                StudentName = studentName,
                FatherName = fatherName,
                DateOfBirth = dateOfBirth,
                Gender = gender,
                Address = address,
                MobileNo = mobileNo,
                DeleteFlag = 0
            };

            context.TblStudents.Add(student);
            int result = context.SaveChanges();

            string message = result > 0 ? "Saving Successful." : "Saving Failed.";
            Console.WriteLine(message);
        }

        // Update data by ID
        public void Update(int studentId, string studentName, string address)
        {
            using var context = new AppDbContext();
            var student = context.TblStudents
                .FirstOrDefault(s => s.StudentId == studentId && s.DeleteFlag == 0);

            if (student is null)
            {
                Console.WriteLine($"Student with ID {studentId} not found for updating.");
                return;
            }

            student.StudentName = studentName;
            student.Address = address;
            int result = context.SaveChanges();

            string message = result > 0 ? "Updating Successful." : "Updating Failed.";
            Console.WriteLine(message);
        }

        // Delete data by ID (Soft Delete)
        public void Delete(int studentId)
        {
            using var context = new AppDbContext();
            var student = context.TblStudents.FirstOrDefault(s => s.StudentId == studentId);

            if (student is null)
            {
                Console.WriteLine($"Student with ID {studentId} not found for deleting.");
                return;
            }

            student.DeleteFlag = 1; // Set the flag to 1 for soft delete
            int result = context.SaveChanges();

            string message = result > 0 ? "Deleting Successful." : "Deleting Failed.";
            Console.WriteLine(message);
        }
    }
}
```

### Calling EF Core Methods in Program.cs

အခု ကျွန်တော်တို့ရဲ့ `Program.cs` မှာ အသစ်ဆောက်ထားတဲ့ `EfCoreService` ကို ခေါ်သုံးပါမယ်။

```csharp
using System;

namespace SLHDotNetTrainingInPersonBatch0.ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            var service = new EfCoreCrudService();

            // 1. Read all students at the beginning
            Console.WriteLine("--- Reading all students initially ---");
            service.Read();
            Console.WriteLine("----------------------------------\n");

            // 2. Create a new student
            Console.WriteLine("--- Creating a new student ---");
            service.Create(
                "S11223", 
                "Kyaw Kyaw", 
                "U Mya", 
                new DateTime(2002, 5, 20),
                "M", 
                "Yangon", 
                "09111222333"
            );
            service.Read(); // Read again to show the new student
            Console.WriteLine("------------------------------\n");

            // 3. Edit a specific student's data
            // Note: Please use an ID that actually exists in your database.
            int studentIdToEdit = 11; 
            Console.WriteLine($"--- Fetching student with ID {studentIdToEdit} for editing ---");
            service.Edit(studentIdToEdit);
            Console.WriteLine("-----------------------------------------------------------\n");

            // 4. Update that student's data
            Console.WriteLine($"--- Updating student with ID {studentIdToEdit} ---");
            service.Update(studentIdToEdit, "Kyaw Kyaw Oo", "Mandalay");
            service.Read(); // Read again to show the updated data
            Console.WriteLine("------------------------------------------\n");

            // 5. Delete that student
            Console.WriteLine($"--- Deleting student with ID {studentIdToEdit} ---");
            service.Delete(studentIdToEdit);
            service.Read(); // Read again, the student should be gone (soft-deleted)
            Console.WriteLine("------------------------------------------\n");

            Console.ReadLine();
        }
    }
}
```

---

# Note on Using Other Databases

ကျွန်တော်တို့ ဒီဥပမာမှာ `Microsoft.EntityFrameworkCore.SqlServer` ကို အသုံးပြုပြီး SQL Server နဲ့ ချိတ်ဆက်ခဲ့ပါတယ်။ တကယ်လို့ ကျွန်တော်တို့က MySQL, PostgreSQL, SQLite လိုမျိုး တခြား Database တွေကို သုံးချင်တယ်ဆိုရင် သက်ဆိုင်ရာ Database Provider package ကို `Microsoft.EntityFrameworkCore.SqlServer` အစား ထည့်သွင်းပေးဖို့ လိုအပ်ပါတယ်ဗျ။

ဥပမာ PostgreSQL ကိုသုံးချင်ရင် `Npgsql.EntityFrameworkCore.PostgreSQL` package ကို install လုပ်ရမှာဖြစ်ပါတယ်။ Provider အမျိုးမျိုးအကြောင်းကို ဒီ link မှာ အသေးစိတ်လေ့လာနိုင်ပါတယ်: https://learn.microsoft.com/en-us/ef/core/providers/

---

# Why We Should Separate the Database Layer

ကျွန်တော်တို့ရဲ့ Project တွေမှာ EF Core ကို Database-First ချဉ်းကပ်မှုနဲ့ အသုံးပြုတဲ့အခါ Database နဲ့ သက်ဆိုင်တဲ့ Code တွေကို သီးသန့် Class Library တစ်ခုအနေနဲ့ ဘာကြောင့်ခွဲထုတ်သင့်သလဲဆိုတာကို အသေးစိတ်ရှင်းပြပါမယ်။ ဒီလိုခွဲထုတ်ခြင်းအားဖြင့် ကျွန်တော်တို့ရဲ့ Project ကို ပိုမိုကောင်းမွန်အောင် ထိန်းသိမ်းနိုင်ပြီး နောက်ပိုင်းတွေမှာ Feature အသစ်တွေထပ်ထည့်တဲ့အခါ အလွန်အဆင်ပြေစေမှာ ဖြစ်ပါတယ်။

### The Main Problem We Face

ကျွန်တော်တို့ရဲ့ Database ဟာ အမြဲတမ်းပြောင်းလဲနေနိုင်ပါတယ်။ Column အသစ်တွေထပ်တိုးတာ၊ Table တွေထပ်ထည့်တာမျိုးတွေ လုပ်ရလေ့ရှိပါတယ်။ အဲ့ဒီအခါတိုင်းမှာ ကျွန်တော်တို့ဟာ EF Core ရဲ့ scaffolding command ကို ပြန်ပြီး run ပေးရပါတယ်။
ပြဿနာကတော့ EF Core ကနေ auto-generate လုပ်ပေးထားတဲ့ DbContext class တွေ၊ Model class တွေနဲ့ ကျွန်တော်တို့ရေးထားတဲ့ business logic တွေ၊ service တွေက Project တစ်ခုတည်းမှာ အတူတူရှိနေတာကြောင့်ဖြစ်ပါတယ်။ Scaffolding command ကို ပြန် run လိုက်တဲ့အခါ EF Core က ရှိပြီးသား auto-generated file တွေကို အသစ်ပြန်ရေး overwrite လုပ်ပါတယ်။ အဲ့ဒီမှာ ကျွန်တော်တို့ကိုယ်တိုင် custom ရေးထည့်ထားတဲ့ code တွေ၊ method တွေ ထဲမှာ EFCore နဲ့ ပတ်သက်တဲ့ auto-generate လုပ်ပေးထားတဲ့ Code တွေကို ယူသုံးခဲ့သည်ရှိသော် ဒါမှမဟုတ် အရင်ကရှိခဲ့တဲ့ class တစ်ခုခုကို မလိုအပ်တော့လို့ ဖျက်ပစ်လိုက်တာမျိုးတွေ ဖြစ်တတ်ပါတယ်။ ဒီအခြေအနေမှာ ကျွန်တော်တို့ရဲ့ Project ဟာ build error တွေ ရှိလာနိုင်ပါတယ်။

### The Solution: Separating into a Class Library

ဒီပြဿနာကို ဖြေရှင်းဖို့ အကောင်းဆုံးနည်းလမ်းကတော့ Database Layer ကို သီးသန့် Class Library project တစ်ခုအနေနဲ့ ခွဲထုတ်လိုက်တာပါပဲ။ ဆိုလိုတာက EF Core က generate လုပ်ပေးတဲ့ DbContext နဲ့ Model class တွေအားလုံးကို အဲ့ဒီ Library အသစ်ထဲမှာပဲ ထားမှာဖြစ်ပါတယ်။
ဒီလိုခွဲထုတ်လိုက်ခြင်းရဲ့ အဓိကကောင်းကျိုးတွေကို အောက်မှာ ရေးပြပါမယ်။

**1. Easy Maintenance (ထိန်းသိမ်းရလွယ်ကူခြင်း)**
ကျွန်တော်တို့ရဲ့ Database schema မှာ အပြောင်းအလဲရှိလို့ scaffolding command ကို ပြန် run ရတဲ့အခါ ပြောင်းလဲမှုတွေအားလုံးဟာ သီးသန့်ခွဲထုတ်ထားတဲ့ Database Library ထဲမှာပဲ ဖြစ်ပေါ်မှာပါ။ ကျွန်တော်တို့ရဲ့ အဓိက Application Project (ဥပမာ - Web API, MVC) ကို လုံးဝထိခိုက်မှုမရှိတော့ပါဘူး။ Database Library တစ်ခုတည်းကိုပဲ update လုပ်ပြီးရင် ကျန်တဲ့ Project တွေက အဲ့ဒီ Library ကို reference လုပ်ပြီး အသုံးပြုရုံပါပဲ။

**2. Avoiding Build Errors (Build Error များကို ရှောင်ရှားနိုင်ခြင်း)**
အရင်ကလို Project တစ်ခုတည်းမှာ ရောထွေးမနေတော့တဲ့အတွက် scaffolding command ကြောင့် ကျွန်တော်တို့ရေးထားတဲ့ custom service တွေ၊ business logic တွေ ပျက်စီးသွားမှာကို စိုးရိမ်စရာမလိုတော့ပါဘူး။ Database Library မှာ EF Core က generate လုပ်ပေးတဲ့ code တွေသက်သက်ပဲ ရှိသင့်ပါတယ်။ ကျွန်တော်တို့ရဲ့ custom logic တွေကို အဲ့ဒီ Library ထဲမှာ ရေးသားခြင်းမပြုလုပ်ဖို့ လိုအပ်ပါတယ်။ ဒါမှသာ command ကို ဘယ်နှစ်ခါ run run ကျွန်တော်တို့ရဲ့ main project က build error မတက်တော့မှာ ဖြစ်ပါတယ်။

**Note**

> Database Library မှာ EF Core က generate လုပ်ပေးတဲ့ code တွေပဲ သီးသန့်ရှိနေသင့်ပါတယ်။ ကိုယ်ပိုင် business logic တွေ၊ service တွေကို အဲ့ဒီ Library ထဲမှာ ထပ်ပြီးမရေးသင့်ပါဘူး။
> 

**3. Better Project Structure [Separation of Concerns] (ပိုမိုကောင်းမွန်သော Project တည်ဆောက်ပုံ)**
ဒါဟာ Software Engineering မှာ အလွန်အရေးကြီးတဲ့ "Separation of Concerns" ဆိုတဲ့ Rule နဲ့ ကိုက်ညီပါတယ်။ ကျွန်တော်တို့ရဲ့ project မှာ အလွှာ (Layer) တွေ သေသေချာချာခွဲထားလိုက်တာနဲ့တူပါတယ်။

- **Database Layer (ကျွန်တော်တို့ရဲ့ Class Library အသစ်):** သူ့ရဲ့အဓိကတာဝန်က Database နဲ့ ဆက်သွယ်ဖို့၊ Data တွေကို ဖတ်ဖို့၊ ရေးဖို့သက်သက်ပါပဲ။
- **Application Layer (ကျွန်တော်တို့ရဲ့ Web API/MVC Project):** ဒီ Layer ကတော့ business logic တွေ၊ user request တွေကို စီမံခန့်ခွဲဖို့ တာဝန်ယူပါတယ်။ သူက Data လိုအပ်ရင် Database Layer ကို လှမ်းခေါ်သုံးပါမယ်။

ဒီလို Layer တွေခွဲထားတဲ့အတွက် Project ဟာ ပိုပြီးရှင်းလင်းတယ်၊ နားလည်ရလွယ်ကူတယ်၊ အဖွဲ့လိုက်လုပ်ဆောင်တဲ့အခါမှာလည်း အဆင်ပြေစေပါတယ်။