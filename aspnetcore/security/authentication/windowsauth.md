---
title: ASP.NET Core Windows kimlik doğrulamasını yapılandırma
author: ardalis
description: Bu makalede, ASP.NET çekirdek IIS Express, IIS, HTTP.sys ve WebListener kullanarak, Windows kimlik doğrulamasını yapılandırmak açıklar.
manager: wpickett
ms.author: riande
ms.date: 10/24/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authentication/windowsauth
ms.openlocfilehash: dbcef095561fe656bdd28c4fa6560c6b269a2db0
ms.sourcegitcommit: 43bd79667bbdc8a07bd39fb4cd6f7ad3e70212fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34689015"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="7a262-103">ASP.NET Core Windows kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7a262-103">Configure Windows authentication in ASP.NET Core</span></span>

<span data-ttu-id="7a262-104">Tarafından [Steve Smith](https://ardalis.com) ve [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="7a262-104">By [Steve Smith](https://ardalis.com) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="7a262-105">Windows kimlik doğrulaması, IIS ile barındırılan ASP.NET Core uygulamaları için yapılandırılabilir [HTTP.sys](xref:fundamentals/servers/httpsys), veya [WebListener](xref:fundamentals/servers/weblistener).</span><span class="sxs-lookup"><span data-stu-id="7a262-105">Windows authentication can be configured for ASP.NET Core apps hosted with IIS, [HTTP.sys](xref:fundamentals/servers/httpsys), or [WebListener](xref:fundamentals/servers/weblistener).</span></span>

## <a name="what-is-windows-authentication"></a><span data-ttu-id="7a262-106">Windows kimlik doğrulaması nedir?</span><span class="sxs-lookup"><span data-stu-id="7a262-106">What is Windows authentication?</span></span>

<span data-ttu-id="7a262-107">Windows kimlik doğrulaması ASP.NET Core uygulamaları kullanıcıların kimliklerini işletim sistemine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="7a262-107">Windows authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="7a262-108">Sunucunuzu kullanıcıları tanımlamak için Active Directory etki alanı kimlikleri veya diğer Windows hesaplarını kullanarak bir şirket ağında çalıştığında, Windows kimlik doğrulaması kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7a262-108">You can use Windows authentication when your server runs on a corporate network using Active Directory domain identities or other Windows accounts to identify users.</span></span> <span data-ttu-id="7a262-109">Windows kimlik doğrulaması kullanıcılar, istemci uygulamaları ve web sunucuları aynı Windows etki alanına ait intranet ortamları için uygundur.</span><span class="sxs-lookup"><span data-stu-id="7a262-109">Windows authentication is best suited to intranet environments in which users, client applications, and web servers belong to the same Windows domain.</span></span>

<span data-ttu-id="7a262-110">[Windows kimlik doğrulaması ve IIS için yükleme hakkında daha fazla bilgi](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).</span><span class="sxs-lookup"><span data-stu-id="7a262-110">[Learn more about Windows authentication and installing it for IIS](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).</span></span>

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a><span data-ttu-id="7a262-111">Bir ASP.NET Core uygulamasında Windows kimlik doğrulamasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="7a262-111">Enable Windows authentication in an ASP.NET Core app</span></span>

<span data-ttu-id="7a262-112">Visual Studio Web uygulaması şablonu, Windows kimlik doğrulamayı destekleyecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="7a262-112">The Visual Studio Web Application template can be configured to support Windows authentication.</span></span>

### <a name="use-the-windows-authentication-app-template"></a><span data-ttu-id="7a262-113">Windows kimlik doğrulaması uygulama şablonu kullanın</span><span class="sxs-lookup"><span data-stu-id="7a262-113">Use the Windows authentication app template</span></span>

<span data-ttu-id="7a262-114">Visual Studio'da:</span><span class="sxs-lookup"><span data-stu-id="7a262-114">In Visual Studio:</span></span>
1. <span data-ttu-id="7a262-115">Yeni bir ASP.NET çekirdek Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7a262-115">Create a new ASP.NET Core Web Application.</span></span> 
1. <span data-ttu-id="7a262-116">Web uygulaması şablonları listesinden seçin.</span><span class="sxs-lookup"><span data-stu-id="7a262-116">Select Web Application from the list of templates.</span></span>
1. <span data-ttu-id="7a262-117">Seçin **kimlik doğrulamayı Değiştir** düğmesine tıklayın ve ardından **Windows kimlik doğrulaması**.</span><span class="sxs-lookup"><span data-stu-id="7a262-117">Select the **Change Authentication** button and select **Windows Authentication**.</span></span> 

<span data-ttu-id="7a262-118">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7a262-118">Run the app.</span></span> <span data-ttu-id="7a262-119">Kullanıcı adı görünür uygulama sağında.</span><span class="sxs-lookup"><span data-stu-id="7a262-119">The username appears in the top right of the app.</span></span>

![Windows kimlik doğrulaması tarayıcı ekran görüntüsü](windowsauth/_static/browser-screenshot.png)

<span data-ttu-id="7a262-121">IIS Express kullanarak geliştirme iş için Windows kimlik doğrulaması kullanmak için gerekli tüm yapılandırma şablonu sağlar.</span><span class="sxs-lookup"><span data-stu-id="7a262-121">For development work using IIS Express, the template provides all the configuration necessary to use Windows authentication.</span></span> <span data-ttu-id="7a262-122">Aşağıdaki bölümde el ile bir ASP.NET Core uygulama Windows kimlik doğrulaması için nasıl yapılandırılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="7a262-122">The following section shows how to manually configure an ASP.NET Core app for Windows authentication.</span></span>

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a><span data-ttu-id="7a262-123">Windows ve anonim kimlik doğrulaması için Visual Studio ayarları</span><span class="sxs-lookup"><span data-stu-id="7a262-123">Visual Studio settings for Windows and anonymous authentication</span></span>

<span data-ttu-id="7a262-124">Visual Studio projesi **özellikleri** sayfanın **hata ayıklama** sekmesi, Windows kimlik doğrulaması ve anonim kimlik doğrulaması için onay kutularını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7a262-124">The Visual Studio project **Properties** page's **Debug** tab provides check boxes for Windows authentication and anonymous authentication.</span></span>

![Windows kimlik doğrulaması tarayıcı ekran görüntüsü](windowsauth/_static/vs-auth-property-menu.png)

<span data-ttu-id="7a262-126">Alternatif olarak, bu iki özellik de yapılandırılabilir *launchSettings.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="7a262-126">Alternatively, these two properties can be configured in the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample/launchSettings.json?highlight=3-4)]

## <a name="enable-windows-authentication-with-iis"></a><span data-ttu-id="7a262-127">IIS Windows kimlik doğrulamasını etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="7a262-127">Enable Windows authentication with IIS</span></span>

<span data-ttu-id="7a262-128">IIS kullanan [ASP.NET Core Modülü](xref:fundamentals/servers/aspnet-core-module) konak ASP.NET Core uygulamaları için.</span><span class="sxs-lookup"><span data-stu-id="7a262-128">IIS uses the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="7a262-129">Modül IIS için varsayılan akış için Windows kimlik doğrulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="7a262-129">The module allows Windows authentication to flow to IIS by default.</span></span> <span data-ttu-id="7a262-130">Windows kimlik doğrulaması, IIS, uygulama yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="7a262-130">Windows authentication is configured in IIS, not the app.</span></span> <span data-ttu-id="7a262-131">Aşağıdaki bölümlerde ASP.NET Core uygulamayı Windows kimlik doğrulaması kullanacak şekilde yapılandırmak için IIS Yöneticisi'ni kullanmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="7a262-131">The following sections show how to use IIS Manager to configure an ASP.NET Core app to use Windows authentication.</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="7a262-132">Yeni bir IIS sitesi oluştur</span><span class="sxs-lookup"><span data-stu-id="7a262-132">Create a new IIS site</span></span>

<span data-ttu-id="7a262-133">Bir ad ve klasörü belirtin ve yeni bir uygulama havuzu oluşturmak izin verin.</span><span class="sxs-lookup"><span data-stu-id="7a262-133">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="customize-authentication"></a><span data-ttu-id="7a262-134">Kimlik doğrulama özelleştirme</span><span class="sxs-lookup"><span data-stu-id="7a262-134">Customize authentication</span></span>

<span data-ttu-id="7a262-135">Site için kimlik doğrulama menüsünü açın.</span><span class="sxs-lookup"><span data-stu-id="7a262-135">Open the Authentication menu for the site.</span></span>

![IIS kimlik doğrulaması menüsü](windowsauth/_static/iis-authentication-menu.png)

<span data-ttu-id="7a262-137">Anonim kimlik doğrulamasını devre dışı bırakın ve Windows kimlik doğrulamasını etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="7a262-137">Disable Anonymous Authentication and enable Windows Authentication.</span></span>

![IIS kimlik doğrulama ayarları](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a><span data-ttu-id="7a262-139">IIS site klasörüne projenizin Yayımla</span><span class="sxs-lookup"><span data-stu-id="7a262-139">Publish your project to the IIS site folder</span></span>

<span data-ttu-id="7a262-140">Visual Studio veya .NET Core CLI kullanarak, hedef klasöre uygulamayı yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="7a262-140">Using Visual Studio or the .NET Core CLI, publish the app to the destination folder.</span></span>

![Visual Studio yayımlama iletişim kutusu](windowsauth/_static/vs-publish-app.png)

<span data-ttu-id="7a262-142">Daha fazla bilgi edinmek [IIS yayımlama](xref:host-and-deploy/iis/index).</span><span class="sxs-lookup"><span data-stu-id="7a262-142">Learn more about [publishing to IIS](xref:host-and-deploy/iis/index).</span></span>

<span data-ttu-id="7a262-143">Windows kimlik doğrulaması çalıştığını doğrulamak için uygulamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="7a262-143">Launch the app to verify Windows authentication is working.</span></span>

## <a name="enable-windows-authentication-with-httpsys-or-weblistener"></a><span data-ttu-id="7a262-144">HTTP.sys veya WebListener Windows kimlik doğrulamasını etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="7a262-144">Enable Windows authentication with HTTP.sys or WebListener</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7a262-145">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7a262-145">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

<span data-ttu-id="7a262-146">Kestrel Windows kimlik doğrulamasını desteklemez, ancak kullanabilirsiniz [HTTP.sys](xref:fundamentals/servers/httpsys) Windows kendini barındıran senaryoları desteklemek için.</span><span class="sxs-lookup"><span data-stu-id="7a262-146">Although Kestrel doesn't support Windows authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="7a262-147">Aşağıdaki örnek HTTP.sys Windows kimlik doğrulaması ile kullanmak için uygulamanın web ana bilgisayarı yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="7a262-147">The following example configures the app's web host to use HTTP.sys with Windows authentication:</span></span>

[!code-csharp[](windowsauth/sample/Program2x.cs?highlight=9-14)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7a262-148">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7a262-148">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

<span data-ttu-id="7a262-149">Kestrel Windows kimlik doğrulamasını desteklemez, ancak kullanabilirsiniz [WebListener](xref:fundamentals/servers/weblistener) Windows kendini barındıran senaryoları desteklemek için.</span><span class="sxs-lookup"><span data-stu-id="7a262-149">Although Kestrel doesn't support Windows authentication, you can use [WebListener](xref:fundamentals/servers/weblistener) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="7a262-150">Aşağıdaki örnek WebListener Windows kimlik doğrulaması ile kullanmak için uygulamanın web ana bilgisayarı yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="7a262-150">The following example configures the app's web host to use WebListener with Windows authentication:</span></span>

[!code-csharp[](windowsauth/sample/Program1x.cs?highlight=6-11)]

---

## <a name="work-with-windows-authentication"></a><span data-ttu-id="7a262-151">Windows kimlik doğrulaması ile çalışma</span><span class="sxs-lookup"><span data-stu-id="7a262-151">Work with Windows authentication</span></span>

<span data-ttu-id="7a262-152">Anonim erişim yapılandırma durumunu şekilde belirler `[Authorize]` ve `[AllowAnonymous]` öznitelikleri uygulamada kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7a262-152">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="7a262-153">Aşağıdaki iki bölümü anonim erişime izin verilmeyen ve izin verilen yapılandırma durumlarını nasıl ele alınacağını açıklamaktadır.</span><span class="sxs-lookup"><span data-stu-id="7a262-153">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="7a262-154">Anonim erişime izin verme</span><span class="sxs-lookup"><span data-stu-id="7a262-154">Disallow anonymous access</span></span>

<span data-ttu-id="7a262-155">Windows kimlik doğrulaması etkinleştirildi ve adsız erişim devre dışıysa, `[Authorize]` ve `[AllowAnonymous]` öznitelikleri etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="7a262-155">When Windows authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="7a262-156">IIS sitesi (veya HTTP.sys veya WebListener sunucusu) anonim erişime izin vermeyecek şekilde yapılandırılmışsa, istek uygulamanıza hiçbir zaman ulaşır.</span><span class="sxs-lookup"><span data-stu-id="7a262-156">If the IIS site (or HTTP.sys or WebListener server) is configured to disallow anonymous access, the request never reaches your app.</span></span> <span data-ttu-id="7a262-157">Bu nedenle, `[AllowAnonymous]` özniteliği geçerli değil.</span><span class="sxs-lookup"><span data-stu-id="7a262-157">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="7a262-158">Anonim erişime izin ver</span><span class="sxs-lookup"><span data-stu-id="7a262-158">Allow anonymous access</span></span>

<span data-ttu-id="7a262-159">Windows kimlik doğrulaması ve anonim erişim etkin olduğunda, kullanın `[Authorize]` ve `[AllowAnonymous]` öznitelikleri.</span><span class="sxs-lookup"><span data-stu-id="7a262-159">When both Windows authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="7a262-160">`[Authorize]` Özniteliği gerçekten Windows kimlik doğrulaması gerektiren uygulama parçalarını güvenli olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="7a262-160">The `[Authorize]` attribute allows you to secure pieces of the app which truly do require Windows authentication.</span></span> <span data-ttu-id="7a262-161">`[AllowAnonymous]` Özniteliği geçersiz kılmaları `[Authorize]` özniteliği anonim erişime izin veren uygulamaların içindeki kullanım.</span><span class="sxs-lookup"><span data-stu-id="7a262-161">The `[AllowAnonymous]` attribute overrides `[Authorize]` attribute usage within apps which allow anonymous access.</span></span> <span data-ttu-id="7a262-162">Bkz: [basit yetkilendirme](xref:security/authorization/simple) özniteliği kullanım ayrıntıları için.</span><span class="sxs-lookup"><span data-stu-id="7a262-162">See [Simple Authorization](xref:security/authorization/simple) for attribute usage details.</span></span>

<span data-ttu-id="7a262-163">ASP.NET Core içinde 2.x `[Authorize]` öznitelik, bir ek yapılandırma gerektirir *haline* Windows kimlik doğrulaması için anonim isteklere sınama için.</span><span class="sxs-lookup"><span data-stu-id="7a262-163">In ASP.NET Core 2.x, the `[Authorize]` attribute requires additional configuration in *Startup.cs* to challenge anonymous requests for Windows authentication.</span></span> <span data-ttu-id="7a262-164">Önerilen yapılandırma kullanılan web sunucusu göre biraz değişir.</span><span class="sxs-lookup"><span data-stu-id="7a262-164">The recommended configuration varies slightly based on the web server being used.</span></span>

> [!NOTE]
> <span data-ttu-id="7a262-165">Varsayılan olarak, bir sayfaya erişmek için yetkilendirme eksikliği kullanıcılar ile boş bir HTTP 403 yanıt sunulur.</span><span class="sxs-lookup"><span data-stu-id="7a262-165">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="7a262-166">[StatusCodePages Ara](xref:fundamentals/error-handling#configuring-status-code-pages) kullanıcıları daha iyi bir "Erişim engellendi" deneyimi sunmak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="7a262-166">The [StatusCodePages middleware](xref:fundamentals/error-handling#configuring-status-code-pages) can be configured to provide users with a better "Access Denied" experience.</span></span>

#### <a name="iis"></a><span data-ttu-id="7a262-167">IIS</span><span class="sxs-lookup"><span data-stu-id="7a262-167">IIS</span></span>

<span data-ttu-id="7a262-168">IIS kullanıyorsanız, aşağıdaki ekleyin `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="7a262-168">If using IIS, add the following to the `ConfigureServices` method:</span></span> 

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a><span data-ttu-id="7a262-169">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="7a262-169">HTTP.sys</span></span>

<span data-ttu-id="7a262-170">HTTP.sys kullanıyorsanız, aşağıdaki ekleyin `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="7a262-170">If using HTTP.sys, add the following to the `ConfigureServices` method:</span></span>

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a><span data-ttu-id="7a262-171">Kimliğe bürünme</span><span class="sxs-lookup"><span data-stu-id="7a262-171">Impersonation</span></span>

<span data-ttu-id="7a262-172">ASP.NET Core kimliğe bürünme uygulamaz.</span><span class="sxs-lookup"><span data-stu-id="7a262-172">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="7a262-173">Uygulamalar, uygulama havuzu veya işlem kimliğini kullanarak, tüm istekleri için uygulama kimliği ile çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7a262-173">Apps run with the application identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="7a262-174">Açıkça bir kullanıcı adına bir eylem gerçekleştirmeniz gerekirse, kullanmak `WindowsIdentity.RunImpersonated`.</span><span class="sxs-lookup"><span data-stu-id="7a262-174">If you need to explicitly perform an action on behalf of a user, use `WindowsIdentity.RunImpersonated`.</span></span> <span data-ttu-id="7a262-175">Bu bağlamda tek bir eylem çalıştırın ve ardından bağlam kapatın.</span><span class="sxs-lookup"><span data-stu-id="7a262-175">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample/Startup.cs?name=snippet_Impersonate&highlight=10-18)]

<span data-ttu-id="7a262-176">Unutmayın `RunImpersonated` zaman uyumsuz işlemleri desteklemez ve karmaşık senaryolar için kullanılmaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7a262-176">Note that `RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="7a262-177">Örneğin, tüm istekleri ya da Ara yazılım zincirleri kaydırma desteklenen önerilen veya değil.</span><span class="sxs-lookup"><span data-stu-id="7a262-177">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

---
