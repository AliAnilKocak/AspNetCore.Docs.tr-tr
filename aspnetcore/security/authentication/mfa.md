---
title: ASP.NET Core 'de çok faktörlü kimlik doğrulaması
author: damienbod
description: ASP.NET Core uygulamasında Multi-Factor Authentication (MFA) ayarlamayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: rick-anderson
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/mfa
ms.openlocfilehash: e2f34a72515a700223ce83ce6ec8b55020599ab0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767427"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a>ASP.NET Core 'de çok faktörlü kimlik doğrulaması

, [Davmien Bowden](https://github.com/damienbod) tarafından

Multi-Factor Authentication (MFA), bir kullanıcının ek tanımlama biçimleri için oturum açma olayında istediği bir işlemdir. Bu istem, bir Cellphone 'dan bir kod girmek, FIDO2 anahtarı kullanmak veya parmak izi taraması sağlamak olabilir. İkinci bir kimlik doğrulama formu gerektirdiğinde güvenlik geliştirilmiştir. Ek faktör bir saldırgan tarafından kolayca alınıp çoğaltılamaz.

Bu makalede aşağıdaki alanlarda yer verilmiştir:

* MFA nedir ve MFA akışlarının kullanılması önerilir
* ASP.NET Core kullanarak yönetim sayfaları için MFA 'yı yapılandırmaIdentity
* OpenID Connect sunucusuna MFA oturum açma gereksinimi gönderin
* ASP.NET Core OpenID Connect istemcisini MFA gerektir olarak zorla

## <a name="mfa-2fa"></a>MFA, 2FA

MFA, bildiğiniz bir şey, sahip olduğunuz bir şey veya kullanıcının kimlik doğrulaması için Biyometri doğrulaması gibi bir kimlik için en az iki veya daha fazla kanıt türü gerektirir.

İki öğeli kimlik doğrulaması (2FA) MFA 'nın bir alt kümesi gibidir, ancak MFA 'nın kimliği kanıtlamak için iki veya daha fazla faktör gerektirmesine yönelik fark.

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a>MFA TOTP (zamana bağlı bir kerelik parola algoritması)

TOTP kullanarak MFA, ASP.NET Core Identitykullanarak desteklenen bir uygulama. Bu, aşağıdakiler dahil olmak üzere tüm uyumlu Doğrulayıcı uygulamaları ile birlikte kullanılabilir:

* Microsoft Authenticator uygulaması
* Google Authenticator uygulaması

Uygulama ayrıntıları için aşağıdaki bağlantıya bakın:

[ASP.NET Core 'daki TOTP Authenticator uygulamaları için QR kodu oluşturmayı etkinleştirme](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a>MFA FIDO2 veya passwordless

FIDO2 Şu anda:

* MFA elde etmenin en güvenli yolu.
* Sızdırma saldırılarına karşı koruyan tek MFA akışı.

Mevcut olduğunda ASP.NET Core doğrudan FIDO2 desteklemez. FIDO2, MFA veya parolasız akışlar için kullanılabilir.

Azure Active Directory, FIDO2 ve parolasız akışlar için destek sağlar. Daha fazla bilgi için bkz. [Azure Active Directory Için passwordless kimlik doğrulama seçenekleri](/azure/active-directory/authentication/concept-authentication-passwordless).

### <a name="mfa-sms"></a>MFA SMS

SMS ile MFA, parola kimlik doğrulaması (tek etken) ile karşılaştırıldığında güvenlik çarpanını büyük ölçüde artırır. Ancak, SMS 'nin ikinci bir faktör olarak kullanılması artık önerilmez. Bu tür bir uygulama için çok sayıda bilinen saldırı vektörü var.

[NıST yönergeleri](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-aspnet-core-identity"></a>ASP.NET Core kullanarak yönetim sayfaları için MFA 'yı yapılandırmaIdentity

MFA, kullanıcıların bir ASP.NET Core Identity uygulaması içindeki hassas sayfalara erişmelerine zorlanabilir. Bu, farklı kimlikler için farklı erişim düzeylerinin bulunduğu uygulamalar için yararlı olabilir. Örneğin, kullanıcılar bir parola oturum açma kullanarak profil verilerini görüntüleyebilir, ancak yönetim sayfalarına erişmek için bir yöneticinin MFA 'yı kullanması gerekir.

### <a name="extend-the-login-with-an-mfa-claim"></a>Bir MFA talebi ile oturum açmayı genişletme

Tanıtım kodu, ve Identity Razor sayfaları ile ASP.NET Core kullanarak ayarlanır. Yöntemi bir yerine kullanılır, bu nedenle başarılı bir oturum `IUserClaimsPrincipalFactory` açma işleminden sonra kimliğe talep eklemek için bir uygulama kullanılabilir. `AddDefaultIdentity` `AddIdentity`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

`AdditionalUserClaimsPrincipalFactory` Sınıfı, `amr` talebi yalnızca başarılı bir oturum açma işleminden sonra Kullanıcı taleplerine ekler. Talebin değeri veritabanından okundu. Talep buraya eklenir, çünkü kimlik MFA ile oturum açtıysa Kullanıcı yalnızca daha yüksek korumalı görünüme erişir. Veritabanı görünümü talebi kullanmak yerine doğrudan veritabanından okunduktan sonra MFA 'yı etkinleştirdikten sonra doğrudan MFA olmadan görünüme erişebilirsiniz.

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

Identity Hizmet kurulumu `Startup` sınıfında değiştiği için, güncelleştirilmeleri Identity gereken düzenler. Identity Sayfaları uygulamaya dönüştürün. */Account/Manage/_Layout. cshtml dosyasında düzeni tanımlayın. Identity*

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

Ayrıca, Identity sayfalardaki tüm yönetim sayfalarını için Düzen ata:

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a>Yönetim sayfasında MFA gereksinimini doğrulama

Yönetim Razor sayfası, kullanıcının MFA kullanarak oturum açtığını doğrular. `OnGet` Yönteminde, kimlik Kullanıcı taleplerine erişmek için kullanılır. `amr` Talep değer `mfa`için denetlenir. Kimliğin bu talebi yoksa veya bu talep yoksa `false`, sayfa MFA 'yı etkinleştir sayfasına yeniden yönlendirir. Bu, Kullanıcı zaten MFA olmadan oturum açtığından, bu mümkündür.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a>Kullanıcı oturum açma bilgilerini değiştirmek için UI mantığı

Başlatma sırasında bir yetkilendirme ilkesi eklenmiştir. İlke, bu değere `amr` `mfa`sahip talep gerektiriyor.

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

Bu ilke daha sonra, **yönetici** menüsünü Uyarı `_Layout` ile göstermek veya gizlemek için görünümünde kullanılabilir:

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

Kimlik MFA kullanılarak oturum açtıysa, **yönetici** menüsü araç ipucu uyarısı olmadan görüntülenir. Kullanıcı MFA olmadan oturum açtığında, **yönetici (etkin değil)** menüsü, kullanıcıya bildiren araç ipucuyla birlikte görüntülenir (uyarı hakkında).

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

Kullanıcı MFA olmadan oturum açarsa, uyarı görüntülenir:

![Yönetici MFA kimlik doğrulaması](mfa/_static/identitystandalonemfa_01.png)

**Yönetici** bağlantısına tıklarken Kullanıcı MFA etkinleştirme görünümüne yönlendirilir:

![Yönetici MFA kimlik doğrulamasını etkinleştirir](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a>OpenID Connect sunucusuna MFA oturum açma gereksinimi gönderin 

`acr_values` Parametresi, `mfa` gerekli değeri istemciden bir kimlik doğrulaması isteğindeki sunucuya geçirmek için kullanılabilir.

> [!NOTE]
> Bunun `acr_values` çalışması için, PARAMETRENIN açık kimlik Connect sunucusunda işlenmesi gerekir.

### <a name="openid-connect-aspnet-core-client"></a>OpenID Connect ASP.NET Core istemcisi

ASP.NET Core Razor sayfaları açma kimliği Connect istemci uygulaması, açık kimlik `AddOpenIdConnect` Connect sunucusunda oturum açmak için yöntemini kullanır. `acr_values` Parametresi `mfa` değeri ile ayarlanır ve kimlik doğrulama isteğiyle birlikte gönderilir. `OpenIdConnectEvents` Bunu eklemek için kullanılır.

Önerilen `acr_values` parametre değerleri için bkz. [kimlik doğrulama yöntemi başvuru değerleri](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-identityserver-4-server-with-aspnet-core-identity"></a>Örnek OpenID Connect IdentityServer 4 sunucusu ASP.NET CoreIdentity

MVC görünümleriyle ASP.NET Core Identity kullanılarak uygulanan OpenID Connect sunucusunda, *ErrorEnable2FA. cshtml* adlı yeni bir görünüm oluşturulur. Görünüm:

* , MFA gerektiren Identity bir uygulamadan geliyorsa, ancak Kullanıcı ' de Identitybunu etkinleştirmediğinde görüntüler.
* Kullanıcıya bildirir ve bunu etkinleştirmek için bir bağlantı ekler.

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

`Login` Yönteminde, `IIdentityServerInteractionService` arabirim uygulama `_interaction` açık kimlik Connect istek parametrelerine erişmek için kullanılır. `acr_values` Parametresi, `AcrValues` özelliği kullanılarak erişilir. İstemci bu `mfa` ayarı kümesiyle gönderdiğinden, bu daha sonra denetlenebilir.

MFA gerekliyse ve ASP.NET Core Identity IÇINDEKI Kullanıcı MFA 'yı etkinleştirmişse, oturum açma işlemi devam eder. Kullanıcının MFA özelliği etkin olmadığında, Kullanıcı *ErrorEnable2FA. cshtml*özel görünümüne yönlendirilir. Ardından, Identity kullanıcıyı ' de imzalar ASP.NET Core.

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

`ExternalLoginCallback` Yöntemi yerel Identity oturum açma gibi çalışmaktadır. `AcrValues` Özelliği `mfa` değeri için denetlenir. `mfa` Değer varsa,, oturum açma tamamlanmadan önce MFA zorlanır (örneğin, `ErrorEnable2FA` görünüme yeniden yönlendirilir).

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

Kullanıcı zaten oturum açmışsa istemci uygulaması:

* `amr` Talebi hala doğrular.
* MFA 'yı ASP.NET Core Identity görünümüne yönelik bir bağlantıyla ayarlayabilir.

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a>ASP.NET Core OpenID Connect istemcisini MFA gerektir olarak zorla

Bu örnek, OpenID Connect Razor kullanan bir ASP.NET Core sayfası uygulamasının, kullanıcıların MFA kullanarak kimlik doğrulamasından sahip olduğunu gerektirdiğini gösterir.

MFA gereksinimini doğrulamak için bir `IAuthorizationRequirement` gereksinim oluşturulur. Bu, MFA gerektiren bir ilke kullanılarak sayfalara eklenecektir.

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

`AuthorizationHandler` , `amr` Talebi kullanacak ve değeri `mfa`kontrol edecek şekilde uygulanır. `amr` , Başarılı bir kimlik doğrulaması `id_token` Içinde döndürülür ve [kimlik doğrulama yöntemi başvuru değerleri](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) belirtiminde tanımlanan birçok farklı değere sahip olabilir.

Döndürülen değer, kimliğin kimlik doğrulamasının nasıl yapıldığını ve açık KIMLIK sunucu uygulamasında bir bağlantı olduğunu gösterir.

, `AuthorizationHandler` `RequireMfa` Gereksinimi kullanır ve `amr` talebi doğrular. OpenID Connect sunucusu, ASP.NET Core Identityile ıdentityserver4 kullanılarak uygulanabilir. Bir Kullanıcı TOTP kullanarak oturum açtığında, `amr` talep bir MFA değeri ile döndürülür. Farklı bir OpenID Connect sunucu uygulamasının veya farklı bir MFA türünün kullanılması durumunda `amr` talep ya da olabilir, farklı bir değere sahip olur. Bu kod de kabul etmek için genişletilmelidir.

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

`Startup.ConfigureServices` Yönteminde, `AddOpenIdConnect` yöntemi varsayılan zorluk düzeni olarak kullanılır. `amr` Talebi denetlemek için kullanılan yetkilendirme Işleyicisi, Denetim kapsayıcısının INVERSION öğesine eklenir. Daha sonra, `RequireMfa` gereksinimi ekleyen bir ilke oluşturulur.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

Bu ilke daha sonra Razor sayfada gerektiği şekilde kullanılır. İlke, tüm uygulama için genel olarak eklenebilir.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

Kullanıcı MFA olmadan kimlik doğrulaması gerçekleştiriyorsa, `amr` talep muhtemelen bir `pwd` değere sahip olur. İsteğin sayfaya erişim yetkisi yok. Varsayılan değerleri kullanarak, Kullanıcı *Hesap/Accessreddedildi* sayfasına yönlendirilir. Bu davranış değiştirilebilir veya kendi özel mantığınızı buradan uygulayabilirsiniz. Bu örnekte, geçerli kullanıcının, hesabı için MFA ayarlayabilmesi için bir bağlantı eklenir.

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

Artık yalnızca MFA ile kimlik doğrulaması yapan kullanıcılar sayfaya veya Web sitesine erişebilir. Farklı MFA türleri kullanılıyorsa veya 2FA tamam ise, `amr` talebin farklı değerleri olur ve doğru şekilde işlenmesi gerekir. Farklı açık KIMLIK Connect sunucuları Ayrıca bu talep için farklı değerler döndürür ve [kimlik doğrulama yöntemi başvuru değerleri](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) belirtimini izlemeyebilir.

MFA olmadan oturum açarken (örneğin, yalnızca bir parola kullanarak):

* , `amr` Şu `pwd` değere sahiptir:

    ![require_mfa_oidc_02. png](mfa/_static/require_mfa_oidc_02.png)

* Erişim reddedildi:

    ![require_mfa_oidc_03. png](mfa/_static/require_mfa_oidc_03.png)

Alternatif olarak, ile IdentityOTP kullanarak oturum açma:

![require_mfa_oidc_01. png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core 'daki TOTP Authenticator uygulamaları için QR kodu oluşturmayı etkinleştirme](xref:security/authentication/identity-enable-qrcodes)
* [Azure Active Directory için passwordless kimlik doğrulama seçenekleri](/azure/active-directory/authentication/concept-authentication-passwordless)
* [.NET kullanarak FIDO2/WebAuthn kanıtlama ve onaylama için FIDO2 .NET kitaplığı](https://github.com/abergs/fido2-net-lib)
* [WebAuthn başar](https://github.com/herrjemand/awesome-webauthn)
