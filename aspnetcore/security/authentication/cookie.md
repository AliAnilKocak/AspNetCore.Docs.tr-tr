---
title: ASP.NET Core kimliği olmadan tanımlama bilgisi kimlik doğrulaması kullanma
author: rick-anderson
description: ASP.NET Core kimliği olmadan tanımlama bilgisi kimlik doğrulamasını nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
uid: security/authentication/cookie
ms.openlocfilehash: b7c8b2cccb27dd6818330b17439675e41bfef013
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219213"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="961b7-103">ASP.NET Core kimliği olmadan tanımlama bilgisi kimlik doğrulaması kullanma</span><span class="sxs-lookup"><span data-stu-id="961b7-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="961b7-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="961b7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="961b7-105">ASP.NET Core kimlik, oturum açma işlemleri oluşturmaya ve korumaya yönelik eksiksiz, tam özellikli bir kimlik doğrulama sağlayıcısıdır.</span><span class="sxs-lookup"><span data-stu-id="961b7-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="961b7-106">Ancak, ASP.NET Core kimliği olmayan tanımlama bilgisi tabanlı bir kimlik doğrulama sağlayıcısı kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="961b7-107">Daha fazla bilgi için bkz. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="961b7-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="961b7-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="961b7-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="961b7-109">Örnek uygulamadaki tanıtım amacıyla, Maria Rodriguez olan kuramsal kullanıcının Kullanıcı hesabı, uygulamaya sabit olarak kodlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="961b7-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="961b7-110">Kullanıcı oturumu açmak için **e-posta** adresi `maria.rodriguez@contoso.com` ve herhangi bir parolayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="961b7-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="961b7-111">Kullanıcının kimliği, *Sayfalar/Account/Login. cshtml. cs* dosyasındaki `AuthenticateUser` yönteminde doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="961b7-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="961b7-112">Gerçek dünyada bir örnekte, kullanıcının kimliği bir veritabanında doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="961b7-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="961b7-113">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="961b7-113">Configuration</span></span>

<span data-ttu-id="961b7-114">Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmıyorsa, [Microsoft. Aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) paketi için proje dosyasında bir paket başvurusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="961b7-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="961b7-115">`Startup.ConfigureServices` yönteminde, <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ve <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> yöntemleriyle kimlik doğrulama ara yazılım hizmetleri oluşturun:</span><span class="sxs-lookup"><span data-stu-id="961b7-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="961b7-116">`AddAuthentication` geçirilen <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>, uygulamanın varsayılan kimlik doğrulama şemasını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="961b7-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="961b7-117">`AuthenticationScheme`, tanımlama bilgisi kimlik doğrulamasının birden çok örneği olduğunda ve [belirli bir şemayla yetkilendirmek](xref:security/authorization/limitingidentitybyscheme)istediğiniz durumlarda kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="961b7-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="961b7-118">`AuthenticationScheme`, [ıeauthenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) olarak ayarlanması, düzen Için "tanımlama bilgileri" değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="961b7-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="961b7-119">Düzeni ayıran herhangi bir dize değeri sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="961b7-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="961b7-120">Uygulamanın kimlik doğrulama düzeni, uygulamanın tanımlama bilgisi kimlik doğrulama düzeninden farklıdır.</span><span class="sxs-lookup"><span data-stu-id="961b7-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="961b7-121"><xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>için bir tanımlama bilgisi kimlik doğrulama düzeni sağlanmamışsa, `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies") kullanır.</span><span class="sxs-lookup"><span data-stu-id="961b7-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="961b7-122">Kimlik doğrulama tanımlama bilgisinin <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> özelliği varsayılan olarak `true` olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="961b7-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="961b7-123">Bir site ziyaretçisi veri toplamaya onay vermemişse kimlik doğrulama tanımlama bilgilerine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="961b7-124">Daha fazla bilgi için bkz. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="961b7-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="961b7-125">`Startup.Configure`, `HttpContext.User` özelliğini ayarlamak ve istekler için yetkilendirme ara yazılımını çalıştırmak için `UseAuthentication` ve `UseAuthorization` çağırın.</span><span class="sxs-lookup"><span data-stu-id="961b7-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="961b7-126">`UseEndpoints`çağrılmadan önce `UseAuthentication` ve `UseAuthorization` yöntemlerini çağırın:</span><span class="sxs-lookup"><span data-stu-id="961b7-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="961b7-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> sınıfı, kimlik doğrulama sağlayıcısı seçeneklerini yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="961b7-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="961b7-128">`Startup.ConfigureServices` yönteminde kimlik doğrulaması için hizmet yapılandırmasında `CookieAuthenticationOptions` ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="961b7-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="961b7-129">Tanımlama bilgisi Ilkesi ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="961b7-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="961b7-130">[Tanımlama bilgisi Ilkesi ara yazılımı](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) , tanımlama bilgisi İlkesi yeteneklerini sunar.</span><span class="sxs-lookup"><span data-stu-id="961b7-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="961b7-131">Ara yazılımı uygulama işleme işlem hattına eklemek&mdash;, yalnızca işlem hattına kayıtlı aşağı akış bileşenlerini etkiler.</span><span class="sxs-lookup"><span data-stu-id="961b7-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="961b7-132">Tanımlama bilgisi işlemenin genel özelliklerini denetlemek için tanımlama bilgisi Ilkesi ara yazılımı ' nı <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> kullanın ve tanımlama bilgileri eklenmiş veya silinmiş olduğunda tanımlama bilgisi işleme işleyicilerine kanca ekleyin.</span><span class="sxs-lookup"><span data-stu-id="961b7-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="961b7-133">Varsayılan <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> değeri, OAuth2 kimlik doğrulamasına izin verecek şekilde `SameSiteMode.Lax`.</span><span class="sxs-lookup"><span data-stu-id="961b7-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="961b7-134">`SameSiteMode.Strict`aynı site ilkesini kesinlikle zorlamak için `MinimumSameSitePolicy`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="961b7-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="961b7-135">Bu ayar, OAuth2 ve diğer çapraz kaynak kimlik doğrulama düzenlerini kesse de, çıkış noktaları arası istek işlemeye bağlı olmayan diğer uygulama türleri için tanımlama bilgisi güvenlik düzeyini yükseltir.</span><span class="sxs-lookup"><span data-stu-id="961b7-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="961b7-136">`MinimumSameSitePolicy` için tanımlama bilgisi Ilkesi ara yazılım ayarı, aşağıdaki matriye göre `CookieAuthenticationOptions` ayarlarındaki `Cookie.SameSite` ayarını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="961b7-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="961b7-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="961b7-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="961b7-138">Cookie.SameSite</span></span> | <span data-ttu-id="961b7-139">Sonuç tanımlama bilgisi. SameSite ayarı</span><span class="sxs-lookup"><span data-stu-id="961b7-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="961b7-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="961b7-140">SameSiteMode.None</span></span>     | <span data-ttu-id="961b7-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="961b7-141">SameSiteMode.None</span></span><br><span data-ttu-id="961b7-142">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="961b7-143">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="961b7-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="961b7-144">SameSiteMode.None</span></span><br><span data-ttu-id="961b7-145">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="961b7-146">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="961b7-147">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="961b7-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="961b7-148">SameSiteMode.None</span></span><br><span data-ttu-id="961b7-149">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="961b7-150">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="961b7-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="961b7-152">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="961b7-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="961b7-154">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="961b7-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="961b7-155">SameSiteMode.None</span></span><br><span data-ttu-id="961b7-156">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="961b7-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="961b7-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="961b7-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="961b7-160">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="961b7-161">Kimlik doğrulama tanımlama bilgisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="961b7-161">Create an authentication cookie</span></span>

<span data-ttu-id="961b7-162">Kullanıcı bilgilerini tutan bir tanımlama bilgisi oluşturmak için bir <xref:System.Security.Claims.ClaimsPrincipal>oluşturun.</span><span class="sxs-lookup"><span data-stu-id="961b7-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="961b7-163">Kullanıcı bilgileri serileştirilir ve tanımlama bilgisinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="961b7-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="961b7-164">Gerekli <xref:System.Security.Claims.Claim>s <xref:System.Security.Claims.ClaimsIdentity> oluşturun ve Kullanıcı oturumu açmak için <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> çağırın:</span><span class="sxs-lookup"><span data-stu-id="961b7-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="961b7-165">`SignInAsync` şifreli bir tanımlama bilgisi oluşturur ve geçerli yanıta ekler.</span><span class="sxs-lookup"><span data-stu-id="961b7-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="961b7-166">`AuthenticationScheme` belirtilmemişse, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="961b7-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="961b7-167">ASP.NET Core [veri koruma](xref:security/data-protection/using-data-protection) sistemi şifreleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="961b7-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="961b7-168">Birden çok makinede barındırılan bir uygulama, uygulamalar arasında yük dengeleme veya bir Web grubu kullanma için, [veri korumayı](xref:security/data-protection/configuration/overview) aynı anahtar halkasını ve uygulama tanımlayıcısını kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="961b7-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="961b7-169">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="961b7-169">Sign out</span></span>

<span data-ttu-id="961b7-170">Geçerli kullanıcının oturumunu kapatmak ve tanımlama bilgilerini silmek için <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>çağırın:</span><span class="sxs-lookup"><span data-stu-id="961b7-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="961b7-171">`CookieAuthenticationDefaults.AuthenticationScheme` (veya "Cookies"), düzen olarak (örneğin, "ContosoCookie") kullanılmazsa, kimlik doğrulama sağlayıcısını yapılandırırken kullanılan düzeni sağlayın.</span><span class="sxs-lookup"><span data-stu-id="961b7-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="961b7-172">Aksi takdirde, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="961b7-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="961b7-173">Arka uç değişikliklerine tepki verme</span><span class="sxs-lookup"><span data-stu-id="961b7-173">React to back-end changes</span></span>

<span data-ttu-id="961b7-174">Tanımlama bilgisi oluşturulduktan sonra tanımlama bilgisi tek kimlik kaynağıdır.</span><span class="sxs-lookup"><span data-stu-id="961b7-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="961b7-175">Arka uç sistemlerinde bir kullanıcı hesabı devre dışı bırakılmışsa:</span><span class="sxs-lookup"><span data-stu-id="961b7-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="961b7-176">Uygulamanın tanımlama bilgisi kimlik doğrulama sistemi, kimlik doğrulama tanımlama bilgisine göre istekleri işlemeye devam eder.</span><span class="sxs-lookup"><span data-stu-id="961b7-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="961b7-177">Kimlik doğrulama tanımlama bilgisi geçerli olduğu sürece kullanıcı uygulamada oturum açmış durumda kalır.</span><span class="sxs-lookup"><span data-stu-id="961b7-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="961b7-178"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> olayı, tanımlama bilgisi kimliğinin doğrulanmasını ve geçersiz kılınması için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="961b7-179">Her istekte tanımlama bilgisinin doğrulanması, uygulamaya erişen kullanıcıların iptal edilmesinin riskini azaltır.</span><span class="sxs-lookup"><span data-stu-id="961b7-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="961b7-180">Tanımlama bilgisi doğrulamasına yönelik bir yaklaşım, kullanıcı veritabanının değiştiği zaman izlemenin izlenmesine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="961b7-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="961b7-181">Kullanıcının tanımlama bilgisi verildikten sonra veritabanı değiştirilmediyse, tanımlama bilgisi hala geçerliyse kullanıcının kimliğini yeniden doğrulamaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="961b7-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="961b7-182">Örnek uygulamada, veritabanı `IUserRepository` uygulanır ve bir `LastChanged` değeri depolar.</span><span class="sxs-lookup"><span data-stu-id="961b7-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="961b7-183">Veritabanında bir Kullanıcı güncelleştirildiği zaman, `LastChanged` değeri geçerli saate ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="961b7-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="961b7-184">Veritabanı `LastChanged` değerine göre değiştiğinde bir tanımlama bilgisini geçersiz kılmak için, veritabanından geçerli `LastChanged` değerini içeren bir `LastChanged` talep ile tanımlama bilgisini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="961b7-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="961b7-185">`ValidatePrincipal` olayına bir geçersiz kılma uygulamak için, <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>türetilen bir sınıfa aşağıdaki imzaya sahip bir yöntem yazın:</span><span class="sxs-lookup"><span data-stu-id="961b7-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="961b7-186">Aşağıda `CookieAuthenticationEvents`örnek bir uygulamasıdır:</span><span class="sxs-lookup"><span data-stu-id="961b7-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="961b7-187">`Startup.ConfigureServices` yönteminde tanımlama bilgisi hizmeti kaydı sırasında olay örneğini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="961b7-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="961b7-188">`CustomCookieAuthenticationEvents` sınıfınız için [kapsamlı bir hizmet kaydı](xref:fundamentals/dependency-injection#service-lifetimes) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="961b7-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="961b7-189">Kullanıcı adının güncelleştirildiği bir durumu, güvenliği hiçbir şekilde etkilemeyen bir kararı&mdash;göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="961b7-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="961b7-190">Kullanıcı sorumlusunu kalıcı olarak güncelleştirmek istiyorsanız, `context.ReplacePrincipal` çağırın ve `context.ShouldRenew` özelliğini `true`olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="961b7-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="961b7-191">Burada açıklanan yaklaşım her istekte tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="961b7-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="961b7-192">Her istekteki tüm kullanıcılar için kimlik doğrulama tanımlama bilgilerinin doğrulanması, uygulama için büyük bir performans cezası oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="961b7-193">Kalıcı tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="961b7-193">Persistent cookies</span></span>

<span data-ttu-id="961b7-194">Tanımlama bilgisinin tarayıcı oturumları arasında kalıcı olmasını isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="961b7-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="961b7-195">Bu Kalıcılık yalnızca oturum açma veya benzer bir mekanizmanın "Beni anımsa" onay kutusu ile açık kullanıcı onayı ile etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="961b7-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="961b7-196">Aşağıdaki kod parçacığı, tarayıcı kapanışları aracılığıyla ilerlikli bir kimlik ve ilgili tanımlama bilgisi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="961b7-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="961b7-197">Daha önce yapılandırılmış tüm Kayan süre sonu ayarları kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="961b7-198">Tarayıcı kapalıyken tanımlama bilgisinin süresi dolarsa tarayıcı, yeniden başlatıldıktan sonra tanımlama bilgisini temizler.</span><span class="sxs-lookup"><span data-stu-id="961b7-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="961b7-199"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>`true` olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="961b7-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="961b7-200">Mutlak tanımlama bilgisi süre sonu</span><span class="sxs-lookup"><span data-stu-id="961b7-200">Absolute cookie expiration</span></span>

<span data-ttu-id="961b7-201">Mutlak bir süre sonu, <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>ile ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="961b7-202">Kalıcı bir tanımlama bilgisi oluşturmak için, `IsPersistent` de ayarlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="961b7-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="961b7-203">Aksi takdirde, tanımlama bilgisi oturum tabanlı bir yaşam süresi ile oluşturulur ve bu kimlik doğrulama biletinden önce ya da sonra zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="961b7-204">`ExpiresUtc` ayarlandığında, ayarlandıysa, <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> seçeneğinin değerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="961b7-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="961b7-205">Aşağıdaki kod parçacığı, 20 dakika boyunca bir kimlik ve karşılık gelen tanımlama bilgisi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="961b7-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="961b7-206">Bu, daha önce yapılandırılmış olan tüm Kayan süre sonu ayarlarını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="961b7-206">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="961b7-207">ASP.NET Core kimlik, oturum açma işlemleri oluşturmaya ve korumaya yönelik eksiksiz, tam özellikli bir kimlik doğrulama sağlayıcısıdır.</span><span class="sxs-lookup"><span data-stu-id="961b7-207">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="961b7-208">Ancak, ASP.NET Core kimliği olmayan tanımlama bilgisi tabanlı kimlik doğrulama sağlayıcısı kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-208">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="961b7-209">Daha fazla bilgi için bkz. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="961b7-209">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="961b7-210">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="961b7-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="961b7-211">Örnek uygulamadaki tanıtım amacıyla, Maria Rodriguez olan kuramsal kullanıcının Kullanıcı hesabı, uygulamaya sabit olarak kodlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="961b7-211">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="961b7-212">Kullanıcı oturumu açmak için **e-posta** adresi `maria.rodriguez@contoso.com` ve herhangi bir parolayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="961b7-212">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="961b7-213">Kullanıcının kimliği, *Sayfalar/Account/Login. cshtml. cs* dosyasındaki `AuthenticateUser` yönteminde doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="961b7-213">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="961b7-214">Gerçek dünyada bir örnekte, kullanıcının kimliği bir veritabanında doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="961b7-214">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="961b7-215">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="961b7-215">Configuration</span></span>

<span data-ttu-id="961b7-216">Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmıyorsa, [Microsoft. Aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) paketi için proje dosyasında bir paket başvurusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="961b7-216">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="961b7-217">`Startup.ConfigureServices` yönteminde, kimlik doğrulama ara yazılım hizmetini <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ve <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> yöntemlerle oluşturun:</span><span class="sxs-lookup"><span data-stu-id="961b7-217">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="961b7-218">`AddAuthentication` geçirilen <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>, uygulamanın varsayılan kimlik doğrulama şemasını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="961b7-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="961b7-219">`AuthenticationScheme`, tanımlama bilgisi kimlik doğrulamasının birden çok örneği olduğunda ve [belirli bir şemayla yetkilendirmek](xref:security/authorization/limitingidentitybyscheme)istediğiniz durumlarda kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="961b7-219">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="961b7-220">`AuthenticationScheme`, [ıeauthenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) olarak ayarlanması, düzen Için "tanımlama bilgileri" değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="961b7-220">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="961b7-221">Düzeni ayıran herhangi bir dize değeri sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="961b7-221">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="961b7-222">Uygulamanın kimlik doğrulama düzeni, uygulamanın tanımlama bilgisi kimlik doğrulama düzeninden farklıdır.</span><span class="sxs-lookup"><span data-stu-id="961b7-222">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="961b7-223"><xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>için bir tanımlama bilgisi kimlik doğrulama düzeni sağlanmamışsa, `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies") kullanır.</span><span class="sxs-lookup"><span data-stu-id="961b7-223">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="961b7-224">Kimlik doğrulama tanımlama bilgisinin <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> özelliği varsayılan olarak `true` olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="961b7-224">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="961b7-225">Bir site ziyaretçisi veri toplamaya onay vermemişse kimlik doğrulama tanımlama bilgilerine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-225">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="961b7-226">Daha fazla bilgi için bkz. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="961b7-226">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="961b7-227">`Startup.Configure` yönteminde, `HttpContext.User` özelliğini ayarlayan kimlik doğrulama ara yazılımını çağırmak için `UseAuthentication` yöntemini çağırın.</span><span class="sxs-lookup"><span data-stu-id="961b7-227">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="961b7-228">`UseMvcWithDefaultRoute` veya `UseMvc`çağrılmadan önce `UseAuthentication` yöntemi çağırın:</span><span class="sxs-lookup"><span data-stu-id="961b7-228">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="961b7-229"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> sınıfı, kimlik doğrulama sağlayıcısı seçeneklerini yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="961b7-229">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="961b7-230">`Startup.ConfigureServices` yönteminde kimlik doğrulaması için hizmet yapılandırmasında `CookieAuthenticationOptions` ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="961b7-230">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="961b7-231">Tanımlama bilgisi Ilkesi ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="961b7-231">Cookie Policy Middleware</span></span>

<span data-ttu-id="961b7-232">[Tanımlama bilgisi Ilkesi ara yazılımı](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) , tanımlama bilgisi İlkesi yeteneklerini sunar.</span><span class="sxs-lookup"><span data-stu-id="961b7-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="961b7-233">Ara yazılımı uygulama işleme işlem hattına eklemek&mdash;, yalnızca işlem hattına kayıtlı aşağı akış bileşenlerini etkiler.</span><span class="sxs-lookup"><span data-stu-id="961b7-233">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="961b7-234">Tanımlama bilgisi işlemenin genel özelliklerini denetlemek için tanımlama bilgisi Ilkesi ara yazılımı ' nı <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> kullanın ve tanımlama bilgileri eklenmiş veya silinmiş olduğunda tanımlama bilgisi işleme işleyicilerine kanca ekleyin.</span><span class="sxs-lookup"><span data-stu-id="961b7-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="961b7-235">Varsayılan <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> değeri, OAuth2 kimlik doğrulamasına izin verecek şekilde `SameSiteMode.Lax`.</span><span class="sxs-lookup"><span data-stu-id="961b7-235">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="961b7-236">`SameSiteMode.Strict`aynı site ilkesini kesinlikle zorlamak için `MinimumSameSitePolicy`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="961b7-236">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="961b7-237">Bu ayar, OAuth2 ve diğer çapraz kaynak kimlik doğrulama düzenlerini kesse de, çıkış noktaları arası istek işlemeye bağlı olmayan diğer uygulama türleri için tanımlama bilgisi güvenlik düzeyini yükseltir.</span><span class="sxs-lookup"><span data-stu-id="961b7-237">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="961b7-238">`MinimumSameSitePolicy` için tanımlama bilgisi Ilkesi ara yazılım ayarı, aşağıdaki matriye göre `CookieAuthenticationOptions` ayarlarındaki `Cookie.SameSite` ayarını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-238">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="961b7-239">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="961b7-239">MinimumSameSitePolicy</span></span> | <span data-ttu-id="961b7-240">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="961b7-240">Cookie.SameSite</span></span> | <span data-ttu-id="961b7-241">Sonuç tanımlama bilgisi. SameSite ayarı</span><span class="sxs-lookup"><span data-stu-id="961b7-241">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="961b7-242">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="961b7-242">SameSiteMode.None</span></span>     | <span data-ttu-id="961b7-243">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="961b7-243">SameSiteMode.None</span></span><br><span data-ttu-id="961b7-244">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-244">SameSiteMode.Lax</span></span><br><span data-ttu-id="961b7-245">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-245">SameSiteMode.Strict</span></span> | <span data-ttu-id="961b7-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="961b7-246">SameSiteMode.None</span></span><br><span data-ttu-id="961b7-247">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="961b7-248">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-248">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="961b7-249">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-249">SameSiteMode.Lax</span></span>      | <span data-ttu-id="961b7-250">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="961b7-250">SameSiteMode.None</span></span><br><span data-ttu-id="961b7-251">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="961b7-252">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-252">SameSiteMode.Strict</span></span> | <span data-ttu-id="961b7-253">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="961b7-254">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="961b7-255">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-255">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="961b7-256">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-256">SameSiteMode.Strict</span></span>   | <span data-ttu-id="961b7-257">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="961b7-257">SameSiteMode.None</span></span><br><span data-ttu-id="961b7-258">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="961b7-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="961b7-259">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-259">SameSiteMode.Strict</span></span> | <span data-ttu-id="961b7-260">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-260">SameSiteMode.Strict</span></span><br><span data-ttu-id="961b7-261">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-261">SameSiteMode.Strict</span></span><br><span data-ttu-id="961b7-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="961b7-262">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="961b7-263">Kimlik doğrulama tanımlama bilgisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="961b7-263">Create an authentication cookie</span></span>

<span data-ttu-id="961b7-264">Kullanıcı bilgilerini tutan bir tanımlama bilgisi oluşturmak için bir <xref:System.Security.Claims.ClaimsPrincipal>oluşturun.</span><span class="sxs-lookup"><span data-stu-id="961b7-264">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="961b7-265">Kullanıcı bilgileri serileştirilir ve tanımlama bilgisinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="961b7-265">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="961b7-266">Gerekli <xref:System.Security.Claims.Claim>s <xref:System.Security.Claims.ClaimsIdentity> oluşturun ve Kullanıcı oturumu açmak için <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> çağırın:</span><span class="sxs-lookup"><span data-stu-id="961b7-266">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="961b7-267">`SignInAsync` şifreli bir tanımlama bilgisi oluşturur ve geçerli yanıta ekler.</span><span class="sxs-lookup"><span data-stu-id="961b7-267">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="961b7-268">`AuthenticationScheme` belirtilmemişse, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="961b7-268">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="961b7-269">ASP.NET Core [veri koruma](xref:security/data-protection/using-data-protection) sistemi şifreleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="961b7-269">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="961b7-270">Birden çok makinede barındırılan bir uygulama, uygulamalar arasında yük dengeleme veya bir Web grubu kullanma için, [veri korumayı](xref:security/data-protection/configuration/overview) aynı anahtar halkasını ve uygulama tanımlayıcısını kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="961b7-270">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="961b7-271">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="961b7-271">Sign out</span></span>

<span data-ttu-id="961b7-272">Geçerli kullanıcının oturumunu kapatmak ve tanımlama bilgilerini silmek için <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>çağırın:</span><span class="sxs-lookup"><span data-stu-id="961b7-272">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="961b7-273">`CookieAuthenticationDefaults.AuthenticationScheme` (veya "Cookies"), düzen olarak (örneğin, "ContosoCookie") kullanılmazsa, kimlik doğrulama sağlayıcısını yapılandırırken kullanılan düzeni sağlayın.</span><span class="sxs-lookup"><span data-stu-id="961b7-273">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="961b7-274">Aksi takdirde, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="961b7-274">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="961b7-275">Arka uç değişikliklerine tepki verme</span><span class="sxs-lookup"><span data-stu-id="961b7-275">React to back-end changes</span></span>

<span data-ttu-id="961b7-276">Tanımlama bilgisi oluşturulduktan sonra tanımlama bilgisi tek kimlik kaynağıdır.</span><span class="sxs-lookup"><span data-stu-id="961b7-276">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="961b7-277">Arka uç sistemlerinde bir kullanıcı hesabı devre dışı bırakılmışsa:</span><span class="sxs-lookup"><span data-stu-id="961b7-277">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="961b7-278">Uygulamanın tanımlama bilgisi kimlik doğrulama sistemi, kimlik doğrulama tanımlama bilgisine göre istekleri işlemeye devam eder.</span><span class="sxs-lookup"><span data-stu-id="961b7-278">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="961b7-279">Kimlik doğrulama tanımlama bilgisi geçerli olduğu sürece kullanıcı uygulamada oturum açmış durumda kalır.</span><span class="sxs-lookup"><span data-stu-id="961b7-279">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="961b7-280"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> olayı, tanımlama bilgisi kimliğinin doğrulanmasını ve geçersiz kılınması için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-280">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="961b7-281">Her istekte tanımlama bilgisinin doğrulanması, uygulamaya erişen kullanıcıların iptal edilmesinin riskini azaltır.</span><span class="sxs-lookup"><span data-stu-id="961b7-281">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="961b7-282">Tanımlama bilgisi doğrulamasına yönelik bir yaklaşım, kullanıcı veritabanının değiştiği zaman izlemenin izlenmesine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="961b7-282">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="961b7-283">Kullanıcının tanımlama bilgisi verildikten sonra veritabanı değiştirilmediyse, tanımlama bilgisi hala geçerliyse kullanıcının kimliğini yeniden doğrulamaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="961b7-283">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="961b7-284">Örnek uygulamada, veritabanı `IUserRepository` uygulanır ve bir `LastChanged` değeri depolar.</span><span class="sxs-lookup"><span data-stu-id="961b7-284">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="961b7-285">Veritabanında bir Kullanıcı güncelleştirildiği zaman, `LastChanged` değeri geçerli saate ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="961b7-285">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="961b7-286">Veritabanı `LastChanged` değerine göre değiştiğinde bir tanımlama bilgisini geçersiz kılmak için, veritabanından geçerli `LastChanged` değerini içeren bir `LastChanged` talep ile tanımlama bilgisini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="961b7-286">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="961b7-287">`ValidatePrincipal` olayına bir geçersiz kılma uygulamak için, <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>türetilen bir sınıfa aşağıdaki imzaya sahip bir yöntem yazın:</span><span class="sxs-lookup"><span data-stu-id="961b7-287">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="961b7-288">Aşağıda `CookieAuthenticationEvents`örnek bir uygulamasıdır:</span><span class="sxs-lookup"><span data-stu-id="961b7-288">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="961b7-289">`Startup.ConfigureServices` yönteminde tanımlama bilgisi hizmeti kaydı sırasında olay örneğini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="961b7-289">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="961b7-290">`CustomCookieAuthenticationEvents` sınıfınız için [kapsamlı bir hizmet kaydı](xref:fundamentals/dependency-injection#service-lifetimes) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="961b7-290">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="961b7-291">Kullanıcı adının güncelleştirildiği bir durumu, güvenliği hiçbir şekilde etkilemeyen bir kararı&mdash;göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="961b7-291">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="961b7-292">Kullanıcı sorumlusunu kalıcı olarak güncelleştirmek istiyorsanız, `context.ReplacePrincipal` çağırın ve `context.ShouldRenew` özelliğini `true`olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="961b7-292">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="961b7-293">Burada açıklanan yaklaşım her istekte tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="961b7-293">The approach described here is triggered on every request.</span></span> <span data-ttu-id="961b7-294">Her istekteki tüm kullanıcılar için kimlik doğrulama tanımlama bilgilerinin doğrulanması, uygulama için büyük bir performans cezası oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-294">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="961b7-295">Kalıcı tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="961b7-295">Persistent cookies</span></span>

<span data-ttu-id="961b7-296">Tanımlama bilgisinin tarayıcı oturumları arasında kalıcı olmasını isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="961b7-296">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="961b7-297">Bu Kalıcılık yalnızca oturum açma veya benzer bir mekanizmanın "Beni anımsa" onay kutusu ile açık kullanıcı onayı ile etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="961b7-297">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="961b7-298">Aşağıdaki kod parçacığı, tarayıcı kapanışları aracılığıyla ilerlikli bir kimlik ve ilgili tanımlama bilgisi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="961b7-298">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="961b7-299">Daha önce yapılandırılmış tüm Kayan süre sonu ayarları kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-299">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="961b7-300">Tarayıcı kapalıyken tanımlama bilgisinin süresi dolarsa tarayıcı, yeniden başlatıldıktan sonra tanımlama bilgisini temizler.</span><span class="sxs-lookup"><span data-stu-id="961b7-300">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="961b7-301"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>`true` olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="961b7-301">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="961b7-302">Mutlak tanımlama bilgisi süre sonu</span><span class="sxs-lookup"><span data-stu-id="961b7-302">Absolute cookie expiration</span></span>

<span data-ttu-id="961b7-303">Mutlak bir süre sonu, <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>ile ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-303">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="961b7-304">Kalıcı bir tanımlama bilgisi oluşturmak için, `IsPersistent` de ayarlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="961b7-304">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="961b7-305">Aksi takdirde, tanımlama bilgisi oturum tabanlı bir yaşam süresi ile oluşturulur ve bu kimlik doğrulama biletinden önce ya da sonra zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="961b7-305">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="961b7-306">`ExpiresUtc` ayarlandığında, ayarlandıysa, <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions><xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> seçeneğinin değerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="961b7-306">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="961b7-307">Aşağıdaki kod parçacığı, 20 dakika boyunca bir kimlik ve karşılık gelen tanımlama bilgisi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="961b7-307">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="961b7-308">Bu, daha önce yapılandırılmış olan tüm Kayan süre sonu ayarlarını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="961b7-308">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="961b7-309">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="961b7-309">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="961b7-310">İlke tabanlı rol denetimleri</span><span class="sxs-lookup"><span data-stu-id="961b7-310">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
