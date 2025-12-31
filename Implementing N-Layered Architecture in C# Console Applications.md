# Implementing N-Layered Architecture in C# Console Applications

N-Layered Architecture ဆိုတာ Software ရေးသားရာမှာ အသုံးပြုတဲ့ တည်ဆောက်ပုံ (Design Pattern) တစ်ခုဖြစ်ပြီး Application တစ်ခုရဲ့ မတူညီတဲ့ အပိုင်းတွေကို တာဝန်အလိုက် အလွှာ (Layers) တွေ ခွဲခြားပြီး တည်ဆောက်တာ ဖြစ်ပါတယ်။ "N" ဆိုတာက ကိန်းဂဏန်းတစ်ခုကို ကိုယ်စားပြုတာဖြစ်ပြီး၊ ကျွန်တော်တို့ ရေးသားမယ့် Application ရဲ့ ရှုပ်ထွေးမှုပေါ်မူတည်ပြီး အလွှာအရေအတွက် ၃-လွှာ၊ ၄-လွှာ သို့မဟုတ် အဲ့ဒီထက်လည်းပိုပြီး ရှိနိုင်ပါတယ်။

ဒီလို အလွှာတွေခွဲခြားလိုက်ခြင်းအားဖြင့် ပြုပြင်ထိန်းသိမ်းရ လွယ်ကူခြင်း (Maintainability)၊ လိုအပ်သလို အတိုင်းအတာ ချဲ့ထွင်နိုင်ခြင်း (Scalability) နဲ့ ပြောင်းလွယ်ပြင်လွယ် ရှိခြင်း (Flexibility) စတဲ့ အကျိုးကျေးဇူးတွေ ရရှိစေပါတယ်။

### Real-world Analogy (စားသောက်ဆိုင် ဥပမာ)

ဒါကို မြင်သာအောင် စားသောက်ဆိုင်တစ်ဆိုင်နဲ့ နှိုင်းယှဉ်ကြည့်ရအောင်။

- **Presentation Layer (စားပွဲထိုး):** စားသုံးသူတွေဆီက Order လက်ခံပြီး ဟင်းပွဲချပေးတာ ဖြစ်ပါတယ်။ C# Console App မှာဆိုရင်တော့ User က Keyboard ကနေ စာရိုက်ထည့်တာ၊ Screen ပေါ်မှာ စာတန်းပေါ်လာတာတွေ ဖြစ်ပါတယ်။
- **Business Logic Layer (စားဖိုမှူး):** ဟင်းချက်နည်း (Business Rules) အတိုင်း ချက်ပြုတ်စီမံတဲ့နေရာ ဖြစ်ပါတယ်။ Order က မှန်ကန်မှုရှိမရှိ စစ်ဆေးတာတွေ လုပ်ဆောင်ပါတယ်။
- **Data Access Layer (သိုလှောင်ခန်း):** ဟင်းချက်ဖို့လိုတဲ့ ကုန်ကြမ်းတွေကို ထုတ်ယူသိမ်းဆည်းတဲ့ နေရာဖြစ်ပါတယ်။ Program မှာဆိုရင် Database ထဲက Data တွေကို ယူတာ၊ သိမ်းတာတွေ လုပ်ဆောင်ပါတယ်။

---

## C# Console Implementation Guide

အခု ကျွန်တော်တို့ရဲ့ C# Console Application မှာ ရိုးရှင်းတဲ့ "User Management System" လေးတစ်ခုကို N-Layered ပုံစံနဲ့ တည်ဆောက်ကြည့်ရအောင်။ ကျွန်တော်တို့ရဲ့ Project မှာ အဓိက အလွှာ ၃ ခု ခွဲခြားသွားပါမယ်။

### 1. Folder Structure Setup

အရင်ဆုံး ကျွန်တော်တို့ရဲ့ Project ထဲမှာ ကုဒ်တွေကို စနစ်တကျခွဲခြားဖို့ Folder ၃ ခု ဆောက်ပါမယ်။

1. **Models** (Data ပုံစံတွေ ထားဖို့)
2. **DAL** (Data Access Layer အတွက်)
3. **BLL** (Business Logic Layer အတွက်)
4. **Program.cs** (ဒါက Presentation Layer အဖြစ် ရှိနေမှာပါ)

### 2. Creating the Model

ပထမဆုံး `Models` Folder ထဲမှာ `User.cs` ဆိုတဲ့ Class တစ်ခုကို ဆောက်ပါမယ်။ ဒါက ကျွန်တော်တို့ သယ်ယူပို့ဆောင်မယ့် Data ပုံစံဖြစ်ပါတယ်။

```csharp
// Models/User.cs
namespace NLayerDemo.Models
{
    public class User
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
    }
}
```

### 3. Building the Data Access Layer (DAL)

နောက်တစ်ဆင့်အနေနဲ့ `DAL` Folder ထဲမှာ `UserRepository.cs` ကို ဆောက်ပါမယ်။ ဒီအလွှာရဲ့ တာဝန်က Database နဲ့ တိုက်ရိုက်ထိတွေ့ဖို့ ဖြစ်ပါတယ်။ အခုလောလောဆယ် Database အစစ်မရှိသေးတဲ့အတွက် List တစ်ခုကို Database အနေနဲ့ သဘောထားပြီး ရေးပြပါမယ်။

```csharp
// DAL/UserRepository.cs
using NLayerDemo.Models;
using System.Collections.Generic;
using System.Linq;

namespace NLayerDemo.DAL
{
    public class UserRepository
    {
        // Database ကို ကိုယ်စားပြုတဲ့ List ပါ
        private static List<User> _fakeDatabase = new List<User>();

        public void Add(User user)
        {
            // Database ထဲကို သိမ်းတဲ့အလုပ်ကို လုပ်ပါတယ်
            _fakeDatabase.Add(user);
        }

        public List<User> GetAll()
        {
            // Database ထဲက Data အကုန်ပြန်ယူပါတယ်
            return _fakeDatabase;
        }
    }
}
```

### 4. Building the Business Logic Layer (BLL)

ဆက်လက်ပြီး `BLL` Folder ထဲမှာ `UserService.cs` ကို ဆောက်ပါမယ်။ ဒီအလွှာက `Presentation Layer` နဲ့ `DAL` ကြားမှာ ကြားခံဆောင်ရွက်ပေးပါမယ်။ ဥပမာ - User နာမည်မပါရင် လက်မခံဘူးဆိုတဲ့ Logic မျိုးကို ဒီမှာရေးပါမယ်။

```csharp
// BLL/UserService.cs
using NLayerDemo.BLL;
using NLayerDemo.DAL;
using NLayerDemo.Models;
using System;
using System.Collections.Generic;

namespace NLayerDemo.BLL
{
    public class UserService
    {
        private UserRepository _repository = new UserRepository();

        public void RegisterUser(string name, string email)
        {
            // Logic စစ်ဆေးခြင်း
            if (string.IsNullOrWhiteSpace(name))
            {
                throw new Exception("နာမည် ထည့်သွင်းရန် လိုအပ်ပါတယ်");
            }

            // Logic အဆင်ပြေမှ DAL ကို လှမ်းခေါ်ပြီး Data သိမ်းပါမယ်
            User newUser = new User
            {
                Id = new Random().Next(1, 1000),
                Name = name,
                Email = email
            };

            _repository.Add(newUser);
        }

        public List<User> GetUserList()
        {
            return _repository.GetAll();
        }
    }
}
```

### 5. The Presentation Layer (Console UI)

နောက်ဆုံးမှာတော့ `Program.cs` မှာ User နဲ့ ထိတွေ့မယ့်အပိုင်းကို ရေးပြပါမယ်။ ဒီမှာ Database နဲ့ပတ်သက်တဲ့ Code တွေ လုံးဝမပါဘဲ BLL (UserService) ကိုပဲ ခေါ်သုံးထားတာ တွေ့ရပါလိမ့်မယ်။

```csharp
// Program.cs
using NLayerDemo.BLL;
using NLayerDemo.Models;
using System;

class Program
{
    static void Main(string[] args)
    {
        UserService userService = new UserService();

        while (true)
        {
            Console.WriteLine("1. User အသစ်ထည့်မယ်");
            Console.WriteLine("2. User စာရင်းကြည့်မယ်");
            Console.Write("တစ်ခုခုရွေးပါ: ");
            var choice = Console.ReadLine();

            if (choice == "1")
            {
                Console.Write("နာမည်: ");
                string name = Console.ReadLine();
                Console.Write("Email: ");
                string email = Console.ReadLine();

                try
                {
                    // Business Logic Layer ကို လှမ်းခေါ်ပါတယ်
                    userService.RegisterUser(name, email);
                    Console.WriteLine("အောင်မြင်စွာ သိမ်းဆည်းလိုက်ပါပြီ။");
                }
                catch (Exception ex)
                {
                    Console.WriteLine($"Error: {ex.Message}");
                }
            }
            else if (choice == "2")
            {
                var users = userService.GetUserList();
                Console.WriteLine("--- User စာရင်း ---");
                foreach (var user in users)
                {
                    Console.WriteLine($"ID: {user.Id}, Name: {user.Name}, Email: {user.Email}");
                }
            }
            
            Console.WriteLine("--------------------");
        }
    }
}
```

---

## Summary

ဒီဥပမာမှာ တွေ့ရတဲ့အတိုင်း ကျွန်တော်တို့ရဲ့ Project မှာ တာဝန်တွေကို သီးခြားစီ ခွဲထားလိုက်ပါတယ်။

1. **Presentation Layer (`Program.cs`)**: User ဆီက Data လက်ခံတာနဲ့ ပြပေးတာပဲ လုပ်ပါတယ်။
2. **BLL (`UserService`)**: Data မှန်ကန်မှု ရှိမရှိ စစ်ဆေးပြီးမှ အလုပ်ဆက်လုပ်ပါတယ်။
3. **DAL (`UserRepository`)**: Data သိမ်းဆည်းခြင်းနဲ့ ပြန်ထုတ်ခြင်းကိုပဲ သီးသန့်တာဝန်ယူပါတယ်။

**Note:** ဒီဥပမာမှာတော့ List ကို သုံးထားပေမယ့် နောက်ပိုင်းတွေမှာ SQL Server လို Database အစစ်နဲ့ ချိတ်ဆက်ချင်ရင် `DAL` အပိုင်းကိုပဲ ပြင်စရာ လိုအပ်ပါတယ်။ ကျန်တဲ့ `Program.cs` နဲ့ `UserService` ကို ပြင်စရာမလိုဘဲ ဆက်သုံးလို့ရနေမှာ ဖြစ်ပါတယ်။

ဒါကတော့ N-Layered Architecture ကို C# Console App မှာ ရိုးရှင်းစွာ အသုံးချပုံပဲ ဖြစ်ပါတယ်။ ဒီ Architecture ကို နားလည်ထားရင် နောက်ပိုင်း Web Application တွေ ရေးတဲ့အခါမှာလည်း အများကြီး အထောက်အကူ ဖြစ်စေပါလိမ့်မယ်။

ဒီအကြောင်းအရာနဲ့ ပတ်သက်ပြီး ကျွန်တော်တို့ရဲ့ Project မှာ ထပ်ဖြည့်စွက်ချင်တာတွေ ရှိရင် ပြောပေးပါခင်ဗျာ။