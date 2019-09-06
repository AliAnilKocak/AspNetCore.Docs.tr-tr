---
title: ASP.NET Core kimliği olmadan tanımlama bilgisi kimlik doğrulaması kullanma
author: rick-anderson
description: ASP.NET Core kimliği olmadan tanımlama bilgisi kimlik doğrulamasını nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/20/2019
uid: security/authentication/cookie
ms.openlocfilehash: 76c7fc20c8870668ca7c65d975e2ed59f40f7dc8
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384824"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>ASP.NET Core kimliği olmadan tanımlama bilgisi kimlik doğrulaması kullanma

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core kimlik, oturum açma işlemleri oluşturmaya ve korumaya yönelik eksiksiz, tam özellikli bir kimlik doğrulama sağlayıcısıdır. Ancak, ASP.NET Core kimliği olmayan tanımlama bilgisi tabanlı kimlik doğrulama sağlayıcısı kullanılabilir. Daha fazla bilgi için bkz. <xref:security/authentication/identity>.

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))

Örnek uygulamadaki tanıtım amacıyla, Maria Rodriguez olan kuramsal kullanıcının Kullanıcı hesabı, uygulamaya sabit olarak kodlanmıştır. Kullanıcı oturumu açmak için `maria.rodriguez@contoso.com` **e-posta** adresini ve parolayı kullanın. Kullanıcının kimliği, `AuthenticateUser` *Sayfalar/Account/Login. cshtml. cs* dosyasındaki yönteminde doğrulanır. Gerçek dünyada bir örnekte, kullanıcının kimliği bir veritabanında doğrulanır.

## <a name="configuration"></a>Yapılandırma

Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmıyorsa, [Microsoft. Aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) paketi için proje dosyasında bir paket başvurusu oluşturun.

Yönteminde, <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ve<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> yöntemleriyle kimlik doğrulama ara yazılım hizmetlerini oluşturun: `Startup.ConfigureServices`

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>geçildi, uygulamanın varsayılan kimlik doğrulama şemasını ayarlar.`AddAuthentication` `AuthenticationScheme`birden fazla tanımlama bilgisi kimlik doğrulaması örneği olduğunda ve [belirli bir şemayla yetkilendirmek](xref:security/authorization/limitingidentitybyscheme)istediğinizde yararlıdır. ' In [ıeauthenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) olarakayarlanması,şemaiçinbir"tanımlamabilgileri"değerisağlar.`AuthenticationScheme` Düzeni ayıran herhangi bir dize değeri sağlayabilirsiniz.

Uygulamanın kimlik doğrulama düzeni, uygulamanın tanımlama bilgisi kimlik doğrulama düzeninden farklıdır. İçin <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>bir tanımlama bilgisi kimlik doğrulama düzeni sağlanmazsa, (" `CookieAuthenticationDefaults.AuthenticationScheme` Cookies") kullanır.

Kimlik doğrulama tanımlama bilgisinin <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> özelliği varsayılan olarak olarak `true` ayarlanır. Bir site ziyaretçisi veri toplamaya onay vermemişse kimlik doğrulama tanımlama bilgilerine izin verilir. Daha fazla bilgi için bkz. <xref:security/gdpr#essential-cookies>.

İçinde `Startup.Configure` `UseAuthentication` , özelliğiayarlamak`HttpContext.User` ve istekler için yetkilendirme ara yazılımını çalıştırmak `UseAuthorization` için öğesini çağırın. `UseAuthentication` Çağrılmadan `UseAuthorization` önce ve yöntemlerini çağırın: `UseEndpoints`

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Sınıfı, kimlik doğrulama sağlayıcısı seçeneklerini yapılandırmak için kullanılır.

`Startup.ConfigureServices` Yönteminde `CookieAuthenticationOptions` kimlik doğrulaması için hizmet yapılandırmasında ayarlanır:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Tanımlama bilgisi Ilkesi ara yazılımı

[Tanımlama bilgisi Ilkesi ara yazılımı](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) , tanımlama bilgisi İlkesi yeteneklerini sunar. Ara yazılımı uygulama işleme işlem hattına eklemek,&mdash;yalnızca işlem hattına kaydedilen aşağı akış bileşenlerini etkiler.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Tanımlama bilgisi işlemenin genel özelliklerini denetlemek için tanımlama bilgisi İlkesi ara yazılımı, tanımlama bilgileri eklenmiş veya silinmiş olduğunda tanımlama bilgisi işleme işleyicilerine kanca olarak sunulur.<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>

Varsayılan <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> değer OAuth2 kimlik `SameSiteMode.Lax` doğrulamasına izin vermek için kullanılır. Aynı site ilkesini `SameSiteMode.Strict`kesinlikle zorlamak için, öğesini `MinimumSameSitePolicy`ayarlayın. Bu ayar, OAuth2 ve diğer çapraz kaynak kimlik doğrulama düzenlerini kesse de, çıkış noktaları arası istek işlemeye bağlı olmayan diğer uygulama türleri için tanımlama bilgisi güvenlik düzeyini yükseltir.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

İçin `MinimumSameSitePolicy` tanımlama bilgisi İlkesi ara yazılımı ayarı, aşağıdaki matriye `Cookie.SameSite` göre `CookieAuthenticationOptions` ayarlar ' ın ayarını etkileyebilir.

| MinimumSameSitePolicy | Cookie. SameSite | Sonuç tanımlama bilgisi. SameSite ayarı |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode. None     | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict |
| SameSiteMode. LAX      | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. LAX<br>SameSiteMode. LAX<br>SameSiteMode. Strict |
| SameSiteMode. Strict   | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. Strict<br>SameSiteMode. Strict<br>SameSiteMode. Strict |

## <a name="create-an-authentication-cookie"></a>Kimlik doğrulama tanımlama bilgisi oluşturma

Kullanıcı bilgilerini tutan bir tanımlama bilgisi oluşturmak için, oluşturun <xref:System.Security.Claims.ClaimsPrincipal>. Kullanıcı bilgileri serileştirilir ve tanımlama bilgisinde depolanır. 

Gerekli <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> <xref:System.Security.Claims.ClaimsIdentity> tümöğeleriiçerenbiroluşturunveKullanıcıoturumunu<xref:System.Security.Claims.Claim>açmak için çağırın:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`şifreli bir tanımlama bilgisi oluşturur ve geçerli yanıta ekler. `AuthenticationScheme` Belirtilmemişse, varsayılan düzen kullanılır.

ASP.NET Core [veri koruma](xref:security/data-protection/using-data-protection) sistemi şifreleme için kullanılır. Birden çok makinede barındırılan bir uygulama, uygulamalar arasında yük dengeleme veya bir Web grubu kullanma için, [veri korumayı](xref:security/data-protection/configuration/overview) aynı anahtar halkasını ve uygulama tanımlayıcısını kullanacak şekilde yapılandırın.

## <a name="sign-out"></a>Oturumu kapat

Geçerli kullanıcının oturumunu kapatmak ve tanımlama bilgilerini silmek için şunu arayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

`CookieAuthenticationDefaults.AuthenticationScheme` (Veya "Cookies"), düzen olarak (örneğin, "contosocookie") kullanılmazsa, kimlik doğrulama sağlayıcısını yapılandırırken kullanılan düzeni sağlayın. Aksi takdirde, varsayılan düzen kullanılır.

## <a name="react-to-back-end-changes"></a>Arka uç değişikliklerine tepki verme

Tanımlama bilgisi oluşturulduktan sonra tanımlama bilgisi tek kimlik kaynağıdır. Arka uç sistemlerinde bir kullanıcı hesabı devre dışı bırakılmışsa:

* Uygulamanın tanımlama bilgisi kimlik doğrulama sistemi, kimlik doğrulama tanımlama bilgisine göre istekleri işlemeye devam eder.
* Kimlik doğrulama tanımlama bilgisi geçerli olduğu sürece kullanıcı uygulamada oturum açmış durumda kalır.

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> Olay, tanımlama bilgisi kimliği doğrulamasını ele almak ve geçersiz kılmak için kullanılabilir. Her istekte tanımlama bilgisinin doğrulanması, uygulamaya erişen kullanıcıların iptal edilmesinin riskini azaltır.

Tanımlama bilgisi doğrulamasına yönelik bir yaklaşım, kullanıcı veritabanının değiştiği zaman izlemenin izlenmesine bağlıdır. Kullanıcının tanımlama bilgisi verildikten sonra veritabanı değiştirilmediyse, tanımlama bilgisi hala geçerliyse kullanıcının kimliğini yeniden doğrulamaya gerek yoktur. Örnek uygulamada, veritabanı ' de `IUserRepository` uygulanır ve bir `LastChanged` değer depolar. Veritabanında bir Kullanıcı güncellenmiştir, `LastChanged` değer geçerli saate ayarlanır.

Veritabanı `LastChanged` değere göre değiştiğinde bir tanımlama bilgisini geçersiz kılmak için, veritabanından geçerli `LastChanged` değeri içeren bir `LastChanged` talep ile tanımlama bilgisini oluşturun:

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

`ValidatePrincipal` Olay için bir geçersiz kılma uygulamak üzere, aşağıdakilerden <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>türetilen bir sınıfa aşağıdaki imzaya sahip bir yöntem yazın:

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Aşağıda örnek bir uygulama `CookieAuthenticationEvents`verilmiştir:

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

`Startup.ConfigureServices` Yöntemine tanımlama bilgisi hizmeti kaydı sırasında olay örneğini kaydedin. `CustomCookieAuthenticationEvents` Sınıfınız için [kapsamlı bir hizmet kaydı](xref:fundamentals/dependency-injection#service-lifetimes) sağlayın:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Kullanıcı adının, güvenliği hiçbir şekilde etkilemeyen bir kararı güncelleştirmediği&mdash;bir durum düşünün. Kullanıcı sorumlusunu kalıcı olarak güncelleştirmek istiyorsanız, çağırın `context.ReplacePrincipal` ve `context.ShouldRenew` özelliğini olarak `true`ayarlayın.

> [!WARNING]
> Burada açıklanan yaklaşım her istekte tetiklenir. Her istekteki tüm kullanıcılar için kimlik doğrulama tanımlama bilgilerinin doğrulanması, uygulama için büyük bir performans cezası oluşmasına neden olabilir.

## <a name="persistent-cookies"></a>Kalıcı tanımlama bilgileri

Tanımlama bilgisinin tarayıcı oturumları arasında kalıcı olmasını isteyebilirsiniz. Bu Kalıcılık yalnızca oturum açma veya benzer bir mekanizmanın "Beni anımsa" onay kutusu ile açık kullanıcı onayı ile etkinleştirilmelidir. 

Aşağıdaki kod parçacığı, tarayıcı kapanışları aracılığıyla ilerlikli bir kimlik ve ilgili tanımlama bilgisi oluşturur. Daha önce yapılandırılmış tüm Kayan süre sonu ayarları kabul edilir. Tarayıcı kapalıyken tanımlama bilgisinin süresi dolarsa tarayıcı, yeniden başlatıldıktan sonra tanımlama bilgisini temizler.

Şu <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> şekilde`true` ayarlayın :<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>Mutlak tanımlama bilgisi süre sonu

Mutlak bir sona erme saati ile <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>ayarlanabilir. Kalıcı tanımlama bilgisi `IsPersistent` oluşturmak için de ayarlanmalıdır. Aksi takdirde, tanımlama bilgisi oturum tabanlı bir yaşam süresi ile oluşturulur ve bu kimlik doğrulama biletinden önce ya da sonra zaman alabilir. , Ayarlandığında, <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> seçeneğinin<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>değerinigeçersizkılar. `ExpiresUtc`

Aşağıdaki kod parçacığı, 20 dakika boyunca bir kimlik ve karşılık gelen tanımlama bilgisi oluşturur. Bu, daha önce yapılandırılmış olan tüm Kayan süre sonu ayarlarını yoksayar.

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core kimlik, oturum açma işlemleri oluşturmaya ve korumaya yönelik eksiksiz, tam özellikli bir kimlik doğrulama sağlayıcısıdır. Ancak, ASP.NET Core kimliği olmayan tanımlama bilgisi tabanlı kimlik doğrulama sağlayıcısı kullanılabilir. Daha fazla bilgi için bkz. <xref:security/authentication/identity>.

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))

Örnek uygulamadaki tanıtım amacıyla, Maria Rodriguez olan kuramsal kullanıcının Kullanıcı hesabı, uygulamaya sabit olarak kodlanmıştır. Kullanıcı oturumu açmak için `maria.rodriguez@contoso.com` **e-posta** adresini ve parolayı kullanın. Kullanıcının kimliği, `AuthenticateUser` *Sayfalar/Account/Login. cshtml. cs* dosyasındaki yönteminde doğrulanır. Gerçek dünyada bir örnekte, kullanıcının kimliği bir veritabanında doğrulanır.

## <a name="configuration"></a>Yapılandırma

Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmıyorsa, [Microsoft. Aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) paketi için proje dosyasında bir paket başvurusu oluşturun.

Yönteminde, <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ve<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> yöntemleriyle kimlik doğrulama ara yazılım hizmetini oluşturun: `Startup.ConfigureServices`

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>geçildi, uygulamanın varsayılan kimlik doğrulama şemasını ayarlar.`AddAuthentication` `AuthenticationScheme`birden fazla tanımlama bilgisi kimlik doğrulaması örneği olduğunda ve [belirli bir şemayla yetkilendirmek](xref:security/authorization/limitingidentitybyscheme)istediğinizde yararlıdır. ' In [ıeauthenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) olarakayarlanması,şemaiçinbir"tanımlamabilgileri"değerisağlar.`AuthenticationScheme` Düzeni ayıran herhangi bir dize değeri sağlayabilirsiniz.

Uygulamanın kimlik doğrulama düzeni, uygulamanın tanımlama bilgisi kimlik doğrulama düzeninden farklıdır. İçin <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>bir tanımlama bilgisi kimlik doğrulama düzeni sağlanmazsa, (" `CookieAuthenticationDefaults.AuthenticationScheme` Cookies") kullanır.

Kimlik doğrulama tanımlama bilgisinin <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> özelliği varsayılan olarak olarak `true` ayarlanır. Bir site ziyaretçisi veri toplamaya onay vermemişse kimlik doğrulama tanımlama bilgilerine izin verilir. Daha fazla bilgi için bkz. <xref:security/gdpr#essential-cookies>.

Yönteminde, özelliğini ayarlayan kimlik doğrulama `UseAuthentication` ara yazılımını çağırmak için yöntemini çağırın. `HttpContext.User` `Startup.Configure` `UseAuthentication` Veya `UseMvcWithDefaultRoute` çağrılmadan önceyöntemiçağırın:`UseMvc`

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Sınıfı, kimlik doğrulama sağlayıcısı seçeneklerini yapılandırmak için kullanılır.

`Startup.ConfigureServices` Yönteminde `CookieAuthenticationOptions` kimlik doğrulaması için hizmet yapılandırmasında ayarlanır:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Tanımlama bilgisi Ilkesi ara yazılımı

[Tanımlama bilgisi Ilkesi ara yazılımı](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) , tanımlama bilgisi İlkesi yeteneklerini sunar. Ara yazılımı uygulama işleme işlem hattına eklemek,&mdash;yalnızca işlem hattına kaydedilen aşağı akış bileşenlerini etkiler.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Tanımlama bilgisi işlemenin genel özelliklerini denetlemek için tanımlama bilgisi İlkesi ara yazılımı, tanımlama bilgileri eklenmiş veya silinmiş olduğunda tanımlama bilgisi işleme işleyicilerine kanca olarak sunulur.<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>

Varsayılan <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> değer OAuth2 kimlik `SameSiteMode.Lax` doğrulamasına izin vermek için kullanılır. Aynı site ilkesini `SameSiteMode.Strict`kesinlikle zorlamak için, öğesini `MinimumSameSitePolicy`ayarlayın. Bu ayar, OAuth2 ve diğer çapraz kaynak kimlik doğrulama düzenlerini kesse de, çıkış noktaları arası istek işlemeye bağlı olmayan diğer uygulama türleri için tanımlama bilgisi güvenlik düzeyini yükseltir.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

İçin `MinimumSameSitePolicy` tanımlama bilgisi İlkesi ara yazılımı ayarı, aşağıdaki matriye `Cookie.SameSite` göre `CookieAuthenticationOptions` ayarlar ' ın ayarını etkileyebilir.

| MinimumSameSitePolicy | Cookie. SameSite | Sonuç tanımlama bilgisi. SameSite ayarı |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode. None     | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict |
| SameSiteMode. LAX      | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. LAX<br>SameSiteMode. LAX<br>SameSiteMode. Strict |
| SameSiteMode. Strict   | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. Strict<br>SameSiteMode. Strict<br>SameSiteMode. Strict |

## <a name="create-an-authentication-cookie"></a>Kimlik doğrulama tanımlama bilgisi oluşturma

Kullanıcı bilgilerini tutan bir tanımlama bilgisi oluşturmak için, oluşturun <xref:System.Security.Claims.ClaimsPrincipal>. Kullanıcı bilgileri serileştirilir ve tanımlama bilgisinde depolanır. 

Gerekli <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> <xref:System.Security.Claims.ClaimsIdentity> tümöğeleriiçerenbiroluşturunveKullanıcıoturumunu<xref:System.Security.Claims.Claim>açmak için çağırın:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`şifreli bir tanımlama bilgisi oluşturur ve geçerli yanıta ekler. `AuthenticationScheme` Belirtilmemişse, varsayılan düzen kullanılır.

ASP.NET Core [veri koruma](xref:security/data-protection/using-data-protection) sistemi şifreleme için kullanılır. Birden çok makinede barındırılan bir uygulama, uygulamalar arasında yük dengeleme veya bir Web grubu kullanma için, [veri korumayı](xref:security/data-protection/configuration/overview) aynı anahtar halkasını ve uygulama tanımlayıcısını kullanacak şekilde yapılandırın.

## <a name="sign-out"></a>Oturumu kapat

Geçerli kullanıcının oturumunu kapatmak ve tanımlama bilgilerini silmek için şunu arayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

`CookieAuthenticationDefaults.AuthenticationScheme` (Veya "Cookies"), düzen olarak (örneğin, "contosocookie") kullanılmazsa, kimlik doğrulama sağlayıcısını yapılandırırken kullanılan düzeni sağlayın. Aksi takdirde, varsayılan düzen kullanılır.

## <a name="react-to-back-end-changes"></a>Arka uç değişikliklerine tepki verme

Tanımlama bilgisi oluşturulduktan sonra tanımlama bilgisi tek kimlik kaynağıdır. Arka uç sistemlerinde bir kullanıcı hesabı devre dışı bırakılmışsa:

* Uygulamanın tanımlama bilgisi kimlik doğrulama sistemi, kimlik doğrulama tanımlama bilgisine göre istekleri işlemeye devam eder.
* Kimlik doğrulama tanımlama bilgisi geçerli olduğu sürece kullanıcı uygulamada oturum açmış durumda kalır.

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> Olay, tanımlama bilgisi kimliği doğrulamasını ele almak ve geçersiz kılmak için kullanılabilir. Her istekte tanımlama bilgisinin doğrulanması, uygulamaya erişen kullanıcıların iptal edilmesinin riskini azaltır.

Tanımlama bilgisi doğrulamasına yönelik bir yaklaşım, kullanıcı veritabanının değiştiği zaman izlemenin izlenmesine bağlıdır. Kullanıcının tanımlama bilgisi verildikten sonra veritabanı değiştirilmediyse, tanımlama bilgisi hala geçerliyse kullanıcının kimliğini yeniden doğrulamaya gerek yoktur. Örnek uygulamada, veritabanı ' de `IUserRepository` uygulanır ve bir `LastChanged` değer depolar. Veritabanında bir Kullanıcı güncellenmiştir, `LastChanged` değer geçerli saate ayarlanır.

Veritabanı `LastChanged` değere göre değiştiğinde bir tanımlama bilgisini geçersiz kılmak için, veritabanından geçerli `LastChanged` değeri içeren bir `LastChanged` talep ile tanımlama bilgisini oluşturun:

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

`ValidatePrincipal` Olay için bir geçersiz kılma uygulamak üzere, aşağıdakilerden <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>türetilen bir sınıfa aşağıdaki imzaya sahip bir yöntem yazın:

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Aşağıda örnek bir uygulama `CookieAuthenticationEvents`verilmiştir:

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

`Startup.ConfigureServices` Yöntemine tanımlama bilgisi hizmeti kaydı sırasında olay örneğini kaydedin. `CustomCookieAuthenticationEvents` Sınıfınız için [kapsamlı bir hizmet kaydı](xref:fundamentals/dependency-injection#service-lifetimes) sağlayın:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Kullanıcı adının, güvenliği hiçbir şekilde etkilemeyen bir kararı güncelleştirmediği&mdash;bir durum düşünün. Kullanıcı sorumlusunu kalıcı olarak güncelleştirmek istiyorsanız, çağırın `context.ReplacePrincipal` ve `context.ShouldRenew` özelliğini olarak `true`ayarlayın.

> [!WARNING]
> Burada açıklanan yaklaşım her istekte tetiklenir. Her istekteki tüm kullanıcılar için kimlik doğrulama tanımlama bilgilerinin doğrulanması, uygulama için büyük bir performans cezası oluşmasına neden olabilir.

## <a name="persistent-cookies"></a>Kalıcı tanımlama bilgileri

Tanımlama bilgisinin tarayıcı oturumları arasında kalıcı olmasını isteyebilirsiniz. Bu Kalıcılık yalnızca oturum açma veya benzer bir mekanizmanın "Beni anımsa" onay kutusu ile açık kullanıcı onayı ile etkinleştirilmelidir. 

Aşağıdaki kod parçacığı, tarayıcı kapanışları aracılığıyla ilerlikli bir kimlik ve ilgili tanımlama bilgisi oluşturur. Daha önce yapılandırılmış tüm Kayan süre sonu ayarları kabul edilir. Tarayıcı kapalıyken tanımlama bilgisinin süresi dolarsa tarayıcı, yeniden başlatıldıktan sonra tanımlama bilgisini temizler.

Şu <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> şekilde`true` ayarlayın :<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>Mutlak tanımlama bilgisi süre sonu

Mutlak bir sona erme saati ile <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>ayarlanabilir. Kalıcı tanımlama bilgisi `IsPersistent` oluşturmak için de ayarlanmalıdır. Aksi takdirde, tanımlama bilgisi oturum tabanlı bir yaşam süresi ile oluşturulur ve bu kimlik doğrulama biletinden önce ya da sonra zaman alabilir. , Ayarlandığında, <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> seçeneğinin<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>değerinigeçersizkılar. `ExpiresUtc`

Aşağıdaki kod parçacığı, 20 dakika boyunca bir kimlik ve karşılık gelen tanımlama bilgisi oluşturur. Bu, daha önce yapılandırılmış olan tüm Kayan süre sonu ayarlarını yoksayar.

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [İlke tabanlı rol denetimleri](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
