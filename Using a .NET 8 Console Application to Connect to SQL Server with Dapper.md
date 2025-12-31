# Using .NET 8 Console App with Dapper

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

## **ကျွန်တော်တို့ရဲ့ .NET Application မှာ Dapper အတွက် Code ရေးပြပါမယ်**

### **Dapper** Installation

**Dapper** ကို NuGet Package Manager ကနေထည့်သွင်းဖို့ လိုအပ်ပါတယ်။ **Visual Studio** မှာ **Manage NuGet Packages** ကိုသုံးပြီး **Dapper** package ကို ထည့်သွင်းနိုင်ပါတယ်။

1. **Solution Explorer** မှာ Project ကို right-click ပြီး **Manage NuGet Packages** ကိုရွေးချယ်ပါ။
2. **Browse** tab မှာ `Dapper` လို့ရှာပြီး **Install** ကိုနှိပ်ပါ။

### **Database** နှင့် Dapper ချိတ်ဆက်ခြင်း

```csharp
using System;
using System.Data.SqlClient;
using Dapper;  // Dapper namespace

namespace SLHDotNetTrainingInPersonBatch0.ConsoleApp
{
    public class DapperService
    {
        // Connection string to connect with SQL Server
        string connectionString = "Data Source=.;Initial Catalog=SLHDotNetTrainingInPersonBatch0;User ID=sa;Password=sasa@123;TrustServerCertificate=True;";

        // Read data from the database using Dapper
        public void Read()
        {
            using (SqlConnection connection = new SqlConnection(connectionString))
            {
                connection.Open();

                string query = @"SELECT [StudentId], [StudentNo], [StudentName], [FatherName], [DateOfBirth], [Gender], [Address], [MobileNo], [DeleteFlag]
                                 FROM [dbo].[Tbl_Student]";

                // Using Dapper to execute the query
                var students = connection.Query(query);

                // Display the data
                foreach (var student in students)
                {
                    Console.WriteLine($"{student.StudentNo} - {student.StudentName}");
                }

                connection.Close();
            }
        }

        // Insert new data using Dapper
        public void Create()
        {
            using (SqlConnection connection = new SqlConnection(connectionString))
            {
                connection.Open();

                string query = @"INSERT INTO [dbo].[Tbl_Student] ([StudentNo], [StudentName], [FatherName], [DateOfBirth], [Gender], [Address], [MobileNo], [DeleteFlag])
                                 VALUES (@StudentNo, @StudentName, @FatherName, @DateOfBirth, @Gender, @Address, @MobileNo, @DeleteFlag)";

                var parameters = new
                {
                    StudentNo = "S12345",
                    StudentName = "John Doe",
                    FatherName = "Richard Doe",
                    DateOfBirth = DateTime.Parse("2000-01-01"),
                    Gender = "M",
                    Address = "123 Main St",
                    MobileNo = "555-1234",
                    DeleteFlag = 0
                };

                // Using Dapper to execute the insert query
                var result = connection.Execute(query, parameters);

                string message = result > 0 ? "Saving Successful." : "Saving Failed.";
                Console.WriteLine(message);

                connection.Close();
            }
        }

        // Update data using Dapper
        public void Update()
        {
            using (SqlConnection connection = new SqlConnection(connectionString))
            {
                connection.Open();

                string query = @"UPDATE [dbo].[Tbl_Student]
                                 SET [StudentName] = @StudentName
                                 WHERE StudentId = @StudentId";

                var parameters = new { StudentName = "Jane Doe", StudentId = 8 };

                // Using Dapper to execute the update query
                var result = connection.Execute(query, parameters);
                string message = result > 0 ? "Updating Successful." : "Updating Failed.";

                connection.Close();

                Console.WriteLine(message);
            }
        }

        // Delete data using Dapper
        public void Delete()
        {
            using (SqlConnection connection = new SqlConnection(connectionString))
            {
                connection.Open();

                string query = @"UPDATE [dbo].[Tbl_Student]
                                 SET DeleteFlag = 1
                                 WHERE StudentId = @StudentId";

                var parameters = new { StudentId = 8 };

                // Using Dapper to execute the delete query
                var result = connection.Execute(query, parameters);
                string message = result > 0 ? "Deleting Successful." : "Deleting Failed.";

                connection.Close();

                Console.WriteLine(message);
            }
        }
    }
}
```

### **Program.cs** မှာ Dapper Methods ကို ခေါ်သုံးခြင်း

```csharp
using System;
using SLHDotNetTrainingInPersonBatch0.ConsoleApp;

namespace SLHDotNetTrainingInPersonBatch0.ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // DapperService instance
            DapperService dapperService = new DapperService();

            // Call Read method
            Console.WriteLine("Reading data...");
            dapperService.Read();

            // Call Create method
            Console.WriteLine("Inserting new data...");
            dapperService.Create();

            // Call Update method
            Console.WriteLine("Updating data...");
            dapperService.Update();

            // Call Delete method
            Console.WriteLine("Deleting data...");
            dapperService.Delete();

            Console.ReadLine();
        }
    }
}
```

ဒီအဆင့်တွေ ပြီးပြီဆိုရင်တော့ ကျွန်တော်တို့ရဲ့ **console application** ကို **run** ပြီး **Database** လုပ်ဆောင်ချက်တွေကို မြင်တွေ့နိုင်ပါပြီဗျ။ 

`သတိထားရမယ့်အချက်ကတော့ **Connection String** ထဲမှာ **Initial Catalog** မှာ ထည့်သွင်းထားတဲ့ **Database** နာမည်က **SSMS** မှာ **ဆောက်**ထားတဲ့ **Database** နာမည်နဲ့ တူညီဖို့ **လိုအပ်ပါတယ်**ဗျ။`