# Using .NET 8 Console App to Connect to SQL Server with Microsoft.Data.SqlClient

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

## **ကျွန်တော်တို့ရဲ့ .NET Application မှာ Code ရေးပြပါမယ်**

ကျွန်တော်တို့ရဲ့ **.NET 8 Console Application** ကနေ **SQL Server** ကို **Microsoft.Data.SqlClient** **package** ကိုအသုံးပြုပြီး ဘယ်လိုချိတ်ဆက်ရမယ်ဆိုတာကို ရေးပြပါမယ်။

### **Microsoft.Data.SqlClient Package** ကို ထည့်သွင်းခြင်း

ကျွန်တော်တို့ရဲ့ **.NET 8 Console Application** ကို **ဆောက်**ပြီးပြီဆိုရင် **SQL Server** နဲ့ **Database**ကို ချိတ်ဆက်ဖို့အတွက် လိုအပ်တဲ့ **data access library** ကို ထည့်သွင်းဖို့ လိုအပ်ပါတယ်။ **.NET Core** နဲ့ **.NET 8** မှာ **SQL Server** နဲ့ချိတ်ဆက်ဖို့ အကောင်းဆုံး **package** ကတော့ **Microsoft.Data.SqlClient** ပါပဲ။ **Visual Studio** ရဲ့ **NuGet Package Manager** ကိုအသုံးပြုပြီး ဒီ **package** ကို ထည့်သွင်းနိုင်ပါတယ်ဗျ။

**Visual Studio** ရဲ့ **Solution Explorer** **Folder** ထဲက ကျွန်တော်တို့ရဲ့ **Project** နာမည်ပေါ်မှာ **Right-click** နှိပ်ပါ။ ပေါ်လာတဲ့ **context menu** ကနေ **Manage NuGet Packages...** ကိုရွေးချယ်လိုက်ပါ။ **Browse** **tab** ကိုနှိပ်ပါ။ **search bar** ထဲမှာ `Microsoft.Data.SqlClient` လို့ရိုက်ထည့်ပါ။ **search result** မှာတွေ့ရတဲ့ **package** ပေါ်မှာ **click** နှိပ်ပါ။ ညာဘက်ခြမ်းမှာရှိတဲ့ **Install** **button** ကို **click** နှိပ်ပြီး **package** ကို ထည့်သွင်းနိုင်ပါတယ်ဗျ။

ဒီလိုထည့်သွင်းပြီးသွားရင်တော့ ကျွန်တော်တို့ရဲ့ **Project** မှာ **SqlConnection**၊ **SqlCommand** နဲ့ **SqlDataAdapter** စတဲ့ **class** တွေကို **Database** နဲ့ချိတ်ဆက်ဖို့ အသုံးပြုနိုင်ပါပြီ။

### **Data Access Class** ကို ရေးပြပါမယ်

အခုဆိုရင် **Database**နဲ့သက်ဆိုင်တဲ့ လုပ်ဆောင်ချက်တွေအတွက် **Class** တစ်ခုကို **ဆောက်**ပါမယ်။ ကျွန်တော်တို့ရဲ့ **Class** နာမည်ကို **AdoDotNetService.cs** လို့ပေးပါမယ်။ ဒီ **Class** ထဲမှာ **ဒေတာ**တွေဖတ်တာ၊ အသစ်ထည့်တာ၊ ပြင်တာနဲ့ ဖျက်တာတွေကို လုပ်ဆောင်မယ့် **methods** တွေပါဝင်မှာပါ။

```csharp
using System;
using System.Data;
using Microsoft.Data.SqlClient; // လိုအပ်တဲ့ Namespace ကိုထည့်ထားပါတယ်

namespace SLHDotNetTrainingInPersonBatch0.ConsoleApp
{
    public class AdoDotNetService
    {
        // Database နဲ့ချိတ်ဆက်ဖို့ connection string ကိုသတ်မှတ်ပါတယ်
        string connectionString = "Data Source=.;Initial Catalog=SLHDotNetTrainingInPersonBatch0;User ID=sa;Password=sasa@123;TrustServerCertificate=True;";

        // ဒေတာတွေကိုဖတ်ဖို့ Read Method ကိုရေးပြပါမယ်
        public void Read()
        {
            using (SqlConnection connection = new SqlConnection(connectionString))
            {
                connection.Open();

                string query = @"SELECT [StudentId], [StudentNo], [StudentName], [FatherName], [DateOfBirth], [Gender], [Address], [MobileNo], [DeleteFlag]
                                 FROM [dbo].[Tbl_Student]";

                SqlCommand cmd = new SqlCommand(query, connection);
                SqlDataAdapter adapter = new SqlDataAdapter(cmd);
                DataTable dt = new DataTable();
                adapter.Fill(dt);

                // ဒေတာတွေကိုပြသပါမယ်
                for (int i = 0; i < dt.Rows.Count; i++)
                {
                    DataRow dr = dt.Rows[i];
                    string no = dr["StudentNo"].ToString();
                    string name = dr["StudentName"].ToString();
                    Console.WriteLine($"{i + 1} {no} - {name}");
                }

                connection.Close();
            }
        }

        // ဒေတာအသစ်ထည့်သွင်းဖို့ Create Method ကိုရေးပြပါမယ်
        public void Create()
        {
            using (SqlConnection connection = new SqlConnection(connectionString))
            {
                connection.Open();

                string query = @"INSERT INTO [dbo].[Tbl_Student] ([StudentNo], [StudentName], [FatherName], [DateOfBirth], [Gender], [Address], [MobileNo], [DeleteFlag])
                                 VALUES ('S12345', 'John Doe', 'Richard Doe', '2000-01-01', 'M', '123 Main St', '555-1234', 0)";

                SqlCommand cmd = new SqlCommand(query, connection);
                int result = cmd.ExecuteNonQuery();

                connection.Close();

                string message = result > 0 ? "Saving Successful." : "Saving Failed.";  
                Console.WriteLine(message);
            }
        }

        // ဒေတာပြင်ဆင်ဖို့ Update Method ကိုရေးပြပါမယ်
        public void Update()
        {
            using (SqlConnection connection = new SqlConnection(connectionString))
            {
                connection.Open();

                string query = @"UPDATE [dbo].[Tbl_Student]
                                 SET [StudentName] = 'Jane Doe'
                                 WHERE StudentId = 8";

                SqlCommand cmd = new SqlCommand(query, connection);
                int result = cmd.ExecuteNonQuery();
                string message = result > 0 ? "Updating Successful." : "Updating Failed.";

                connection.Close();

                Console.WriteLine(message);
            }
        }

        // ဒေတာဖျက်ဖို့ Delete Method ကိုရေးပြပါမယ်
        public void Delete()
        {
            using (SqlConnection connection = new SqlConnection(connectionString))
            {
                connection.Open();

                string query = @"UPDATE [dbo].[Tbl_Student]
                                 SET DeleteFlag = 1
                                 WHERE StudentId = 8";

                SqlCommand cmd = new SqlCommand(query, connection);
                int result = cmd.ExecuteNonQuery();
                string message = result > 0 ? "Deleting Successful." : "Deleting Failed.";

                connection.Close();

                Console.WriteLine(message);
            }
        }

    }
}
```

### **Program.cs** **မှာ Methods** တွေကို ခေါ်သုံးခြင်း

**ပြီးရင်တော့** ကျွန်တော်တို့ရဲ့ **AdoDotNetService** **Class** ရဲ့ **instance** ကို **ဆောက်**ပြီးတော့ **Program.cs** **Folder**ထဲမှာပါတဲ့ `Main` **method** ကနေ အောက်ပါအတိုင်း ခေါ်သုံးနိုင်ပါတယ်ဗျ။

```csharp
using System;
using SLHDotNetTrainingInPersonBatch0.ConsoleApp;

namespace SLHDotNetTrainingInPersonBatch0.ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // AdoDotNetService ရဲ့ instance ကိုဆောက်ပါမယ်
            AdoDotNetService adoService = new AdoDotNetService();

            // Read method ကိုခေါ်ပါမယ်
            Console.WriteLine("ဒေတာဖတ်နေပါတယ်...");
            adoService.Read();

            // Create method ကိုခေါ်ပါမယ်
            Console.WriteLine("အချက်အလက်အသစ်ထည့်နေပါတယ်...");
            adoService.Create();

            // Update method ကိုခေါ်ပါမယ်
            Console.WriteLine("အချက်အလက်ပြင်ဆင်နေပါတယ်...");
            adoService.Update();

            // Delete method ကိုခေါ်ပါမယ်
            Console.WriteLine("အချက်အလက်ဖျက်နေပါတယ်...");
            adoService.Delete();

            Console.ReadLine();
        }
    }
}
```

ဒီအဆင့်တွေ ပြီးပြီဆိုရင်တော့ ကျွန်တော်တို့ရဲ့ **console application** ကို **run** ပြီး **Database** လုပ်ဆောင်ချက်တွေကို မြင်တွေ့နိုင်ပါပြီဗျ။ 

`သတိထားရမယ့်အချက်ကတော့ **Connection String** ထဲမှာ **Initial Catalog** မှာ ထည့်သွင်းထားတဲ့ **Database** နာမည်က **SSMS** မှာ **ဆောက်**ထားတဲ့ **Database** နာမည်နဲ့ တူညီဖို့ **လိုအပ်ပါတယ်**ဗျ။`