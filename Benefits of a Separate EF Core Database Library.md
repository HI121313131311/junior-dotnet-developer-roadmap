# Benefits of a Separate EF Core Database Library

ကျွန်တော်တို့ရဲ့ Project တွေမှာ EF Core ကို Database-First ချဉ်းကပ်မှုနဲ့ အသုံးပြုတဲ့အခါ Database နဲ့ သက်ဆိုင်တဲ့ Code တွေကို သီးသန့် Class Library တစ်ခုအနေနဲ့ ဘာကြောင့်ခွဲထုတ်သင့်သလဲဆိုတာကို အသေးစိတ်ရှင်းပြပါမယ်။ ဒီလိုခွဲထုတ်ခြင်းအားဖြင့် ကျွန်တော်တို့ရဲ့ Project ကို ပိုမိုကောင်းမွန်အောင် ထိန်းသိမ်းနိုင်ပြီး နောက်ပိုင်းတွေမှာ Feature အသစ်တွေထပ်ထည့်တဲ့အခါ အလွန်အဆင်ပြေစေမှာ ဖြစ်ပါတယ်။

---

### The Main Problem We Face

ကျွန်တော်တို့ရဲ့ Database ဟာ အမြဲတမ်းပြောင်းလဲနေနိုင်ပါတယ်။ Column အသစ်တွေထပ်တိုးတာ၊ Table တွေထပ်ထည့်တာမျိုးတွေ လုပ်ရလေ့ရှိပါတယ်။ အဲ့ဒီအခါတိုင်းမှာ ကျွန်တော်တို့ဟာ EF Core ရဲ့ scaffolding command ကို ပြန်ပြီး run ပေးရပါတယ်။

ပြဿနာကတော့ EF Core ကနေ auto-generate လုပ်ပေးထားတဲ့ DbContext class တွေ၊ Model class တွေနဲ့ ကျွန်တော်တို့ရေးထားတဲ့ business logic တွေ၊ service တွေက Project တစ်ခုတည်းမှာ အတူတူရှိနေတာကြောင့်ဖြစ်ပါတယ်။ Scaffolding command ကို ပြန် run လိုက်တဲ့အခါ EF Core က ရှိပြီးသား auto-generated file တွေကို အသစ်ပြန်ရေး overwrite လုပ်ပါတယ်။ အဲ့ဒီမှာ ကျွန်တော်တို့ကိုယ်တိုင် custom ရေးထည့်ထားတဲ့ code တွေ၊ method တွေ ထဲမှာ EFCore နဲ့ ပတ်သက်တဲ့ auto-generate လုပ်ပေးထားတဲ့ Code တွေကို ယူသုံးခဲ့သည်ရှိသော် ဒါမှမဟုတ် အရင်ကရှိခဲ့တဲ့ class တစ်ခုခုကို မလိုအပ်တော့လို့ ဖျက်ပစ်လိုက်တာမျိုးတွေ ဖြစ်တတ်ပါတယ်။ ဒီအခြေအနေမှာ ကျွန်တော်တို့ရဲ့ Project ဟာ build error တွေ ရှိလာနိုင်ပါတယ်။

---

### The Solution: Separating into a Class Library

ဒီပြဿနာကို ဖြေရှင်းဖို့ အကောင်းဆုံးနည်းလမ်းကတော့ **Database Layer** ကို သီးသန့် Class Library project တစ်ခုအနေနဲ့ ခွဲထုတ်လိုက်တာပါပဲ။ ဆိုလိုတာက EF Core က generate လုပ်ပေးတဲ့ DbContext နဲ့ Model class တွေအားလုံးကို အဲ့ဒီ Library အသစ်ထဲမှာပဲ ထားမှာဖြစ်ပါတယ်။

ဒီလိုခွဲထုတ်လိုက်ခြင်းရဲ့ အဓိကကောင်းကျိုးတွေကို ကြည့်လိုက်ရအောင်။

### 1. Easy Maintenance (**ထိန်းသိမ်းရလွယ်ကူခြင်း)**

ကျွန်တော်တို့ရဲ့ Database schema မှာ အပြောင်းအလဲရှိလို့ scaffolding command ကို ပြန် run ရတဲ့အခါ ပြောင်းလဲမှုတွေအားလုံးဟာ သီးသန့်ခွဲထုတ်ထားတဲ့ **Database Library** ထဲမှာပဲ ဖြစ်ပေါ်မှာပါ။ ကျွန်တော်တို့ရဲ့ အဓိက Application Project (ဥပမာ - Web API, MVC) ကို လုံးဝထိခိုက်မှုမရှိတော့ပါဘူး။ **Database Library** တစ်ခုတည်းကိုပဲ update လုပ်ပြီးရင် ကျန်တဲ့ Project တွေက အဲ့ဒီ Library ကို reference လုပ်ပြီး အသုံးပြုရုံပါပဲ။

### 2. Avoiding Build Errors (**Build Error များကို ရှောင်ရှားနိုင်ခြင်း)**

အရင်ကလို Project တစ်ခုတည်းမှာ ရောထွေးမနေတော့တဲ့အတွက် scaffolding command ကြောင့် ကျွန်တော်တို့ရေးထားတဲ့ custom service တွေ၊ business logic တွေ ပျက်စီးသွားမှာကို စိုးရိမ်စရာမလိုတော့ပါဘူး။ **Database Library** မှာ EF Core က generate လုပ်ပေးတဲ့ code တွေသက်သက်ပဲ ရှိသင့်ပါတယ်။ ကျွန်တော်တို့ရဲ့ custom logic တွေကို အဲ့ဒီ Library ထဲမှာ ရေးသားခြင်းမပြုလုပ်ဖို့ လိုအပ်ပါတယ်။ ဒါမှသာ command ကို ဘယ်နှစ်ခါ run run ကျွန်တော်တို့ရဲ့ main project က build error မတက်တော့မှာ ဖြစ်ပါတယ်။

Note

> Database Library မှာ EF Core က generate လုပ်ပေးတဲ့ code တွေပဲ သီးသန့်ရှိနေသင့်ပါတယ်။ ကိုယ်ပိုင် business logic တွေ၊ service တွေကို အဲ့ဒီ Library ထဲမှာ ထပ်ပြီးမရေးသင့်ပါဘူး။
> 

### 3. Better Project Structure [Separation of Concerns] (**ပိုမိုကောင်းမွန်သော Project တည်ဆောက်ပုံ)**

ဒါဟာ Software Engineering မှာ အလွန်အရေးကြီးတဲ့ "Separation of Concerns" ဆိုတဲ့ Rule နဲ့ ကိုက်ညီပါတယ်။ ကျွန်တော်တို့ရဲ့ project မှာ အလွှာ (Layer) တွေ သေသေချာချာခွဲထားလိုက်တာနဲ့တူပါတယ်။

- **Database Layer** (ကျွန်တော်တို့ရဲ့ Class Library အသစ်): သူ့ရဲ့အဓိကတာဝန်က Database နဲ့ ဆက်သွယ်ဖို့၊ Data တွေကို ဖတ်ဖို့၊ ရေးဖို့သက်သက်ပါပဲ။
- **Application Layer** (ကျွန်တော်တို့ရဲ့ Web API/MVC Project): ဒီ Layer ကတော့ business logic တွေ၊ user request တွေကို စီမံခန့်ခွဲဖို့ တာဝန်ယူပါတယ်။ သူက Data လိုအပ်ရင် **Database Layer** ကို လှမ်းခေါ်သုံးပါမယ်။

ဒီလို Layer တွေခွဲထားတဲ့အတွက် Project ဟာ ပိုပြီးရှင်းလင်းတယ်၊ နားလည်ရလွယ်ကူတယ်၊ အဖွဲ့လိုက်လုပ်ဆောင်တဲ့အခါမှာလည်း အလွန်အဆင်ပြေစေပါတယ်။

---

### How We Can Implement This

**ဘယ်လိုစပြီး အကောင်အထည်ဖော်မလဲ**

ကျွန်တော်တို့ရဲ့ Solution ထဲမှာ Class Library Project အသစ်တစ်ခုကို အခုလို ဆောက်ပါမယ်။ ဥပမာအနေနဲ့ `MiniPOS.Database` လို့ နာမည်ပေးလိုက်ပါမယ်။

ပြီးရင် Terminal သို့မဟုတ် Command Prompt ကို `MiniPOS.Database` project ရဲ့ directory မှာဖွင့်ပြီး `dotnet ef` command ကို run ပါမယ်။ ဒီမှာ command နမူနာကို ရေးပြပါမယ်။

```bash
dotnet ef dbcontext scaffold "Server=.;Database=MiniPOS;User ID=sa;Password=sasa@123;TrustServerCertificate=True;" Microsoft.EntityFrameworkCore.SqlServer -o AppDbContextModels -c AppDbContext -f
```

ဒီ command ကို run ပြီးရင် `MiniPOS.Database` project ထဲမှာ `AppDbContextModels` ဆိုတဲ့ **Folder** အသစ်တစ်ခုကို ဆောက်ပေးပါမယ်။ အဲ့ဒီ Folder ထဲမှာ ကျွန်တော်တို့ သတ်မှတ်ထားတဲ့ DbContext class (`-c AppDbContext`) နဲ့ Database table တွေအားလုံးအတွက် Model class တွေကို generate လုပ်ပြီး ထည့်ပေးသွားမှာ ဖြစ်ပါတယ်။ `-f` (force) option ကတော့ ရှိပြီးသား file တွေအပေါ်မှာ အသစ်ထပ်ရေး overwrite လုပ်သွားဖို့ဖြစ်ပါတယ်။

ပြီးရင် ကျွန်တော်တို့ရဲ့ အဓိက Project (ဥပမာ `MiniPOS.WebAPI`) ကနေ `MiniPOS.Database` project ကို reference လုပ်ပြီး `AppDbContext` ကို လှမ်းအသုံးပြုရုံပါပဲ။

ဒီနည်းလမ်းကို အသုံးပြုခြင်းအားဖြင့်၊ နောက်ပိုင်းတွေမှာ Database maintenance လုပ်ရတာ၊ feature အသစ်တွေ ထပ်တိုးရတာတွေက အရင်ကထက် အများကြီးပိုပြီး လွယ်ကူ၊ မြန်ဆန်၊ စိတ်ချရမှာဖြစ်ပါတယ်။