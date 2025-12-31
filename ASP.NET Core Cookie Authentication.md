# ASP.NET Core Cookie Authentication

---

Cookie Authentication ဆိုတာ web applications တွေမှာ user authentication လုပ်ဖို့အတွက် အသုံးအများဆုံးနည်းလမ်းတစ်ခုဖြစ်ပါတယ်။ ဒီစနစ်မှာ server ကနေ user ရဲ့ browser ကို authentication token တစ်ခုကို cookie အဖြစ်ပို့ပေးပြီး နောက်ထပ် request တိုင်းမှာ အဲဒီ cookie ကိုပြန်ပြီး စစ်ဆေးပါတယ်။

**ဘာကြောင့် Cookie Authentication ကိုသုံးကြတာလဲ**

- Stateless HTTP protocol မှာ stateful session တွေကိုထိန်းသိမ်းဖို့
- User ရဲ့ login status ကို browser restart လုပ်လိုက်ရင်တောင် မှတ်မိဖို့
- Security ကောင်းမွန်ပြီး configuration လွယ်ကူတာကြောင့်

---

### Cookie Authentication ဘယ်လိုအလုပ်လုပ်သလဲ

### Authentication Flow

1. **Login Process**
    
    ```
    User Credentials → Server Validation → Create Cookie → Send to Browser
    ```
    
2. **Subsequent Requests**
    
    ```
    Browser Request + Cookie → Server Verification → Access Granted/Denied
    ```
    
3. **Logout Process**
    
    ```
    Logout Request → Server Cookie Invalidation → Browser Cookie Removal
    ```
    

### Cookie ရဲ့ဖွဲ့စည်းပုံ

ကျွန်တော်တို့ရဲ့ application က ထုတ်ပေးတဲ့ authentication cookie မှာ အောက်ပါ information တွေပါဝင်ပါတယ်။

```csharp
// Cookie ထဲမှာပါတဲ့ Claims တွေရဲ့ဥပမာ
{
    "Name": "john_doe",
    "Role": "Admin",
    "SessionId": "abc123-xyz456",
    "Expires": "2024-01-20T10:30:00Z"
}
```

---

### Cookie Authentication ရဲ့ အားသာချက်များ

### Security Benefits

- **HttpOnly Flag** - JavaScript ကနေ cookie ကိုဝင်ရောက်ခွင့်မပေးတာကြောင့် XSS attacks ကနေကာကွယ်ပေးပါတယ်
- **Secure Flag** - HTTPS ကနေပဲ transmit လုပ်တာကြောင့် security ပိုကောင်းပါတယ်
- **SameSite Policy** - CSRF attacks တွေကနေကာကွယ်ပေးပါတယ်

### Development Benefits

- Built-in [ASP.NET](http://asp.net/) Core support ရှိပါတယ်
- Configuration လွယ်ကူပါတယ်
- Scalability ကောင်းမွန်ပါတယ်

---

### Cookie Authentication ကိုဘယ်လိုအသုံးပြုမလဲ

### Basic Setup Steps

**Step 1: Services Configuration**

```csharp
// Program.cs မှာ authentication services တွေကိုထည့်သွင်းပါမယ်
builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.LoginPath = "/Account/Login";          // Login page path
        options.AccessDeniedPath = "/Account/AccessDenied"; // Access denied path
        options.LogoutPath = "/Account/Logout";        // Logout path
        options.ExpireTimeSpan = TimeSpan.FromHours(8); // Cookie expiry time
        options.SlidingExpiration = true;              // Auto-refresh expiry
        options.Cookie.HttpOnly = true;                // Prevent XSS
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always; // HTTPS only
        options.Cookie.SameSite = SameSiteMode.Strict; // CSRF protection
    });
```

**Step 2: Middleware Configuration**

```csharp
// Pipeline မှာ authentication middleware တွေကိုထည့်သွင်းပါမယ်
var app = builder.Build();

app.UseRouting();
app.UseAuthentication();    // Cookie authentication ကိုစစ်ဆေးပါမယ်
app.UseAuthorization();     // User permissions ကိုစစ်ဆေးပါမယ်

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");
```

**Step 3: Controller Implementation**

```csharp
// Login Controller
[HttpPost]
public async Task<IActionResult> Login(LoginViewModel model)
{
    if (ModelState.IsValid)
    {
        // User validation logic ကိုဒီမှာရေးပါမယ်
        if (await ValidateUserAsync(model.Username, model.Password))
        {
            var claims = new List<Claim>
            {
                new Claim(ClaimTypes.Name, model.Username),
                new Claim(ClaimTypes.Role, "User"),
                new Claim("LastLogin", DateTime.UtcNow.ToString())
            };

            var claimsIdentity = new ClaimsIdentity(
                claims, CookieAuthenticationDefaults.AuthenticationScheme);

            var authProperties = new AuthenticationProperties
            {
                IsPersistent = model.RememberMe, // "Remember me" option
                ExpiresUtc = DateTimeOffset.UtcNow.AddHours(8)
            };

            await HttpContext.SignInAsync(
                CookieAuthenticationDefaults.AuthenticationScheme,
                new ClaimsPrincipal(claimsIdentity),
                authProperties);

            return RedirectToAction("Index", "Home");
        }
    }

    ModelState.AddModelError("", "Invalid login attempt");
    return View(model);
}
```

---

### Real-World Usage Scenarios

### E-commerce Website

```csharp
// Shopping cart ကို user session နဲ့ချိတ်ဆက်အသုံးပြုခြင်း
[Authorize]
public async Task<IActionResult> AddToCart(int productId)
{
    var userId = User.FindFirst(ClaimTypes.Name)?.Value;
    // Shopping cart logic ကိုဒီမှာရေးပါမယ်
    return Json(new { success = true });
}
```

### Admin Dashboard

```csharp
// Admin role ရှိတဲ့ user တွေပဲဝင်ရောက်နိုင်တဲ့ area
[Authorize(Roles = "Admin")]
public class AdminController : Controller
{
    public IActionResult Dashboard()
    {
        return View();
    }
}
```

### API Integration

```csharp
// Cookie authentication နဲ့အတူ API calls တွေလုပ်ဆောင်ခြင်း
[Authorize]
public async Task<IActionResult> GetUserProfile()
{
    var username = User.Identity.Name;
    var userData = await _userService.GetUserDataAsync(username);
    return Json(userData);
}
```

---

### Security Considerations

### Best Practices

1. **Always use HTTPS** in production
2. **Set appropriate expiry times** based on application sensitivity
3. **Implement proper logout** functionality
4. **Use secure cookie settings** (HttpOnly, Secure, SameSite)
5. **Regularly update authentication logic**

### Common Security Issues

- Session fixation attacks
- Cross-site request forgery (CSRF)
- Cross-site scripting (XSS)
- Insecure direct object references

ဒီလိုမျိုး security concerns တွေကို ကာကွယ်ဖို့အတွက် ကျွန်တော်တို့ရဲ့ application မှာ proper configuration တွေနဲ့ security headers တွေကိုထည့်သွင်းဖို့လိုအပ်ပါတယ်။