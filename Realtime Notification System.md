# Realtime Notification System with SignalR in ASP.NET Core 8 MVC

### 1. Create the Project

Visual Studio 2022 ကိုဖွင့်ပြီး ASP.NET Core Web App (Model-View-Controller) ကို ရွေးပါမယ်။

ကျွန်တော်တို့ရဲ့ project ကို DotNetTrainingBatch2.RealtimeNotification လို့ နာမည်ပေးပြီး .NET 8.0 ကို ရွေးပါမယ်။

### 2. Add SignalR Client Library

Solution Explorer မှာ ကျွန်တော်တို့ရဲ့ project ကို right-click → Add → Client-Side Library ကို ရွေးပါမယ်။

- **Provider:** unpkg
- **Library:** @microsoft/signalr@latest
- **Choose specific files:** `dist/browser/signalr.js` and `signalr.min.js`
- **Target Location:** `wwwroot/js/signalr/`

ပြီးရင် Install ကို နှိပ်လိုက်ရင် SignalR client library ကို ကျွန်တော်တို့ရဲ့ project ထဲ ထည့်သွင်းပြီးပြီ ဖြစ်ပါတယ်။

### 3. Create the Notification Hub

`Hubs` ဆိုတဲ့ Folder အသစ်တစ်ခု ဆောက်ပြီး `NotificationHub.cs` ဆိုတဲ့ class file ကို အောက်ပါအတိုင်း ရေးပြပါမယ်။

```csharp
using Microsoft.AspNetCore.SignalR;

namespace DotNetTrainingBatch2.RealtimeNotification.Hubs
{
    public class NotificationHub : Hub
    {
    }
}
```

**Note:** ဒီ Hub class က client နဲ့ server ကြားမှာ connection endpoint အဖြစ် কাজလုပ်ပေးမှာ ဖြစ်ပါတယ်။ Message ပို့တဲ့ logic ကိုတော့ ကျွန်တော်တို့ရဲ့ Controller ကနေ ကိုင်တွယ်ပါမယ်။

### 4. Configure SignalR in Program.cs

`Program.cs` file မှာ SignalR service နဲ့ Hub endpoint ကို register လုပ်ဖို့ လိုအပ်ပါတယ်။

```csharp
using DotNetTrainingBatch2.RealtimeNotification.Hubs;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllersWithViews();
// Add SignalR service to the dependency injection container.
builder.Services.AddSignalR();

var app = builder.Build();

app.UseHttpsRedirection();
app.UseStaticFiles();
app.UseRouting();
app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

// Map the NotificationHub to a specific URL endpoint.
app.MapHub<NotificationHub>("/notificationHub");

app.Run();
```

### 5. Create the Announcement Sender UI

Admin ဒါမှမဟုတ် သက်ဆိုင်ရာ user က announcement တွေ ပေးပို့နိုင်ဖို့အတွက် UI နဲ့ Logic ကို ဆောက်ပါမယ်။

a. Create the Controller

Controllers Folder အောက်မှာ AnnouncementController.cs file အသစ်တစ်ခု ဆောက်ပြီး အောက်ပါ code ကို ရေးပြပါမယ်။

```csharp
using DotNetTrainingBatch2.RealtimeNotification.Hubs;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.SignalR;

namespace DotNetTrainingBatch2.RealtimeNotification.Controllers;

public class AnnouncementController : Controller
{
    private readonly IHubContext<NotificationHub> _hubContext;

    public AnnouncementController(IHubContext<NotificationHub> hubContext)
    {
        _hubContext = hubContext;
    }

    public IActionResult Index()
    {
        return View();
    }

    [HttpPost]
    public async Task<IActionResult> Send(AnnouncementRequestModel requestModel)
    {
        await _hubContext.Clients.All.SendAsync("ReceiveAnnouncement", requestModel.Title, requestModel.Content);
        return RedirectToAction("Index");
    }
}

public class AnnouncementRequestModel
{
    public string Title { get; set; }
    public string Content { get; set; }
}
```

**Note:** ဒီ Controller မှာ `IHubContext<NotificationHub>` ကို inject လုပ်ပြီး `Send` action ကနေတစ်ဆင့် ချိတ်ဆက်ထားတဲ့ client တွေအားလုံးကို message ပို့ပေးမှာ ဖြစ်ပါတယ်။

b. Create the View

Views Folder အောက်မှာ Announcement ဆိုတဲ့ Folder အသစ်တစ်ခု ဆောက်ပါမယ်။ အဲ့ဒီ Folder အောက်မှာ Index.cshtml file ကို အောက်ပါ code နဲ့ ရေးပြပါမယ်။

```csharp
<form method="post" action="/Announcement/Send">
    <div class="container">
        <div class="row p-1">
            <div class="col-1">Title</div>
            <div class="col-5"><input type="text" name="Title" class="form-control" /></div>
        </div>
        <div class="row p-1">
            <div class="col-1">Content</div>
            <div class="col-5"><input type="text" class="form-control w-100" name="Content" /></div>
        </div>
        <div class="row p-1">
            <div class="col-6 text-end">
                <input type="submit" class="btn btn-primary" id="sendButton" value="Send Message" />
            </div>
        </div>
    </div>
</form>
```

### 6. Build the Notification Receiver UI

User တွေက realtime notification တွေကို လက်ခံရရှိမယ့် `Home/Index.cshtml` page ကို ပြင်ဆင်ပါမယ်။

`Views/Home/Index.cshtml` file မှာ အောက်ပါ code ကို ရေးပြပါမယ်။

```csharp
@{
    ViewData["Title"] = "Realtime Notifications";
}

<div class="container mt-4">
    <h3>📢 Announcements</h3>
    <div>
        <span id="notificationCount" class="badge bg-danger">0</span>
        <button id="showListBtn" class="btn btn-primary btn-sm">Show Details</button>
    </div>
    <ul id="announcementList" class="list-group mt-3"></ul>
</div>

@section scripts {
    <script src="~/js/signalr/dist/browser/signalr.js"></script>
    <script src="~/js/notification.js"></script>
}
```

### 7. Update the Layout for Scripts Section

Notification receiver UI မှာ ထည့်သွင်းခဲ့တဲ့ `scripts` section ကို render လုပ်နိုင်ဖို့ `Views/Shared/_Layout.cshtml` file ရဲ့ အောက်ဆုံးမှာ `@RenderSectionAsync` ရှိနေဖို့ လိုအပ်ပါတယ်။

```csharp
    ...
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>

    @await RenderSectionAsync("scripts", required: false)
</body>
</html>
```

### 8. Add Client-Side Logic (notification.js)

`wwwroot/js/` Folder အောက်မှာ `notification.js` ဆိုတဲ့ file အသစ်တစ်ခု ဆောက်ပြီး အောက်ပါ client-side logic ကို ရေးပြပါမယ်။

```csharp
"use strict";

var connection = new signalR.HubConnectionBuilder()
    .withUrl("/notificationHub")
    .build();

let count = 0;
let announcements = [];

// This function is called by the server hub to send announcements.
connection.on("ReceiveAnnouncement", function (title, content) {
    count++;
    document.getElementById("notificationCount").textContent = count;

    announcements.push({ title, content });
});

connection.start().catch(function (err) {
    return console.error(err.toString());
});

document.getElementById("showListBtn").addEventListener("click", function () {
    const list = document.getElementById("announcementList");
    list.innerHTML = ""; // Clear previous list

    announcements.forEach(a => {
        const li = document.createElement("li");
        li.className = "list-group-item";
        li.innerHTML = `<strong>${a.title}</strong>: ${a.content}`;
        list.appendChild(li);
    });
});
```

### Final Flow Summary

ကျွန်တော်တို့ရဲ့ system ဘယ်လိုအလုပ်လုပ်လဲဆိုတာကို အကျဉ်းချုပ်အနေနဲ့ ဖော်ပြပေးလိုက်ပါတယ်။

| Step | Description |
| --- | --- |
| **Sender → Server** | Admin က `/Announcement` page ကနေ form ကို submit လုပ်ပြီး `Send` action ကို ခေါ်ပါတယ်။ |
| **Server → Clients** | `AnnouncementController` က `_hubContext` ကိုသုံးပြီး `ReceiveAnnouncement` method နဲ့ client တွေကို ဖြန့်ဝေပါတယ်။ |
| **Notification Count** | Client-side မှာရှိတဲ့ `notification.js` က message ကို လက်ခံရရှိပြီး notification count ကို update လုပ်ပါတယ်။ |
| **Detail List** | User က `Show Details` button ကို နှိပ်လိုက်ရင် ရောက်ရှိလာတဲ့ announcement list ကို ပြပေးပါတယ်။ |

ဒီအဆင့်တွေနဲ့ ကျွန်တော်တို့ရဲ့ realtime notification system ကို အခြေခံပြီး ဆောက်နိုင်ပါပြီ။

နောက်ပိုင်းတွေမှာ user-specific targeting, read/unread status, toast UI integration စတာတွေကို တဖြည်းဖြည်းဖြည့်သွင်းနိုင်ပါတယ်။