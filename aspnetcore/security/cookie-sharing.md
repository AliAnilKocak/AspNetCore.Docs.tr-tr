---
title: ASP.NET uygulamaları arasında kimlik doğrulama tanımlama bilgilerini paylaşma
author: rick-anderson
description: Kimlik doğrulama tanımlama bilgilerini ASP.NET 4. x ve ASP.NET Core uygulamaları arasında paylaşmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: security/cookie-sharing
ms.openlocfilehash: 9b5bee9fb588ef04efd50aa4a5afc3e53da1b123
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384766"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="5c949-103">ASP.NET uygulamaları arasında kimlik doğrulama tanımlama bilgilerini paylaşma</span><span class="sxs-lookup"><span data-stu-id="5c949-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="5c949-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5c949-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5c949-105">Web siteleri genellikle birlikte çalışan ayrı Web uygulamalarından oluşur.</span><span class="sxs-lookup"><span data-stu-id="5c949-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="5c949-106">Çoklu oturum açma (SSO) deneyimi sağlamak için, bir site içindeki Web uygulamaları kimlik doğrulama tanımlama bilgilerini paylaşmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5c949-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="5c949-107">Bu senaryoyu desteklemek için, veri koruma yığını, Katana tanımlama bilgisi kimlik doğrulaması ve ASP.NET Core tanımlama bilgisi kimlik doğrulama biletlerinin paylaşılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="5c949-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="5c949-108">Aşağıdaki örneklerde:</span><span class="sxs-lookup"><span data-stu-id="5c949-108">In the examples that follow:</span></span>

* <span data-ttu-id="5c949-109">Kimlik doğrulama tanımlama bilgisi adı, ortak bir değerine `.AspNet.SharedCookie`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5c949-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="5c949-110">, `AuthenticationType` Açıkça ya `Identity.Application` da varsayılan olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5c949-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="5c949-111">Veri koruma sisteminin veri koruma anahtarlarını (`SharedCookieApp`) paylaşmasını sağlamak için ortak bir uygulama adı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5c949-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="5c949-112">`Identity.Application`, kimlik doğrulama düzeni olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5c949-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="5c949-113">Hangi düzenin kullanıldığı, her ne kadar paylaşılan tanımlama bilgisi *uygulamalarında, varsayılan* düzen olarak, ya da açıkça ayarlanarak kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="5c949-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="5c949-114">Şema, tanımlama bilgilerini şifrelerken ve şifresini çözerken kullanılır, bu nedenle uygulamalar arasında tutarlı bir düzenin kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="5c949-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="5c949-115">Ortak bir [veri koruma anahtarı](xref:security/data-protection/implementation/key-management) depolama konumu kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5c949-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="5c949-116">ASP.NET Core uygulamalarda, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> anahtar depolama konumunu ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5c949-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="5c949-117">.NET Framework uygulamalarda, tanımlama bilgisi kimlik doğrulama ara yazılımı, uygulamasının <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>bir uygulamasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="5c949-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="5c949-118">`DataProtectionProvider`kimlik doğrulama tanımlama bilgisi yük verilerinin şifrelenmesi ve şifresinin çözülmesi için veri koruma hizmetleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="5c949-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="5c949-119">`DataProtectionProvider` Örnek, uygulamanın diğer bölümleri tarafından kullanılan veri koruma sisteminden yalıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="5c949-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="5c949-120">[Dataprotectionprovider. Create (System. IO. DirectoryInfo, Action\<ıdataprotectionbuilder >),](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) veri <xref:System.IO.DirectoryInfo> koruma anahtarı depolamanın konumunu belirtmek için bir değerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="5c949-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="5c949-121">`DataProtectionProvider`[Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet paketini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="5c949-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="5c949-122">ASP.NET Core 2. x uygulamalarında [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)öğesine başvurun.</span><span class="sxs-lookup"><span data-stu-id="5c949-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="5c949-123">.NET Framework uygulamalar ' da [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)'a bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5c949-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="5c949-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>ortak uygulama adını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5c949-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a><span data-ttu-id="5c949-125">Kimlik doğrulama tanımlama bilgilerini ASP.NET Core kimlikle paylaşma</span><span class="sxs-lookup"><span data-stu-id="5c949-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="5c949-126">ASP.NET Core kimliği kullanılırken:</span><span class="sxs-lookup"><span data-stu-id="5c949-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="5c949-127">Veri koruma anahtarlarının ve uygulama adının uygulamalar arasında paylaşılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="5c949-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="5c949-128">Aşağıdaki örneklerde <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> yöntemine ortak bir anahtar depolama konumu verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5c949-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="5c949-129">Ortak <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> bir paylaşılan uygulama adı yapılandırmak için kullanın (`SharedCookieApp` aşağıdaki örneklerde).</span><span class="sxs-lookup"><span data-stu-id="5c949-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="5c949-130">Daha fazla bilgi için bkz. <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="5c949-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="5c949-131">Tanımlama bilgileri için veri koruma hizmetini ayarlamak üzere genişletmeyönteminikullanın.<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*></span><span class="sxs-lookup"><span data-stu-id="5c949-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="5c949-132">Varsayılan kimlik doğrulama türü `Identity.Application`.</span><span class="sxs-lookup"><span data-stu-id="5c949-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="5c949-133">İçinde `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5c949-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a><span data-ttu-id="5c949-134">Kimlik doğrulama tanımlama bilgilerini ASP.NET Core kimlik olmadan paylaşma</span><span class="sxs-lookup"><span data-stu-id="5c949-134">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="5c949-135">Tanımlama bilgilerini ASP.NET Core kimlik olmadan doğrudan kullanırken, ' de `Startup.ConfigureServices`veri korumayı ve kimlik doğrulamasını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5c949-135">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5c949-136">Aşağıdaki örnekte, kimlik doğrulama türü olarak `Identity.Application`ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="5c949-136">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-cookies-across-different-base-paths"></a><span data-ttu-id="5c949-137">Farklı temel yollarda tanımlama bilgilerini paylaşma</span><span class="sxs-lookup"><span data-stu-id="5c949-137">Share cookies across different base paths</span></span>

<span data-ttu-id="5c949-138">Bir kimlik doğrulama tanımlama bilgisi, [HttpRequest. PathBase öğesini](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) varsayılan [Cookie. Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="5c949-138">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="5c949-139">Uygulamanın tanımlama bilgisinin farklı temel yollarda paylaşılması gerekiyorsa, `Path` geçersiz kılınmalıdır:</span><span class="sxs-lookup"><span data-stu-id="5c949-139">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a><span data-ttu-id="5c949-140">Alt etki alanları genelinde tanımlama bilgilerini paylaşma</span><span class="sxs-lookup"><span data-stu-id="5c949-140">Share cookies across subdomains</span></span>

<span data-ttu-id="5c949-141">Etki alanları arasında tanımlama bilgilerini paylaşan uygulamalar barındırırken, [Cookie. Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) özelliğinde ortak bir etki alanı belirtin.</span><span class="sxs-lookup"><span data-stu-id="5c949-141">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="5c949-142">`first_subdomain.contoso.com` `contoso.com` `Cookie.Domain` Ve gibiuygulamalararasındatanımlamabilgilerinipaylaşmak`second_subdomain.contoso.com`için şunu belirtin: `.contoso.com`</span><span class="sxs-lookup"><span data-stu-id="5c949-142">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="5c949-143">Bekleyen veri koruma anahtarlarını şifreleyin</span><span class="sxs-lookup"><span data-stu-id="5c949-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="5c949-144">Üretim dağıtımları için, ' yi `DataProtectionProvider` , geri kalan anahtarları DPAPI veya X509Certificate ile şifrelemek üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5c949-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="5c949-145">Daha fazla bilgi için bkz. <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="5c949-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="5c949-146">Aşağıdaki örnekte, için <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>bir sertifika parmak izi sağlanır:</span><span class="sxs-lookup"><span data-stu-id="5c949-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="5c949-147">ASP.NET 4. x ve ASP.NET Core uygulamaları arasında kimlik doğrulama tanımlama bilgilerini paylaşma</span><span class="sxs-lookup"><span data-stu-id="5c949-147">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="5c949-148">Katana tanımlama bilgisi kimlik doğrulama ara yazılımı kullanan ASP.NET 4. x uygulamaları ASP.NET Core tanımlama bilgisi kimlik doğrulama ara yazılımı ile uyumlu kimlik doğrulama tanımlama bilgileri oluşturacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="5c949-148">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="5c949-149">Bu, site genelinde sorunsuz bir SSO deneyimi sağlarken, büyük bir sitenin ayrı uygulamalarının çeşitli adımlarda yükseltilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="5c949-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="5c949-150">Bir uygulama, Katana tanımlama bilgisi kimlik doğrulama ara yazılımı kullandığında `UseCookieAuthentication` , projenin *Startup.auth.cs* dosyasında çağırır.</span><span class="sxs-lookup"><span data-stu-id="5c949-150">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="5c949-151">Visual Studio 2013 ve sonraki sürümlerde oluşturulan ASP.NET 4. x Web uygulaması projeleri varsayılan olarak Katana tanımlama bilgisi kimlik doğrulama ara yazılımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="5c949-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="5c949-152">ASP.NET Core `UseCookieAuthentication` uygulamalar için artık kullanılmıyor ve desteklenmese de, `UseCookieAuthentication` Katana tanımlama bilgisi kimlik doğrulama ara yazılımı kullanan bir ASP.NET 4. x uygulamasında çağırma geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="5c949-152">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="5c949-153">ASP.NET 4. x uygulaması .NET Framework 4.5.1 veya üstünü hedeflemelidir.</span><span class="sxs-lookup"><span data-stu-id="5c949-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="5c949-154">Aksi takdirde, gerekli NuGet paketleri yüklenemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="5c949-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="5c949-155">Bir ASP.NET 4. x uygulaması ve bir ASP.NET Core uygulaması arasında kimlik doğrulama tanımlama bilgilerini paylaşmak için, ASP.NET Core uygulamayı [ASP.NET Core uygulamalar arasında kimlik doğrulama tanımlama bilgilerini paylaşma](#share-authentication-cookies-with-aspnet-core-identity) bölümünde belirtilen şekilde yapılandırın ve ardından ASP.NET 4. x uygulamasını aşağıdaki şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5c949-155">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="5c949-156">Uygulamanın paketlerinin en son sürümlere güncelleştirildiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="5c949-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="5c949-157">[Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) paketini her bir ASP.NET 4. x uygulamasına yükler.</span><span class="sxs-lookup"><span data-stu-id="5c949-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="5c949-158">Çağrısını bulun ve değiştirin `UseCookieAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="5c949-158">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="5c949-159">Tanımlama bilgisi adını ASP.NET Core tanımlama bilgisi kimlik doğrulama ara yazılımı (`.AspNet.SharedCookie` örnekteki) tarafından kullanılan adla eşleşecek şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5c949-159">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="5c949-160">Aşağıdaki örnekte, kimlik doğrulama türü olarak `Identity.Application`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5c949-160">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="5c949-161">Başlatılmış bir `DataProtectionProvider` örneğini ortak veri koruma anahtarı depolama konumu olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="5c949-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="5c949-162">Uygulama adının, kimlik doğrulama tanımlama bilgilerini paylaşan tüm uygulamalar tarafından kullanılan ortak uygulama adına ayarlandığını onaylayın (`SharedCookieApp` örnekte).</span><span class="sxs-lookup"><span data-stu-id="5c949-162">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="5c949-163"><xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> Ve `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` ayarlamadıysanız,benzersizkullanıcılarıayırtedenbir`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`talep olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="5c949-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="5c949-164">*App_Start/Startup. auth. cs*:</span><span class="sxs-lookup"><span data-stu-id="5c949-164">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="5c949-165">Bir kullanıcı kimliği oluştururken, kimlik doğrulama türü`Identity.Application`(), *App_Start/Startup. auth. cs*içinde ile `UseCookieAuthentication` set içinde `AuthenticationType` tanımlanan türle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="5c949-165">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="5c949-166">*Modeller/ıdentitymodeller. cs*:</span><span class="sxs-lookup"><span data-stu-id="5c949-166">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="5c949-167">Ortak bir kullanıcı veritabanı kullan</span><span class="sxs-lookup"><span data-stu-id="5c949-167">Use a common user database</span></span>

<span data-ttu-id="5c949-168">Uygulamalar aynı kimlik şemasını (kimlik sürümü) kullandıklarında, her bir uygulama için kimlik sisteminin aynı kullanıcı veritabanına işaret olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5c949-168">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="5c949-169">Aksi halde kimlik sistemi, kimlik doğrulama tanımlama bilgisindeki bilgileri veritabanındaki bilgilere göre eşleştirmeye çalıştığında çalışma zamanında hatalara neden olur.</span><span class="sxs-lookup"><span data-stu-id="5c949-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="5c949-170">Kimlik şeması, uygulamalar arasında farklı olduğunda, genellikle uygulamalar farklı kimlik sürümleri kullandığından, en son kimlik sürümüne göre ortak bir veritabanının paylaşılması, yeniden eşleştirmeden ve diğer uygulamanın kimlik şemalarına sütun eklemeden mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="5c949-170">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="5c949-171">Yaygın bir veritabanının uygulamalar tarafından paylaşılabilmesi için diğer uygulamaları en son kimlik sürümünü kullanacak şekilde yükseltmek genellikle daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="5c949-171">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c949-172">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5c949-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
