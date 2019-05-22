---
title: Windows IIS üzerinde ASP.NET Core barındırma
author: guardrex
description: ASP.NET Core uygulamaları Windows Server Internet Information Services (IIS) üzerinde barındırmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2019
uid: host-and-deploy/iis/index
ms.openlocfilehash: 6ba4da913ef712ef897a4c8418263e3060ea85ac
ms.sourcegitcommit: e67356f5e643a5d43f6d567c5c998ce6002bdeb4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66004975"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="a6cef-103">Windows IIS üzerinde ASP.NET Core barındırma</span><span class="sxs-lookup"><span data-stu-id="a6cef-103">Host ASP.NET Core on Windows with IIS</span></span>

<span data-ttu-id="a6cef-104">Tarafından [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a6cef-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[<span data-ttu-id="a6cef-105">Paket barındırma .NET Core'u yükleme</span><span class="sxs-lookup"><span data-stu-id="a6cef-105">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="a6cef-106">Desteklenen işletim sistemleri</span><span class="sxs-lookup"><span data-stu-id="a6cef-106">Supported operating systems</span></span>

<span data-ttu-id="a6cef-107">Aşağıdaki işletim sistemleri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="a6cef-107">The following operating systems are supported:</span></span>

* <span data-ttu-id="a6cef-108">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="a6cef-108">Windows 7 or later</span></span>
* <span data-ttu-id="a6cef-109">Windows Server 2008 R2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="a6cef-109">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="a6cef-110">[HTTP.sys sunucu](xref:fundamentals/servers/httpsys) (eski adıyla çağrılan WebListener), ııs'li bir ters proxy yapılandırması çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="a6cef-110">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="a6cef-111">Kullanım [Kestrel sunucu](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="a6cef-111">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="a6cef-112">Azure'da barındırma hakkında daha fazla bilgi için bkz: <xref:host-and-deploy/azure-apps/index>.</span><span class="sxs-lookup"><span data-stu-id="a6cef-112">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="a6cef-113">Desteklenen platformlar</span><span class="sxs-lookup"><span data-stu-id="a6cef-113">Supported platforms</span></span>

<span data-ttu-id="a6cef-114">(X86) 32 bit veya 64-bit (x 64) dağıtım desteklenir için yayımlanan uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="a6cef-114">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="a6cef-115">.NET Core SDK'sı sürece bir 32-bit (x86) 32-bit uygulamayla dağıtma uygulama:</span><span class="sxs-lookup"><span data-stu-id="a6cef-115">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="a6cef-116">Bir 64-bit uygulamalar tarafından kullanılabilecek daha büyük sanal bellek adres alanı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-116">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="a6cef-117">Daha büyük IIS yığın boyutu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-117">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="a6cef-118">64-bit yerel bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-118">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="a6cef-119">64 bitlik bir uygulamayı yayımlamak için bir 64-bit (x 64) .NET Core SDK'sını kullanın.</span><span class="sxs-lookup"><span data-stu-id="a6cef-119">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="a6cef-120">Bir 64 bit çalışma zamanı ana bilgisayar sisteminde mevcut olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-120">A 64-bit runtime must be present on the host system.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="a6cef-121">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="a6cef-121">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="a6cef-122">IISIntegration bileşenlerini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="a6cef-122">Enable the IISIntegration components</span></span>

<span data-ttu-id="a6cef-123">Tipik bir *Program.cs* çağrıları <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bir konak kurulumunu başlatmak için:</span><span class="sxs-lookup"><span data-stu-id="a6cef-123">A typical *Program.cs* calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to begin setting up a host:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a6cef-124">**İşlem içi barındırma modeli**</span><span class="sxs-lookup"><span data-stu-id="a6cef-124">**In-process hosting model**</span></span>

<span data-ttu-id="a6cef-125">`CreateDefaultBuilder` çağrıları `UseIIS` önyükleme yöntemi [CoreCLR](/dotnet/standard/glossary#coreclr) ve IIS çalışan işlemi uygulama barındırın (*w3wp.exe* veya *iisexpress.exe*).</span><span class="sxs-lookup"><span data-stu-id="a6cef-125">`CreateDefaultBuilder` calls the `UseIIS` method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="a6cef-126">Performans testleri belirten bir .NET Core uygulaması işlem içi barındırma için uygulama işlem dışı ve proxy isteklerini barındırma kıyasla önemli ölçüde daha yüksek istek üretilen işini teslim [Kestrel](xref:fundamentals/servers/kestrel) sunucusu.</span><span class="sxs-lookup"><span data-stu-id="a6cef-126">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

<span data-ttu-id="a6cef-127">İşlem içi barındırma modeli, .NET Framework'ü hedefleyen ASP.NET Core uygulamaları için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="a6cef-127">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="a6cef-128">**İşlem dışı barındırma modeli**</span><span class="sxs-lookup"><span data-stu-id="a6cef-128">**Out-of-process hosting model**</span></span>

<span data-ttu-id="a6cef-129">IIS ile işlem dışı barındırmak için `CreateDefaultBuilder` yapılandırır [Kestrel](xref:fundamentals/servers/kestrel) web sunucusu olarak ve bağlantı noktası ve temel yolunu yapılandırarak IIS tümleştirme sağlar [ASP.NET Core Modülü](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="a6cef-129">For out-of-process hosting with IIS, `CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="a6cef-130">ASP.NET Core modülü arka uç işleme atamak için dinamik bir bağlantı noktası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a6cef-130">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="a6cef-131">`CreateDefaultBuilder` çağrıları <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> yöntemi.</span><span class="sxs-lookup"><span data-stu-id="a6cef-131">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="a6cef-132">`UseIISIntegration` Kestrel'i localhost IP adresi dinamik bir bağlantı noktası dinleyecek şekilde yapılandırır (`127.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="a6cef-132">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="a6cef-133">Dinamik bağlantı noktası 1234 ise sırasında Kestrel dinler `127.0.0.1:1234`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-133">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="a6cef-134">Bu yapılandırma tarafından sağlanan diğer URL'yi yapılandırmaları değiştirir:</span><span class="sxs-lookup"><span data-stu-id="a6cef-134">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="a6cef-135">Kestrel'i'nın dinleme API</span><span class="sxs-lookup"><span data-stu-id="a6cef-135">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="a6cef-136">[Yapılandırma](xref:fundamentals/configuration/index) (veya [--URL'leri komut satırı seçeneği](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="a6cef-136">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="a6cef-137">Çağrılar `UseUrls` veya Kestrel'ın `Listen` Modülü'nü kullanırken API gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-137">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="a6cef-138">Varsa `UseUrls` veya `Listen` çağrılır, yalnızca IIS gerekmeden uygulamayı çalıştırırken belirttiğiniz bağlantı noktalarındaki Kestrel dinlediği.</span><span class="sxs-lookup"><span data-stu-id="a6cef-138">If `UseUrls` or `Listen` is called, Kestrel listens on the ports specified only when running the app without IIS.</span></span>

<span data-ttu-id="a6cef-139">İşlem içi ve dışı işlem barındırma modelleri hakkında daha fazla bilgi için bkz. [ASP.NET Core Modülü](xref:host-and-deploy/aspnet-core-module) ve [ASP.NET Core Module yapılandırma başvurusu](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="a6cef-139">For more information on the in-process and out-of-process hosting models, see [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a6cef-140">`CreateDefaultBuilder` yapılandırır [Kestrel](xref:fundamentals/servers/kestrel) web sunucusu olarak ve bağlantı noktası ve temel yolunu yapılandırarak IIS tümleştirme sağlar [ASP.NET Core Modülü](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="a6cef-140">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="a6cef-141">ASP.NET Core modülü arka uç işleme atamak için dinamik bir bağlantı noktası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a6cef-141">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="a6cef-142">`CreateDefaultBuilder` çağrıları <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> yöntemi.</span><span class="sxs-lookup"><span data-stu-id="a6cef-142">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="a6cef-143">`UseIISIntegration` Kestrel'i localhost IP adresi dinamik bir bağlantı noktası dinleyecek şekilde yapılandırır (`127.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="a6cef-143">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="a6cef-144">Dinamik bağlantı noktası 1234 ise sırasında Kestrel dinler `127.0.0.1:1234`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-144">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="a6cef-145">Bu yapılandırma tarafından sağlanan diğer URL'yi yapılandırmaları değiştirir:</span><span class="sxs-lookup"><span data-stu-id="a6cef-145">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="a6cef-146">Kestrel'i'nın dinleme API</span><span class="sxs-lookup"><span data-stu-id="a6cef-146">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="a6cef-147">[Yapılandırma](xref:fundamentals/configuration/index) (veya [--URL'leri komut satırı seçeneği](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="a6cef-147">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="a6cef-148">Çağrılar `UseUrls` veya Kestrel'ın `Listen` Modülü'nü kullanırken API gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-148">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="a6cef-149">Varsa `UseUrls` veya `Listen` çağrılır, yalnızca IIS gerekmeden uygulamayı çalıştırırken belirtilen bağlantı noktasında dinleyen Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a6cef-149">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

::: moniker-end

<span data-ttu-id="a6cef-150">Barındırma ile ilgili daha fazla bilgi için bkz: [ASP.NET Core ana](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="a6cef-150">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

### <a name="iis-options"></a><span data-ttu-id="a6cef-151">IIS seçenekleri</span><span class="sxs-lookup"><span data-stu-id="a6cef-151">IIS options</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a6cef-152">**İşlem içi barındırma modeli**</span><span class="sxs-lookup"><span data-stu-id="a6cef-152">**In-process hosting model**</span></span>

<span data-ttu-id="a6cef-153">IIS sunucusu seçeneklerini yapılandırmak için bir hizmet yapılandırması dahil <xref:Microsoft.AspNetCore.Builder.IISServerOptions> içinde <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="a6cef-153">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="a6cef-154">Aşağıdaki örnek AutomaticAuthentication devre dışı bırakır:</span><span class="sxs-lookup"><span data-stu-id="a6cef-154">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="a6cef-155">Seçenek</span><span class="sxs-lookup"><span data-stu-id="a6cef-155">Option</span></span>                         | <span data-ttu-id="a6cef-156">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="a6cef-156">Default</span></span> | <span data-ttu-id="a6cef-157">Ayar</span><span class="sxs-lookup"><span data-stu-id="a6cef-157">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="a6cef-158">Varsa `true`, IIS sunucusu ayarlar `HttpContext.User` tarafından kimliği doğrulanmış [Windows kimlik doğrulaması](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a6cef-158">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="a6cef-159">Varsa `false`, sunucu için bir kimlik yalnızca sağlar `HttpContext.User` ve açıkça tarafından istendiğinde zorlukları yanıtlar `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-159">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="a6cef-160">Windows kimlik doğrulaması etkin, IIS için `AutomaticAuthentication` işlevi.</span><span class="sxs-lookup"><span data-stu-id="a6cef-160">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="a6cef-161">Daha fazla bilgi için [Windows kimlik doğrulaması](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a6cef-161">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="a6cef-162">Oturum açma sayfaları kullanıcılara gösterilen görünen adını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="a6cef-162">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="a6cef-163">Zaman uyumlu g/ç için izin verilip verilmediğini `HttpContext.Request` ve `HttpContext.Response`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-163">Whether synchronous IO is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="a6cef-164">Alır veya ayarlar için en fazla istek gövdesi boyutu `HttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-164">Gets or sets the the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="a6cef-165">IIS'nin sınırına sahip olduğuna dikkat edin `maxAllowedContentLength` hangi işlenmeyecek önce `MaxRequestBodySize` kümesinde `IISServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-165">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="a6cef-166">Değiştirme `MaxRequestBodySize` etkilemez `maxAllowedContentLength`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-166">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="a6cef-167">Artırmak için `maxAllowedContentLength`, bir giriş ekleyin *web.config* ayarlanacak `maxAllowedContentLength` daha yüksek bir değer.</span><span class="sxs-lookup"><span data-stu-id="a6cef-167">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="a6cef-168">Daha fazla ayrıntı için [yapılandırma](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="a6cef-168">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="a6cef-169">**İşlem dışı barındırma modeli**</span><span class="sxs-lookup"><span data-stu-id="a6cef-169">**Out-of-process hosting model**</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="a6cef-170">Seçenek</span><span class="sxs-lookup"><span data-stu-id="a6cef-170">Option</span></span>                         | <span data-ttu-id="a6cef-171">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="a6cef-171">Default</span></span> | <span data-ttu-id="a6cef-172">Ayar</span><span class="sxs-lookup"><span data-stu-id="a6cef-172">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="a6cef-173">Varsa `true`, IIS sunucusu ayarlar `HttpContext.User` tarafından kimliği doğrulanmış [Windows kimlik doğrulaması](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a6cef-173">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="a6cef-174">Varsa `false`, sunucu için bir kimlik yalnızca sağlar `HttpContext.User` ve açıkça tarafından istendiğinde zorlukları yanıtlar `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-174">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="a6cef-175">Windows kimlik doğrulaması etkin, IIS için `AutomaticAuthentication` işlevi.</span><span class="sxs-lookup"><span data-stu-id="a6cef-175">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="a6cef-176">Daha fazla bilgi için [Windows kimlik doğrulaması](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a6cef-176">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="a6cef-177">Oturum açma sayfaları kullanıcılara gösterilen görünen adını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="a6cef-177">Sets the display name shown to users on login pages.</span></span> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a6cef-178">**İşlem dışı barındırma modeli**</span><span class="sxs-lookup"><span data-stu-id="a6cef-178">**Out-of-process hosting model**</span></span>

::: moniker-end

<span data-ttu-id="a6cef-179">IIS seçeneklerini yapılandırmak için bir hizmet yapılandırması dahil <xref:Microsoft.AspNetCore.Builder.IISOptions> içinde <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="a6cef-179">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="a6cef-180">Aşağıdaki örnek uygulamayı doldurmasını önler `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="a6cef-180">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="a6cef-181">Seçenek</span><span class="sxs-lookup"><span data-stu-id="a6cef-181">Option</span></span>                         | <span data-ttu-id="a6cef-182">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="a6cef-182">Default</span></span> | <span data-ttu-id="a6cef-183">Ayar</span><span class="sxs-lookup"><span data-stu-id="a6cef-183">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="a6cef-184">Varsa `true`, IIS tümleştirme ara yazılımı ayarlar `HttpContext.User` tarafından kimliği doğrulanmış [Windows kimlik doğrulaması](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a6cef-184">If `true`, IIS Integration Middleware sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="a6cef-185">Varsa `false`, ara yazılım için bir kimlik yalnızca sağlar `HttpContext.User` ve açıkça tarafından istendiğinde zorlukları yanıtlar `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-185">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="a6cef-186">Windows kimlik doğrulaması etkin, IIS için `AutomaticAuthentication` işlevi.</span><span class="sxs-lookup"><span data-stu-id="a6cef-186">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="a6cef-187">Daha fazla bilgi için [Windows kimlik doğrulaması](xref:security/authentication/windowsauth) konu.</span><span class="sxs-lookup"><span data-stu-id="a6cef-187">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="a6cef-188">Oturum açma sayfaları kullanıcılara gösterilen görünen adını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="a6cef-188">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="a6cef-189">Varsa `true` ve `MS-ASPNETCORE-CLIENTCERT` istek üstbilgisi mevcutsa, `HttpContext.Connection.ClientCertificate` doldurulur.</span><span class="sxs-lookup"><span data-stu-id="a6cef-189">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a6cef-190">Ara sunucu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="a6cef-190">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a6cef-191">İletilen üstbilgileri ara yazılım ve ASP.NET Core modülü yapılandırır IIS tümleştirme Ara şema (HTTP/HTTPS) ve isteğin geldiği uzak IP adresine iletecek şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-191">The IIS Integration Middleware, which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="a6cef-192">Ek Ara sunucuları ve yük dengeleyici barındırılan uygulamalar için ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-192">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="a6cef-193">Daha fazla bilgi için [proxy sunucuları ile çalışma ve yük Dengeleyiciler için ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="a6cef-193">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="a6cef-194">Web.config dosyası</span><span class="sxs-lookup"><span data-stu-id="a6cef-194">web.config file</span></span>

<span data-ttu-id="a6cef-195">*Web.config* dosya yapılandırır [ASP.NET Core Modülü](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="a6cef-195">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="a6cef-196">Oluşturma, dönüştürme ve yayımlama *web.config* dosya bir MSBuild hedefi tarafından gerçekleştirilir (`_TransformWebConfig`) projeyi ne zaman yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-196">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="a6cef-197">Bu Web SDK'sı hedeflerin mevcut hedefidir (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="a6cef-197">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="a6cef-198">SDK'sı, proje dosyasının en üstüne ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="a6cef-198">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="a6cef-199">Varsa bir *web.config* dosyası projedeki mevcut değil, dosyanın doğru oluşturulması *processPath* ve *bağımsız değişkenleri* yapılandırmak için [ASP.NET Core Modül](xref:host-and-deploy/aspnet-core-module) ve taşınabilir [yayımlanmış çıktısı](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="a6cef-199">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="a6cef-200">Varsa bir *web.config* dosyası projesinde, dosyanın doğru dönüştürülür *processPath* ve *bağımsız değişkenleri* ASP.NET Core modülü yapılandırmak için ve azure'a taşındı yayımlanmış çıktısı.</span><span class="sxs-lookup"><span data-stu-id="a6cef-200">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="a6cef-201">Dönüştürme, IIS yapılandırma ayarları dosyasında değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="a6cef-201">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="a6cef-202">*Web.config* etkin IIS modüllerini denetleyen ek IIS yapılandırması ayarları dosyası sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-202">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="a6cef-203">ASP.NET Core uygulamaları istekleri işleyebilen IIS modülleri hakkında daha fazla bilgi için bkz: [IIS modüllerini](xref:host-and-deploy/iis/modules) konu.</span><span class="sxs-lookup"><span data-stu-id="a6cef-203">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="a6cef-204">Dönüştürme gelen Web SDK'sı önlemek için *web.config* dosya, kullanın **\<IsTransformWebConfigDisabled >** özelliği proje dosyasında:</span><span class="sxs-lookup"><span data-stu-id="a6cef-204">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="a6cef-205">Dosya dönüştürme gelen Web SDK'yı devre dışı bırakılırken *processPath* ve *bağımsız değişkenleri* geliştirici tarafından el ile ayarlamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-205">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="a6cef-206">Daha fazla bilgi için [ASP.NET Core Module yapılandırma başvurusu](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="a6cef-206">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module).</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="a6cef-207">Web.config dosyası konumu</span><span class="sxs-lookup"><span data-stu-id="a6cef-207">web.config file location</span></span>

<span data-ttu-id="a6cef-208">Ayarlamak için [ASP.NET Core Modülü](xref:host-and-deploy/aspnet-core-module) doğru *web.config* dosya dağıtılan uygulamayı içerik kök yolda (genellikle uygulama temel yolu) mevcut olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-208">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the content root path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="a6cef-209">IIS için sağlanan Web sitesi fiziksel yol ile aynı konumda budur.</span><span class="sxs-lookup"><span data-stu-id="a6cef-209">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="a6cef-210">*Web.config* dosya, Web dağıtımı kullanarak birden fazla uygulama yayımlamayı etkinleştirmek için uygulamanın kök dizininde gereklidir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-210">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="a6cef-211">Mevcut uygulamanın fiziksel yola gibi hassas dosyalar *\<derleme >. runtimeconfig.json*, *\<derleme > .xml* (XML belgeleri açıklamaları) ve *\<derleme >. deps.json*.</span><span class="sxs-lookup"><span data-stu-id="a6cef-211">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="a6cef-212">Zaman *web.config* dosya varsa ve siteyi normalde başlatır, IIS bunların istenmesi halinde bu hassas dosyalar hizmet değil.</span><span class="sxs-lookup"><span data-stu-id="a6cef-212">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="a6cef-213">Varsa *web.config* dosyası eksik, yanlış adlandırılan veya site için normal başlangıç yapılandırılamıyor, IIS hassas dosyalar genel olarak hizmet verebilir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-213">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="a6cef-214">***Web.config* doğru adlı, dağıtımdaki her zaman mevcut ve yukarı site normal başlangıç için yapılandırmak için dosya olmalıdır. Hiçbir zaman Kaldır *web.config* dosyasından bir üretim dağıtımı.**</span><span class="sxs-lookup"><span data-stu-id="a6cef-214">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="a6cef-215">Web.config’i dönüştürme</span><span class="sxs-lookup"><span data-stu-id="a6cef-215">Transform web.config</span></span>

<span data-ttu-id="a6cef-216">Dönüştürmeniz gerekirse *web.config* (örneğin, ortam değişkenlerini ayarlama, yapılandırma, profili veya ortama göre), bkz: yayımlama sırasında <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="a6cef-216">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="a6cef-217">IIS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="a6cef-217">IIS configuration</span></span>

<span data-ttu-id="a6cef-218">**Windows Server işletim sistemleri**</span><span class="sxs-lookup"><span data-stu-id="a6cef-218">**Windows Server operating systems**</span></span>

<span data-ttu-id="a6cef-219">Etkinleştirme **Web sunucusu (IIS)** sunucu rolü ve rol hizmetlerini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a6cef-219">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="a6cef-220">Kullanım **rol ve Özellik Ekle** Sihirbazı'ndan **Yönet** menüsü ya da bağlantı **Sunucu Yöneticisi**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-220">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="a6cef-221">Üzerinde **sunucu rolleri** kutusunu işaretleyin, adım **Web sunucusu (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-221">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Web sunucusu IIS rolünü sunucu rolleri adımda seçilir.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="a6cef-223">Sonra **özellikleri** adım **rol hizmetlerini** adım, Web sunucusu (IIS) yükler.</span><span class="sxs-lookup"><span data-stu-id="a6cef-223">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="a6cef-224">İstenen IIS rol hizmetlerini seçin veya sağlanan varsayılan rol hizmetlerini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-224">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Varsayılan rol hizmetlerini seçin rol hizmetleri adımda seçilir.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="a6cef-226">**Windows kimlik doğrulaması (isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="a6cef-226">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="a6cef-227">Windows kimlik doğrulamasını etkinleştirmek için aşağıdaki düğümleri genişletin: **Web sunucusu** > **güvenlik**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-227">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="a6cef-228">Seçin **Windows kimlik doğrulaması** özelliği.</span><span class="sxs-lookup"><span data-stu-id="a6cef-228">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="a6cef-229">Daha fazla bilgi için [Windows kimlik doğrulaması \<ServiceCredentials >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [yapılandırma Windows kimlik doğrulaması](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a6cef-229">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="a6cef-230">**WebSockets (isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="a6cef-230">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="a6cef-231">WebSockets, ASP.NET Core 1.1 veya sonraki sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-231">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="a6cef-232">WebSockets etkinleştirmek için aşağıdaki düğümleri genişletin: **Web sunucusu** > **uygulama geliştirme**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-232">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="a6cef-233">Seçin **WebSocket Protokolü** özelliği.</span><span class="sxs-lookup"><span data-stu-id="a6cef-233">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="a6cef-234">Daha fazla bilgi için [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="a6cef-234">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="a6cef-235">Aracılığıyla devam **onay** Hizmetleri ve web sunucusu rolü yüklemek için adım.</span><span class="sxs-lookup"><span data-stu-id="a6cef-235">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="a6cef-236">Yükledikten sonra sunucu/IIS yeniden başlatma gerekli değilse **Web sunucusu (IIS)** rol.</span><span class="sxs-lookup"><span data-stu-id="a6cef-236">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="a6cef-237">**Windows masaüstü işletim sistemleri**</span><span class="sxs-lookup"><span data-stu-id="a6cef-237">**Windows desktop operating systems**</span></span>

<span data-ttu-id="a6cef-238">Etkinleştirme **IIS Yönetim Konsolu** ve **World Wide Web Hizmetleri**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-238">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="a6cef-239">Gidin **Denetim Masası** > **programlar** > **programlar ve Özellikler** > **kapatma Windows özellikleri hakkında ya da kapalı** (ekranın sol).</span><span class="sxs-lookup"><span data-stu-id="a6cef-239">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="a6cef-240">Açık **Internet Information Services** düğümü.</span><span class="sxs-lookup"><span data-stu-id="a6cef-240">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="a6cef-241">Açık **Web yönetimi araçları** düğümü.</span><span class="sxs-lookup"><span data-stu-id="a6cef-241">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="a6cef-242">İçin kutuyu **IIS Yönetim Konsolu**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-242">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="a6cef-243">İçin kutuyu **World Wide Web Hizmetleri**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-243">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="a6cef-244">Varsayılan özelliklerini kabul **World Wide Web Hizmetleri** veya IIS özelliklerini özelleştirin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-244">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="a6cef-245">**Windows kimlik doğrulaması (isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="a6cef-245">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="a6cef-246">Windows kimlik doğrulamasını etkinleştirmek için aşağıdaki düğümleri genişletin: **World Wide Web Hizmetleri** > **güvenlik**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-246">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="a6cef-247">Seçin **Windows kimlik doğrulaması** özelliği.</span><span class="sxs-lookup"><span data-stu-id="a6cef-247">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="a6cef-248">Daha fazla bilgi için [Windows kimlik doğrulaması \<ServiceCredentials >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [yapılandırma Windows kimlik doğrulaması](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a6cef-248">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="a6cef-249">**WebSockets (isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="a6cef-249">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="a6cef-250">WebSockets, ASP.NET Core 1.1 veya sonraki sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-250">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="a6cef-251">WebSockets etkinleştirmek için aşağıdaki düğümleri genişletin: **World Wide Web Hizmetleri** > **uygulama geliştirme özellikleri**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-251">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="a6cef-252">Seçin **WebSocket Protokolü** özelliği.</span><span class="sxs-lookup"><span data-stu-id="a6cef-252">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="a6cef-253">Daha fazla bilgi için [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="a6cef-253">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="a6cef-254">IIS yüklemeyi yeniden başlatma gerektirirse, sistemi yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="a6cef-254">If the IIS installation requires a restart, restart the system.</span></span>

![Windows özellikleri, IIS Yönetim Konsolu ve World Wide Web Hizmetleri seçilir.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="a6cef-256">Paket barındırma .NET Core'u yükleme</span><span class="sxs-lookup"><span data-stu-id="a6cef-256">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="a6cef-257">Yükleme *.NET Core barındırma paket* barındıran sistemde.</span><span class="sxs-lookup"><span data-stu-id="a6cef-257">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="a6cef-258">.NET Core çalışma zamanı, .NET Core kitaplığı paketi yükler ve [ASP.NET Core Modülü](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="a6cef-258">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="a6cef-259">Modül IIS çalıştırılacak uygulamaları ASP.NET Core sağlar.</span><span class="sxs-lookup"><span data-stu-id="a6cef-259">The module allows ASP.NET Core apps to run behind IIS.</span></span> <span data-ttu-id="a6cef-260">Sistem, Internet bağlantısı yoksa, alma ve yükleme [Microsoft Visual C++ 2015 yeniden dağıtılabilir](https://www.microsoft.com/download/details.aspx?id=53840) .NET Core barındırma paketini yüklemeden önce.</span><span class="sxs-lookup"><span data-stu-id="a6cef-260">If the system doesn't have an Internet connection, obtain and install the [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) before installing the .NET Core Hosting Bundle.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a6cef-261">Barındırma paket önce IIS yüklü değilse, paket yükleme onarılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-261">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="a6cef-262">IIS yeniden yükledikten sonra paket barındırma yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a6cef-262">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="a6cef-263">.NET Core 64-bit (x 64) sürümünü yükledikten sonra barındırma paket yüklü değilse, SDK'ları eksik görünebilir ([.NET Core SDK algılandı](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="a6cef-263">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="a6cef-264">Bu sorunu gidermek için bkz: <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="a6cef-264">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="a6cef-265">Doğrudan indirme (geçerli sürüm)</span><span class="sxs-lookup"><span data-stu-id="a6cef-265">Direct download (current version)</span></span>

<span data-ttu-id="a6cef-266">Aşağıdaki bağlantıyı kullanarak yükleyiciyi indirin:</span><span class="sxs-lookup"><span data-stu-id="a6cef-266">Download the installer using the following link:</span></span>

[<span data-ttu-id="a6cef-267">Geçerli .NET Core barındırma Paket Yükleyici (doğrudan indirme)</span><span class="sxs-lookup"><span data-stu-id="a6cef-267">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="a6cef-268">Yükleyici önceki sürümleri</span><span class="sxs-lookup"><span data-stu-id="a6cef-268">Earlier versions of the installer</span></span>

<span data-ttu-id="a6cef-269">Yükleyici önceki bir sürümünü almak için:</span><span class="sxs-lookup"><span data-stu-id="a6cef-269">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="a6cef-270">Gidin [.NET indirme arşivleri](https://www.microsoft.com/net/download/archives).</span><span class="sxs-lookup"><span data-stu-id="a6cef-270">Navigate to the [.NET download archives](https://www.microsoft.com/net/download/archives).</span></span>
1. <span data-ttu-id="a6cef-271">Altında **.NET Core**, .NET Core sürümünüzü seçin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-271">Under **.NET Core**, select the .NET Core version.</span></span>
1. <span data-ttu-id="a6cef-272">İçinde **uygulamaları - çalışma zamanı çalıştırma** sütun, istenen .NET Core çalışma zamanı sürümü satırını bulur.</span><span class="sxs-lookup"><span data-stu-id="a6cef-272">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="a6cef-273">Kullanarak yükleyiciyi indirin **çalışma zamanı & paketi barındırma** bağlantı.</span><span class="sxs-lookup"><span data-stu-id="a6cef-273">Download the installer using the **Runtime & Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="a6cef-274">Bazı yükleyiciler, artık Microsoft tarafından desteklenir ve bunların sona erecek (EOL'ye) ulaştınız yayın sürümleri içerir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-274">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="a6cef-275">Daha fazla bilgi için [Destek İlkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="a6cef-275">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="a6cef-276">Barındırma paket yükleme</span><span class="sxs-lookup"><span data-stu-id="a6cef-276">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="a6cef-277">Sunucuda yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a6cef-277">Run the installer on the server.</span></span> <span data-ttu-id="a6cef-278">Aşağıdaki parametreleri, yükleyici komut kabuğunu yönetici olarak çalıştırılırken kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="a6cef-278">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="a6cef-279">`OPT_NO_ANCM=1` &ndash; ASP.NET Core modülü yükleme atlanıyor.</span><span class="sxs-lookup"><span data-stu-id="a6cef-279">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="a6cef-280">`OPT_NO_RUNTIME=1` &ndash; .NET Core çalışma zamanı yükleme atlanıyor.</span><span class="sxs-lookup"><span data-stu-id="a6cef-280">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span>
   * <span data-ttu-id="a6cef-281">`OPT_NO_SHAREDFX=1` &ndash; ASP.NET paylaşılan Framework (ASP.NET çalışma zamanı) yükleme atlanıyor.</span><span class="sxs-lookup"><span data-stu-id="a6cef-281">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span>
   * <span data-ttu-id="a6cef-282">`OPT_NO_X86=1` &ndash; X86 yükleme atlanıyor çalışma zamanları.</span><span class="sxs-lookup"><span data-stu-id="a6cef-282">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="a6cef-283">32-bit uygulamaları barındırma gerekmez, bildiğiniz durumlarda bu parametreyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="a6cef-283">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="a6cef-284">Hem 32 bit hem de 64-bit uygulamaları gelecekte barındıracak ihtimali varsa, yoksa bu parametreyi kullanın ve her iki çalışma zamanları yükleyin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-284">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="a6cef-285">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; IIS paylaşılan yapılandırması kullanmak için denetimi devre dışı olduğunda paylaşılan yapılandırma (*applicationHost.config*) IIS yüklemesi ile aynı makinede olduğu.</span><span class="sxs-lookup"><span data-stu-id="a6cef-285">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="a6cef-286">*Yalnızca, ASP.NET Core 2.2 veya üzeri barındırma Bundler yükleyicileri için de kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="a6cef-286">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="a6cef-287">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a6cef-287">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="a6cef-288">Sistemi yeniden başlatın veya yürütme **net stop olan /y**çizgidir **net start w3svc** komut kabuğundan.</span><span class="sxs-lookup"><span data-stu-id="a6cef-288">Restart the system or execute **net stop was /y**, followed by **net start w3svc** from a command shell.</span></span> <span data-ttu-id="a6cef-289">Sistemde bir değişiklik'kurmak IIS çekme yeniden bir ortam değişkenidir, yol yapılan yükleyicisi tarafından.</span><span class="sxs-lookup"><span data-stu-id="a6cef-289">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

> [!NOTE]
> <span data-ttu-id="a6cef-290">IIS paylaşılan yapılandırması hakkında daha fazla bilgi için bkz: [IIS paylaşılan yapılandırması ile ASP.NET Core Modülü](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="a6cef-290">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="a6cef-291">Visual Studio ile yayımlama sırasında Web Dağıtımı'nı yükleyin</span><span class="sxs-lookup"><span data-stu-id="a6cef-291">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="a6cef-292">Uygulamaları olan sunuculara dağıtırken [Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy), sunucuda Web Dağıtımı'nın en son sürümünü yükleyin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-292">When deploying apps to servers with [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="a6cef-293">Web Dağıtımı'nı yüklemek için kullanın [Web Platformu Yükleyicisi (Webpı)](https://www.microsoft.com/web/downloads/platform.aspx) veya doğrudan bir yükleyicisini [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="a6cef-293">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="a6cef-294">Tercih edilen yöntem, Webpı kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-294">The preferred method is to use WebPI.</span></span> <span data-ttu-id="a6cef-295">Webpı, tek başına bir kurulum ve barındırma sağlayıcıları için bir yapılandırma sağlar.</span><span class="sxs-lookup"><span data-stu-id="a6cef-295">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="a6cef-296">IIS sitesi oluştur</span><span class="sxs-lookup"><span data-stu-id="a6cef-296">Create the IIS site</span></span>

1. <span data-ttu-id="a6cef-297">Barındıran sistemde, uygulamanın yayımlanmış klasörleri ve dosyaları saklamak için bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a6cef-297">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="a6cef-298">Bir uygulamanın dağıtım Düzen açıklanan [dizin yapısı](xref:host-and-deploy/directory-structure) konu.</span><span class="sxs-lookup"><span data-stu-id="a6cef-298">An app's deployment layout is described in the [Directory Structure](xref:host-and-deploy/directory-structure) topic.</span></span>

1. <span data-ttu-id="a6cef-299">IIS Yöneticisi'nde açın sunucu düğümünde **bağlantıları** paneli.</span><span class="sxs-lookup"><span data-stu-id="a6cef-299">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="a6cef-300">Sağ **siteleri** klasör.</span><span class="sxs-lookup"><span data-stu-id="a6cef-300">Right-click the **Sites** folder.</span></span> <span data-ttu-id="a6cef-301">Seçin **Web sitesi Ekle** bağlam menüsünde.</span><span class="sxs-lookup"><span data-stu-id="a6cef-301">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="a6cef-302">Sağlayan bir **Site adı** ayarlayıp **fiziksel yolu** uygulamanın dağıtım klasörüne.</span><span class="sxs-lookup"><span data-stu-id="a6cef-302">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="a6cef-303">Sağlamak **bağlama** yapılandırma ve seçerek Web sitesi oluşturma **Tamam**:</span><span class="sxs-lookup"><span data-stu-id="a6cef-303">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Site adı, fiziksel yolunu ve ana bilgisayar adı Web sitesi Ekle adımda sağlayın.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="a6cef-305">Üst düzey joker bağlamaları (`http://*:80/` ve `http://+:80`) gereken **değil** kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-305">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="a6cef-306">Üst düzey joker bağlamaları uygulamanızı güvenlik açıklarından açabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a6cef-306">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="a6cef-307">Bu, güçlü ve zayıf joker karakterler için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-307">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="a6cef-308">Joker karakterler yerine açık bir ana bilgisayar adları kullanın.</span><span class="sxs-lookup"><span data-stu-id="a6cef-308">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="a6cef-309">Alt etki alanı joker bağlama (örneğin, `*.mysub.com`) tüm üst etki alanını denetimi bu güvenlik riski yok (başlangıcı yerine sonundan `*.com`, güvenlik açığı olan).</span><span class="sxs-lookup"><span data-stu-id="a6cef-309">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a6cef-310">Bkz: [rfc7230 bölümü-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) daha fazla bilgi için.</span><span class="sxs-lookup"><span data-stu-id="a6cef-310">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="a6cef-311">Sunucu düğümü altında seçin **uygulama havuzları**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-311">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="a6cef-312">Sitenin uygulama havuzu sağ tıklayıp **temel ayarları** bağlam menüsünde.</span><span class="sxs-lookup"><span data-stu-id="a6cef-312">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="a6cef-313">İçinde **uygulama havuzunu Düzenle** penceresinde **.NET CLR sürümü** için **yönetilen kod yok**:</span><span class="sxs-lookup"><span data-stu-id="a6cef-313">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Yönetilen kod yok, .NET CLR sürümü için ayarlayın.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="a6cef-315">ASP.NET Core, ayrı bir işlemde çalıştırır ve çalışma zamanı yönetir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-315">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="a6cef-316">ASP.NET Core değil kullanan masaüstü CLR (.NET CLR) yüklemede de&mdash;çekirdek ortak dil çalışma zamanı (CoreCLR) .NET Core için çalışan işlemi uygulamayı barındırmak için önyüklenir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-316">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="a6cef-317">Ayarı **.NET CLR sürümü** için **yönetilen kod yok** isteğe bağlı ancak önerilir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-317">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="a6cef-318">*ASP.NET Core 2.2 veya üzeri*: Bir 64-bit (x64) için [müstakil dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) kullanan [işlem içi barındırma modeli](xref:fundamentals/servers/index#in-process-hosting-model), 32 bit (x 86) işlemleri için uygulama havuzunu devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-318">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="a6cef-319">İçinde **eylemleri** kenar IIS Yöneticisi'nin > **uygulama havuzları**seçin **uygulama havuzu Varsayılanlarını Ayarla** veya **Gelişmiş ayarlar**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-319">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="a6cef-320">Bulun **etkinleştirme 32-Bit uygulamaları** ve değerine `False`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-320">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="a6cef-321">Bu ayar için dağıtılan uygulamaları etkilemez [barındırma işlemi çıkış](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="a6cef-321">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="a6cef-322">İşlem modeli kimliği uygun izinlere sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="a6cef-322">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="a6cef-323">Varsayılan uygulama havuzu kimliğini (**işlem modeli** > **kimlik**) değiştirilirse **ApplicationPoolIdentity** başka bir kimlik için doğrulayın Yeni kimlik uygulamanın klasörünü, veritabanı ve diğer gerekli kaynakları erişmek için gerekli izinlere sahip.</span><span class="sxs-lookup"><span data-stu-id="a6cef-323">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="a6cef-324">Örneğin, uygulama havuzu, okuma ve yazma erişimi burada uygulama okur ve dosyalarını yazar klasörlerine gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-324">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="a6cef-325">**Windows kimlik doğrulaması yapılandırma (isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="a6cef-325">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="a6cef-326">Daha fazla bilgi için [yapılandırma Windows kimlik doğrulaması](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a6cef-326">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="a6cef-327">Uygulamayı dağıtma</span><span class="sxs-lookup"><span data-stu-id="a6cef-327">Deploy the app</span></span>

<span data-ttu-id="a6cef-328">Uygulamayı barındıran sistemde oluşturduğunuz klasöre dağıtın.</span><span class="sxs-lookup"><span data-stu-id="a6cef-328">Deploy the app to the folder created on the hosting system.</span></span> <span data-ttu-id="a6cef-329">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) dağıtımı için önerilen mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-329">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="a6cef-330">Visual Studio ile Web dağıtımı</span><span class="sxs-lookup"><span data-stu-id="a6cef-330">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="a6cef-331">Bkz: [uygulama dağıtımı ASP.NET Core için Visual Studio yayımlama profilleri](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) konu için Web dağıtımı ile kullanmak için bir yayımlama profili oluşturmayı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-331">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="a6cef-332">Barındırma sağlayıcısı oluşturmak için bir yayımlama profili veya destek sağlar, bunların profilini indirin ve Visual Studio kullanarak içe aktarın **Yayımla** iletişim.</span><span class="sxs-lookup"><span data-stu-id="a6cef-332">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog.</span></span>

![Yayımlama iletişim kutusu sayfası](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="a6cef-334">Web dağıtımı Visual Studio'nun dışında</span><span class="sxs-lookup"><span data-stu-id="a6cef-334">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="a6cef-335">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) Visual Studio'nun dışında komut satırından da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-335">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="a6cef-336">Daha fazla bilgi için [Web dağıtım aracı](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="a6cef-336">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="a6cef-337">Alternatif Web dağıtımı</span><span class="sxs-lookup"><span data-stu-id="a6cef-337">Alternatives to Web Deploy</span></span>

<span data-ttu-id="a6cef-338">Uygulama barındırma sistemin el ile kopyalama, Xcopy, Robocopy ve PowerShell gibi taşımak için birkaç yöntemden herhangi birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="a6cef-338">Use any of several methods to move the app to the hosting system, such as manual copy, Xcopy, Robocopy, or PowerShell.</span></span>

<span data-ttu-id="a6cef-339">IIS'ye ASP.NET Core dağıtımı hakkında daha fazla bilgi için bkz. [IIS Yöneticiler için dağıtım kaynakları](#deployment-resources-for-iis-administrators) bölümü.</span><span class="sxs-lookup"><span data-stu-id="a6cef-339">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="a6cef-340">Web sitesine Gözat</span><span class="sxs-lookup"><span data-stu-id="a6cef-340">Browse the website</span></span>

![Microsoft Edge tarayıcısı IIS başlangıç sayfası yüklendi.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="a6cef-342">Kilitli dağıtım dosyaları</span><span class="sxs-lookup"><span data-stu-id="a6cef-342">Locked deployment files</span></span>

<span data-ttu-id="a6cef-343">Uygulama çalışırken, dağıtım klasörü dosyalar kilitli olmadığı.</span><span class="sxs-lookup"><span data-stu-id="a6cef-343">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="a6cef-344">Dağıtım sırasında kilitli dosyalar üzerine yazılamaz.</span><span class="sxs-lookup"><span data-stu-id="a6cef-344">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="a6cef-345">Uygulama havuzunu kullanan bir dağıtımda kilitli dosyalar serbest bırakmak için Durdur **bir** aşağıdaki yaklaşımlardan biri:</span><span class="sxs-lookup"><span data-stu-id="a6cef-345">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="a6cef-346">Web dağıtımı ve başvuru `Microsoft.NET.Sdk.Web` proje dosyasındaki.</span><span class="sxs-lookup"><span data-stu-id="a6cef-346">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="a6cef-347">Bir *app_offline.htm* dosya, web uygulama dizini kökünde yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-347">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="a6cef-348">Dosya varsa, ASP.NET Core modülü düzgün bir şekilde uygulamayı kapatır ve hizmet *app_offline.htm* dağıtımı sırasında dosya.</span><span class="sxs-lookup"><span data-stu-id="a6cef-348">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="a6cef-349">Daha fazla bilgi için [ASP.NET Core Module yapılandırma başvurusu](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="a6cef-349">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="a6cef-350">El ile uygulama havuzu IIS Yöneticisi'nde, sunucu üzerinde durdurun.</span><span class="sxs-lookup"><span data-stu-id="a6cef-350">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="a6cef-351">Silmek için PowerShell kullanma *app_offline.html* (PowerShell 5 veya üzeri gerekir):</span><span class="sxs-lookup"><span data-stu-id="a6cef-351">Use PowerShell to drop *app_offline.html* (requires PowerShell 5 or later):</span></span>

  ```PowerShell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="a6cef-352">Veri koruma</span><span class="sxs-lookup"><span data-stu-id="a6cef-352">Data protection</span></span>

<span data-ttu-id="a6cef-353">[ASP.NET Core veri koruma yığın](xref:security/data-protection/introduction) birkaç ASP.NET Core tarafından kullanılan [middlewares](xref:fundamentals/middleware/index), kimlik doğrulamasında kullanılan ara yazılımı dahil olmak üzere.</span><span class="sxs-lookup"><span data-stu-id="a6cef-353">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="a6cef-354">Veri koruma API'lerini kullanıcı kodu tarafından çağrılan değildir olsa bile, veri koruma dağıtım betiği ile veya bir kalıcı oluşturmak için kullanıcı kodunda yapılandırılmalıdır şifreleme [anahtar deposu](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="a6cef-354">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="a6cef-355">Veri koruma yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılan.</span><span class="sxs-lookup"><span data-stu-id="a6cef-355">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="a6cef-356">Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:</span><span class="sxs-lookup"><span data-stu-id="a6cef-356">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="a6cef-357">Tüm tanımlama bilgisi tabanlı kimlik doğrulama belirteçlerini geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-357">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="a6cef-358">Kullanıcıların, bir sonraki istekte tekrar oturum açmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-358">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="a6cef-359">Anahtar halkası ile korunan tüm veriler artık şifresi çözülebilir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-359">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="a6cef-360">Bu içerebilir [CSRF belirteçleri](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData tanımlama bilgilerini](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="a6cef-360">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="a6cef-361">Veri koruma anahtarı halka kalıcı hale getirmek için IIS altında yapılandırmak için kullanın **bir** aşağıdaki yaklaşımlardan biri:</span><span class="sxs-lookup"><span data-stu-id="a6cef-361">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="a6cef-362">**Veri koruma kayıt defteri anahtarları oluşturma**</span><span class="sxs-lookup"><span data-stu-id="a6cef-362">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="a6cef-363">ASP.NET Core uygulamaları tarafından kullanılan veri koruma anahtarları, uygulamalar için dış kayıt defterinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-363">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="a6cef-364">Belirli bir uygulamanın anahtarları kalıcı hale getirmek için uygulama havuzu için kayıt defteri anahtarları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a6cef-364">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="a6cef-365">Tek başına, webfarm olmayan IIS yüklemeleri [veri koruması sağlama AutoGenKeys.ps1 PowerShell Betiği](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) ile ASP.NET Core uygulaması kullanılan her bir uygulama havuzu için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-365">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="a6cef-366">Bu betik, yalnızca çalışan işlem hesabı uygulamanın uygulama havuzunun kimliği için erişilebilir HKLM Kayıt defterinde bir kayıt defteri anahtarı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a6cef-366">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="a6cef-367">Anahtarları, makine genelindeki anahtarla DPAPI kullanılarak, bekleme sırasında şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-367">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="a6cef-368">Web grubu senaryolarda, uygulama kendi veri koruma anahtarı halkası depolamak için bir UNC yolu kullanmak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-368">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="a6cef-369">Varsayılan olarak, veri koruma anahtarları şifreli değildir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-369">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="a6cef-370">Dosya izinleri ağ paylaşımı için uygulamanın çalıştığı Windows hesabı sınırlı olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="a6cef-370">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="a6cef-371">X X509 bekleyen anahtarlarınızı korumak için sertifika kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-371">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="a6cef-372">Kullanıcıların sertifikaları karşıya yüklemesine imkan tanıyan bir mekanizmayı göz önünde bulundurun: Kullanıcının güvenilen sertifika içine Yerleştir sertifikaları depolamak ve kullanıcının uygulama çalıştığı tüm makinelerde kullanılabilir emin olun.</span><span class="sxs-lookup"><span data-stu-id="a6cef-372">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="a6cef-373">Bkz: [ASP.NET Core veri koruma yapılandırma](xref:security/data-protection/configuration/overview) Ayrıntılar için.</span><span class="sxs-lookup"><span data-stu-id="a6cef-373">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="a6cef-374">**Kullanıcı profili yüklemek için IIS uygulama havuzu yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="a6cef-374">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="a6cef-375">Bu ayar **işlem modeli** bölümüne **Gelişmiş ayarlar** uygulama havuzu için.</span><span class="sxs-lookup"><span data-stu-id="a6cef-375">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="a6cef-376">Ayarlama **kullanıcı profilini Yükle** için `True`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-376">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="a6cef-377">Ayarlandığında `True`, anahtarları kullanıcı profili dizinde depolanır ve kullanıcı hesabı için özel bir anahtarla DPAPI kullanılarak korunan.</span><span class="sxs-lookup"><span data-stu-id="a6cef-377">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="a6cef-378">Anahtarlar için kaldı *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* klasör.</span><span class="sxs-lookup"><span data-stu-id="a6cef-378">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="a6cef-379">Uygulama havuzunun [setProfileEnvironment özniteliği](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) etkinleştirilmiş olması da gerekir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-379">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="a6cef-380">Varsayılan değer olan `setProfileEnvironment` olduğu `true`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-380">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="a6cef-381">(Örneğin, Windows işletim sistemi), bazı senaryolarda `setProfileEnvironment` ayarlanır `false`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-381">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="a6cef-382">Kullanıcı profili dizinde anahtarları depolanmaz, beklenen:</span><span class="sxs-lookup"><span data-stu-id="a6cef-382">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="a6cef-383">Gidin *%windir%/system32/inetsrv/config* klasör.</span><span class="sxs-lookup"><span data-stu-id="a6cef-383">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="a6cef-384">Açık *applicationHost.config* dosya.</span><span class="sxs-lookup"><span data-stu-id="a6cef-384">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="a6cef-385">Bulun `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` öğesi.</span><span class="sxs-lookup"><span data-stu-id="a6cef-385">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="a6cef-386">Onaylayın `setProfileEnvironment` özniteliği mevcut olmadığında, bunun varsayılan değeri için `true`, veya özniteliğin değerini açık olarak `true`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-386">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="a6cef-387">**Dosya sistemi anahtarı halkası deposu olarak kullanın**</span><span class="sxs-lookup"><span data-stu-id="a6cef-387">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="a6cef-388">Uygulama kodu ayarlamak [dosya sistemi bir anahtar halkası deposu olarak kullanırsınız](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="a6cef-388">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="a6cef-389">Kullanım X509 bir sertifika anahtar halkası korumak ve sertifika, güvenilen bir sertifika olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="a6cef-389">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="a6cef-390">Otomatik olarak imzalanan sertifika ise, sertifikayı güvenilen kök deponuza koyun.</span><span class="sxs-lookup"><span data-stu-id="a6cef-390">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="a6cef-391">IIS web grubunda kullanırken:</span><span class="sxs-lookup"><span data-stu-id="a6cef-391">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="a6cef-392">Tüm makineler erişebileceği bir dosya paylaşımı'nı kullanın.</span><span class="sxs-lookup"><span data-stu-id="a6cef-392">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="a6cef-393">X X509 dağıtma her makine için sertifika.</span><span class="sxs-lookup"><span data-stu-id="a6cef-393">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="a6cef-394">Yapılandırma [kod veri koruması](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="a6cef-394">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="a6cef-395">**Veri koruma için makine genelinde bir ilke ayarlayın**</span><span class="sxs-lookup"><span data-stu-id="a6cef-395">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="a6cef-396">Veri koruma sisteminde bir varsayılan ayarı desteği sınırlıdır [makineye ilke](xref:security/data-protection/configuration/machine-wide-policy) veri koruma API'lerini kullanan tüm uygulamalar için.</span><span class="sxs-lookup"><span data-stu-id="a6cef-396">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="a6cef-397">Daha fazla bilgi için bkz. <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="a6cef-397">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="a6cef-398">Sanal dizinler</span><span class="sxs-lookup"><span data-stu-id="a6cef-398">Virtual Directories</span></span>

<span data-ttu-id="a6cef-399">[IIS sanal dizinlerinin](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ile ASP.NET Core uygulamaları desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="a6cef-399">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="a6cef-400">Bir uygulama olarak barındırılan bir [alt uygulama](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="a6cef-400">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="a6cef-401">Alt uygulamalar</span><span class="sxs-lookup"><span data-stu-id="a6cef-401">Sub-applications</span></span>

<span data-ttu-id="a6cef-402">ASP.NET Core uygulaması olarak barındırılan bir [IIS alt uygulama (uygulama içi sub)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="a6cef-402">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="a6cef-403">Sub uygulamanın yolu, uygulama kök URL'SİNİN bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-403">The sub-app's path becomes part of the root app's URL.</span></span>

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a6cef-404">Bir alt uygulama ASP.NET Core modülü bir işleyici içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-404">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="a6cef-405">Modül bir alt uygulamasının işleyici olarak eklenip eklenmediğini *web.config* dosyası bir *iç sunucu hatası 500.19* hatalı yapılandırma dosyasına başvuran alındığında alt uygulama göz atmak çalışırken.</span><span class="sxs-lookup"><span data-stu-id="a6cef-405">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="a6cef-406">Aşağıdaki örnek bir yayımlanan gösterir *web.config* dosyası için bir ASP.NET Core alt uygulama:</span><span class="sxs-lookup"><span data-stu-id="a6cef-406">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="a6cef-407">ASP.NET Core uygulaması altında ASP.NET Core sub-uygulama barındırma, devralınan işleyici sub-uygulamanın açıkça Kaldır *web.config* dosyası:</span><span class="sxs-lookup"><span data-stu-id="a6cef-407">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="a6cef-408">Alt uygulama içindeki statik varlık bağlantılar tilde eğik çizgi kullanılmalıdır (`~/`) gösterimi.</span><span class="sxs-lookup"><span data-stu-id="a6cef-408">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="a6cef-409">Tilde eğik çizgi gösterimi Tetikleyiciler bir [etiketi Yardımcısı](xref:mvc/views/tag-helpers/intro) işlenmiş göreli bağlantısını için alt-uygulamanın pathbase önüne eklediğinizden.</span><span class="sxs-lookup"><span data-stu-id="a6cef-409">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="a6cef-410">Alt uygulama için `/subapp_path`, bir görüntü ile bağlantılı `src="~/image.png"` olarak işlenen `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-410">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="a6cef-411">Kök uygulamanın statik dosya ara yazılımlarını statik dosya istek işlemiyor.</span><span class="sxs-lookup"><span data-stu-id="a6cef-411">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="a6cef-412">İstek, alt uygulamanın statik dosya ara yazılımı tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-412">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="a6cef-413">Statik bir varlık, ın `src` özniteliği için mutlak bir yol ayarlayın (örneğin, `src="/image.png"`), bağlantı alt uygulamanın pathbase işlenir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-413">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="a6cef-414">Kök uygulamanın statik dosya ara yazılımlarını kök uygulamanın varlığından hizmet dener [web kök](xref:fundamentals/index#web-root), hangi sonuçlanıyor bir *404 - Bulunamadı* yanıt statik varlık kök uygulama kullanılabilir değilse.</span><span class="sxs-lookup"><span data-stu-id="a6cef-414">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="a6cef-415">ASP.NET Core uygulaması başka bir ASP.NET Core uygulaması altında bir alt uygulama olarak barındırmak için:</span><span class="sxs-lookup"><span data-stu-id="a6cef-415">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="a6cef-416">Alt uygulama için bir uygulama havuzu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a6cef-416">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="a6cef-417">Ayarlama **.NET CLR sürümü** için **yönetilen kod yok** .NET Core için çekirdek ortak dil çalışma zamanı (CoreCLR) değil Masaüstü CLR (.NET CLR) çalışan işlemindeki uygulamayı barındırmak için önyüklendiğinde olduğundan.</span><span class="sxs-lookup"><span data-stu-id="a6cef-417">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="a6cef-418">IIS Yöneticisi'nde kök sitenin kök site altında bir klasöre alt uygulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-418">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="a6cef-419">IIS Yöneticisi'nde alt uygulama klasörüne sağ tıklayıp **uygulamasına dönüştürün**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-419">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="a6cef-420">İçinde **uygulama Ekle** iletişim kutusunda, kullanmak **seçin** için düğme **uygulama havuzu** alt uygulama için oluşturduğunuz uygulama havuzuna atanamıyor.</span><span class="sxs-lookup"><span data-stu-id="a6cef-420">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="a6cef-421">Seçin **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-421">Select **OK**.</span></span>

<span data-ttu-id="a6cef-422">Bir alt uygulama ayrı bir uygulama havuzuna atamasını işlem içi barındırma modeli kullanılırken zorunludur.</span><span class="sxs-lookup"><span data-stu-id="a6cef-422">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="a6cef-423">Barındırma modeli ve ASP.NET Core modülü yapılandırma işlem hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module> ve <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="a6cef-423">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module> and <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="a6cef-424">Web.config ile IIS yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a6cef-424">Configuration of IIS with web.config</span></span>

<span data-ttu-id="a6cef-425">IIS yapılandırması tarafından etkilenir `<system.webServer>` bölümünü *web.config* işlevsel ASP.NET Core modülü ile ASP.NET Core uygulamaları için IIS senaryolar için.</span><span class="sxs-lookup"><span data-stu-id="a6cef-425">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="a6cef-426">Örneğin, IIS için dinamik sıkıştırma çalışır durumdadır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-426">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="a6cef-427">IIS dinamik sıkıştırması kullanmak için sunucu düzeyinde yapılandırılmışsa `<urlCompression>` uygulamanın öğesinde *web.config* dosya devre dışı bırakabilir, ASP.NET Core uygulaması için.</span><span class="sxs-lookup"><span data-stu-id="a6cef-427">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="a6cef-428">Daha fazla bilgi için [yapılandırma başvurusu için \<system.webServer >](/iis/configuration/system.webServer/), [ASP.NET Core Module yapılandırma başvurusu](xref:host-and-deploy/aspnet-core-module), ve [ASP.NET içeren IIS modülleri Çekirdek](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="a6cef-428">For more information, see the [configuration reference for \<system.webServer>](/iis/configuration/system.webServer/), [ASP.NET Core Module Configuration Reference](xref:host-and-deploy/aspnet-core-module), and [IIS Modules with ASP.NET Core](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="a6cef-429">(IIS 10.0 veya üzeri desteklenir) yalıtılmış uygulama havuzlarında çalışmakta olan tek tek uygulamalar için ortam değişkenlerini ayarlamak için bkz: *AppCmd.exe komut* bölümünü [ortam değişkenlerini \< environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) IIS konudaki başvuru belgeleri.</span><span class="sxs-lookup"><span data-stu-id="a6cef-429">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="a6cef-430">Web.config yapılandırma bölümlerini</span><span class="sxs-lookup"><span data-stu-id="a6cef-430">Configuration sections of web.config</span></span>

<span data-ttu-id="a6cef-431">ASP.NET 4.x uygulamalarında yapılandırma bölümlerini *web.config* yapılandırma için ASP.NET Core uygulamaları tarafından kullanılmaz:</span><span class="sxs-lookup"><span data-stu-id="a6cef-431">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="a6cef-432">ASP.NET Core uygulamaları, diğer yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-432">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="a6cef-433">Daha fazla bilgi için [yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="a6cef-433">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="a6cef-434">Uygulama havuzları</span><span class="sxs-lookup"><span data-stu-id="a6cef-434">Application Pools</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a6cef-435">Uygulama havuzu yalıtımı barındırma modeli tarafından belirlenir:</span><span class="sxs-lookup"><span data-stu-id="a6cef-435">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="a6cef-436">İşlemdeki barındırma &ndash; uygulamalarını ayrı uygulama havuzlarında çalıştırmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-436">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="a6cef-437">Barındırma işlemi çıkış &ndash; her uygulamayı kendi uygulama havuzunda çalıştırarak uygulamaları birbirinden yalıtmak öneririz.</span><span class="sxs-lookup"><span data-stu-id="a6cef-437">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="a6cef-438">IIS **Web sitesi Ekle** iletişim varsayılan olarak bir uygulama başına tek bir uygulama havuzu.</span><span class="sxs-lookup"><span data-stu-id="a6cef-438">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="a6cef-439">Olduğunda bir **Site adı** metni otomatik olarak aktarılır sağlanır **uygulama havuzu** metin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-439">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="a6cef-440">Yeni bir uygulama havuzu, bir site eklendiğinde site adı kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a6cef-440">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a6cef-441">Bir sunucuda birden fazla Web sitesi barındırma, her uygulamayı kendi uygulama havuzunda çalıştırarak uygulamaları birbirinden yalıtmak öneririz.</span><span class="sxs-lookup"><span data-stu-id="a6cef-441">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="a6cef-442">IIS **Web sitesi Ekle** iletişim varsayılan olarak bu yapılandırma.</span><span class="sxs-lookup"><span data-stu-id="a6cef-442">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="a6cef-443">Olduğunda bir **Site adı** metni otomatik olarak aktarılır sağlanır **uygulama havuzu** metin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-443">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="a6cef-444">Yeni bir uygulama havuzu, bir site eklendiğinde site adı kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a6cef-444">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

## <a name="application-pool-identity"></a><span data-ttu-id="a6cef-445">Uygulama havuzu kimliği</span><span class="sxs-lookup"><span data-stu-id="a6cef-445">Application Pool Identity</span></span>

<span data-ttu-id="a6cef-446">Bir uygulama havuzu kimlik hesabının etki alanı veya yerel hesap oluşturmak ve yönetmek zorunda kalmadan benzersiz bir hesabı altında çalıştırılacak bir uygulama sağlar.</span><span class="sxs-lookup"><span data-stu-id="a6cef-446">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="a6cef-447">IIS 8.0 veya sonraki sürümlerde, IIS Yönetici çalışan işlemi (WAS) yeni uygulama havuzu adı ile bir sanal hesabı oluşturur ve uygulama havuzunun çalışan işlemlerini bu hesap altında çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-447">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="a6cef-448">IIS Yönetim konsolunda altında **Gelişmiş ayarlar** emin olmak için uygulama havuzu, **kimlik** kullanmak üzere ayarlanmış **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="a6cef-448">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Uygulama havuzu Gelişmiş Ayarlar iletişim kutusu](index/_static/apppool-identity.png)

<span data-ttu-id="a6cef-450">IIS yönetim işlemi, Windows güvenlik sistemi, uygulama havuzunun adı ile güvenli bir tanıtıcı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a6cef-450">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="a6cef-451">Bu kimlik kullanarak kaynakları güvenliği sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-451">Resources can be secured using this identity.</span></span> <span data-ttu-id="a6cef-452">Ancak, bu kimlik bir gerçek kullanıcı hesabı değildir ve Windows kullanıcı yönetim konsolunda gösterilmesini.</span><span class="sxs-lookup"><span data-stu-id="a6cef-452">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="a6cef-453">IIS çalışan işlemi Uygulamayı yükseltilmiş erişim gerektiriyorsa, uygulamayı içeren dizine erişim denetimi listesi (ACL) değiştirin:</span><span class="sxs-lookup"><span data-stu-id="a6cef-453">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="a6cef-454">Windows Gezgini'ni açın ve dizine gidin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-454">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="a6cef-455">Sağ tıklatın ve dizin **özellikleri**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-455">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="a6cef-456">Altında **güvenlik** sekmesinde **Düzenle** düğmesine ve ardından **Ekle** düğmesi.</span><span class="sxs-lookup"><span data-stu-id="a6cef-456">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="a6cef-457">Seçin **konumları** düğmesine tıklayın ve sistem seçildiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="a6cef-457">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="a6cef-458">ENTER **IIS uygulama havuzu\\< app_pool_name >** içinde **Seçilecek nesne adlarını girin** alan.</span><span class="sxs-lookup"><span data-stu-id="a6cef-458">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="a6cef-459">Seçin **Adları Denetle** düğmesi.</span><span class="sxs-lookup"><span data-stu-id="a6cef-459">Select the **Check Names** button.</span></span> <span data-ttu-id="a6cef-460">İçin *DefaultAppPool* kullanarak adları denetle **IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-460">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="a6cef-461">Zaman **Adları Denetle** düğmesi seçili değerini **DefaultAppPool** nesne adları alanında gösterilir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-461">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="a6cef-462">Uygulama havuzu adı doğrudan nesne adları alanına girmeniz mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-462">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="a6cef-463">Kullanım **IIS uygulama havuzu\\< app_pool_name >** biçimlendirmek için nesne adı denetlenirken.</span><span class="sxs-lookup"><span data-stu-id="a6cef-463">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Kullanıcılar veya gruplar iletişim uygulama klasörü için seçin: "DefaultAppPool" uygulama havuzu adı eklenir "IIS uygulama havuzu\" "Adları Denetle."seçmeden önce nesne adları alanında](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="a6cef-465">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-465">Select **OK**.</span></span>

   ![Kullanıcılar veya gruplar iletişim uygulama klasörü için seçin: Nesne adı "DefaultAppPool", "Adları denetle" seçtikten sonra nesne adları alanında gösterilir.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="a6cef-467">Okuma &amp; Yürütme izinleri varsayılan verilmesi.</span><span class="sxs-lookup"><span data-stu-id="a6cef-467">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="a6cef-468">Gerektiğinde ek izinler sağlar.</span><span class="sxs-lookup"><span data-stu-id="a6cef-468">Provide additional permissions as needed.</span></span>

<span data-ttu-id="a6cef-469">Erişim de verilebilir bir komut istemi kullanarak **ICACLS** aracı.</span><span class="sxs-lookup"><span data-stu-id="a6cef-469">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="a6cef-470">Kullanarak *DefaultAppPool* örnek olarak, aşağıdaki komutu kullanılır:</span><span class="sxs-lookup"><span data-stu-id="a6cef-470">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="a6cef-471">Daha fazla bilgi için [icacls](/windows-server/administration/windows-commands/icacls) konu.</span><span class="sxs-lookup"><span data-stu-id="a6cef-471">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="a6cef-472">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="a6cef-472">HTTP/2 support</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a6cef-473">[HTTP/2](https://httpwg.org/specs/rfc7540.html) ile ASP.NET Core aşağıdaki IIS dağıtım senaryolarında desteklenir:</span><span class="sxs-lookup"><span data-stu-id="a6cef-473">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="a6cef-474">İşlem içi</span><span class="sxs-lookup"><span data-stu-id="a6cef-474">In-process</span></span>
  * <span data-ttu-id="a6cef-475">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="a6cef-475">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="a6cef-476">TLS 1.2 veya sonraki bir bağlantı</span><span class="sxs-lookup"><span data-stu-id="a6cef-476">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="a6cef-477">İşlem dışı</span><span class="sxs-lookup"><span data-stu-id="a6cef-477">Out-of-process</span></span>
  * <span data-ttu-id="a6cef-478">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="a6cef-478">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="a6cef-479">Genel kullanıma yönelik uç sunucu bağlantıları için ters Ara sunucu bağlantısı ancak HTTP/2 kullanın [Kestrel sunucu](xref:fundamentals/servers/kestrel) HTTP/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-479">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="a6cef-480">TLS 1.2 veya sonraki bir bağlantı</span><span class="sxs-lookup"><span data-stu-id="a6cef-480">TLS 1.2 or later connection</span></span>

<span data-ttu-id="a6cef-481">Bir HTTP/2 bağlantı kurulduğunda, işlem içi dağıtımı için [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-481">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="a6cef-482">Bir HTTP/2 bağlantı kurulduğunda, bir işlem dışı dağıtım için [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-482">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="a6cef-483">İşlem içi ve dışı işlem barındırma modelleri hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="a6cef-483">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a6cef-484">[HTTP/2](https://httpwg.org/specs/rfc7540.html) aşağıdaki temel gereksinimlere işlem dışı dağıtımları için desteklenir:</span><span class="sxs-lookup"><span data-stu-id="a6cef-484">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="a6cef-485">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="a6cef-485">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="a6cef-486">Genel kullanıma yönelik uç sunucu bağlantıları için ters Ara sunucu bağlantısı ancak HTTP/2 kullanın [Kestrel sunucu](xref:fundamentals/servers/kestrel) HTTP/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="a6cef-486">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="a6cef-487">Hedef çerçeve: HTTP/2 bağlantı beri işlem dışı dağıtımlar için geçerli değildir tamamen IIS tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-487">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="a6cef-488">TLS 1.2 veya sonraki bir bağlantı</span><span class="sxs-lookup"><span data-stu-id="a6cef-488">TLS 1.2 or later connection</span></span>

<span data-ttu-id="a6cef-489">Bir HTTP/2 bağlantı kurulur, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="a6cef-489">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

::: moniker-end

<span data-ttu-id="a6cef-490">HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-490">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="a6cef-491">Bir HTTP/2 bağlantı değil, bağlantılar, HTTP/1.1 geri döner.</span><span class="sxs-lookup"><span data-stu-id="a6cef-491">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="a6cef-492">IIS dağıtımları olan HTTP/2 yapılandırma hakkında daha fazla bilgi için bkz. [HTTP/2 IIS'de](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="a6cef-492">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="a6cef-493">CORS denetim öncesi isteği</span><span class="sxs-lookup"><span data-stu-id="a6cef-493">CORS preflight requests</span></span>

<span data-ttu-id="a6cef-494">*Bu bölüm, yalnızca .NET Framework'ü hedefleyen ASP.NET Core uygulamaları için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="a6cef-494">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="a6cef-495">ASP.NET Core uygulaması için hedefleyen .NET Framework'IIS içinde varsayılan olarak OPTIONS istekleri uygulamaya geçirilen değildir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-495">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="a6cef-496">Uygulamanın IIS işleyicileri yapılandırma hakkında bilgi edinmek için *web.config* seçenekleri isteklerini iletmek için bkz: [ASP.NET Web API 2'de kaynaklar arası istekleri etkinleştirme: CORS işleyişi](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="a6cef-496">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="a6cef-497">Uygulama Başlatma modülü ve boşta kalma zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="a6cef-497">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="a6cef-498">IIS içinde ASP.NET Core modülü sürüm 2 barındırıldığında:</span><span class="sxs-lookup"><span data-stu-id="a6cef-498">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="a6cef-499">[Uygulama Başlatma modülü](#application-initialization-module) &ndash; uygulamanın barındırılan [işlem içi](xref:fundamentals/servers/index#in-process-hosting-model) veya [işlem dışı](xref:fundamentals/servers/index#out-of-process-hosting-model), bir çalışan işlemin yeniden veya sunucuda otomatik olarak başlatılacak şekilde yapılandırılabilir yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="a6cef-499">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](xref:fundamentals/servers/index#in-process-hosting-model) or [out-of-process](xref:fundamentals/servers/index#out-of-process-hosting-model), can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="a6cef-500">[Boşta kalma zaman aşımı](#idle-timeout) &ndash; uygulamanın barındırılan [işlem içi](xref:fundamentals/servers/index#in-process-hosting-model) için zaman aşımı etkin olmadığı dönemler sırasında yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-500">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](xref:fundamentals/servers/index#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="a6cef-501">Uygulama Başlatma modülü</span><span class="sxs-lookup"><span data-stu-id="a6cef-501">Application Initialization Module</span></span>

<span data-ttu-id="a6cef-502">*İşlemdeki barındırılan uygulamaları ve işlem dışı için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="a6cef-502">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="a6cef-503">[IIS uygulama başlatma](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) uygulama havuzunu başlatır veya geri dönüştürüldüğünde, uygulamaya bir HTTP isteği gönderir, bir IIS özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="a6cef-503">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="a6cef-504">İstek, uygulamanın başlatılması tetikler.</span><span class="sxs-lookup"><span data-stu-id="a6cef-504">The request triggers the app to start.</span></span> <span data-ttu-id="a6cef-505">Varsayılan olarak, IIS uygulama kök URL'si için bir istek sorunları (`/`) uygulamayı başlatmak için (bkz [ek kaynaklar](#application-initialization-module-and-idle-timeout-additional-resources) yapılandırması hakkında daha fazla ayrıntı için).</span><span class="sxs-lookup"><span data-stu-id="a6cef-505">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="a6cef-506">IIS uygulama başlatma rolü özelliği etkin olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="a6cef-506">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="a6cef-507">Windows 7 veya üzeri Masaüstü sistemlerinde IIS yerel olarak kullanırken:</span><span class="sxs-lookup"><span data-stu-id="a6cef-507">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="a6cef-508">Gidin **Denetim Masası** > **programlar** > **programlar ve Özellikler** > **kapatma Windows özellikleri hakkında ya da kapalı** (ekranın sol).</span><span class="sxs-lookup"><span data-stu-id="a6cef-508">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="a6cef-509">Açık **Internet Information Services** > **World Wide Web Hizmetleri** > **uygulama geliştirme özellikleri**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-509">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="a6cef-510">Onay kutusunu seçin **uygulama başlatma**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-510">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="a6cef-511">Windows Server 2008 R2 veya sonraki sürümlerde:</span><span class="sxs-lookup"><span data-stu-id="a6cef-511">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="a6cef-512">Açık **rol ve Özellik Ekle Sihirbazı**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-512">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="a6cef-513">İçinde **rol hizmetlerini seçin** paneli, açık **uygulama geliştirme** düğümü.</span><span class="sxs-lookup"><span data-stu-id="a6cef-513">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="a6cef-514">Onay kutusunu seçin **uygulama başlatma**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-514">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="a6cef-515">Site için uygulama başlatma modülü etkinleştirmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="a6cef-515">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="a6cef-516">IIS Yöneticisi'ni kullanarak:</span><span class="sxs-lookup"><span data-stu-id="a6cef-516">Using IIS Manager:</span></span>

  1. <span data-ttu-id="a6cef-517">Seçin **uygulama havuzları** içinde **bağlantıları** paneli.</span><span class="sxs-lookup"><span data-stu-id="a6cef-517">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="a6cef-518">Uygulamanın uygulama havuzu listesi seçip sağ **Gelişmiş ayarlar**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-518">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="a6cef-519">Varsayılan **modunu Başlat** olduğu **OnDemand**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-519">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="a6cef-520">Ayarlama **başlangıç modu** için **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-520">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="a6cef-521">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-521">Select **OK**.</span></span>
  1. <span data-ttu-id="a6cef-522">Açık **siteleri** düğümünde **bağlantıları** paneli.</span><span class="sxs-lookup"><span data-stu-id="a6cef-522">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="a6cef-523">Uygulamayı sağ tıklayıp **Web sitesini Yönet** > **Gelişmiş ayarlar**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-523">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="a6cef-524">Varsayılan **önceden yükleme etkin** ayardır **False**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-524">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="a6cef-525">Ayarlama **önceden yükleme etkin** için **True**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-525">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="a6cef-526">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-526">Select **OK**.</span></span>

* <span data-ttu-id="a6cef-527">Kullanarak *web.config*, ekleme `<applicationInitialization>` öğeyle `doAppInitAfterRestart` kümesine `true` için `<system.webServer>` uygulamanın öğelerindeki *web.config* dosyası:</span><span class="sxs-lookup"><span data-stu-id="a6cef-527">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="a6cef-528">Boşta kalma zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="a6cef-528">Idle Timeout</span></span>

<span data-ttu-id="a6cef-529">*Yalnızca barındırılan uygulamaları işlem içinde geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="a6cef-529">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="a6cef-530">Uygulama boşa gelen önlemek için IIS Yöneticisi'ni kullanarak uygulama havuzunun boşta kalma zaman aşımını ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="a6cef-530">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="a6cef-531">Seçin **uygulama havuzları** içinde **bağlantıları** paneli.</span><span class="sxs-lookup"><span data-stu-id="a6cef-531">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="a6cef-532">Uygulamanın uygulama havuzu listesi seçip sağ **Gelişmiş ayarlar**.</span><span class="sxs-lookup"><span data-stu-id="a6cef-532">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="a6cef-533">Varsayılan **boşta kalma zaman aşımı (dakika)** olduğu **20** dakika.</span><span class="sxs-lookup"><span data-stu-id="a6cef-533">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="a6cef-534">Ayarlama **boşta kalma zaman aşımı (dakika)** için **0** (sıfır).</span><span class="sxs-lookup"><span data-stu-id="a6cef-534">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="a6cef-535">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-535">Select **OK**.</span></span>
1. <span data-ttu-id="a6cef-536">Çalışan işlemi geri dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="a6cef-536">Recycle the worker process.</span></span>

<span data-ttu-id="a6cef-537">Barındırılan uygulamaları engellemek için [işlem dışı](xref:fundamentals/servers/index#out-of-process-hosting-model) gelen aşımından aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="a6cef-537">To prevent apps hosted [out-of-process](xref:fundamentals/servers/index#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="a6cef-538">Uygulamayı bir dış hizmetten çalışmasını sağlamak için ping yapın.</span><span class="sxs-lookup"><span data-stu-id="a6cef-538">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="a6cef-539">Uygulamayı yalnızca arka plan Hizmetleri barındırıyorsa, IIS barındırma kaçının ve kullanan bir [ASP.NET Core uygulaması barındırmak üzere Windows hizmet](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="a6cef-539">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="a6cef-540">Uygulama Başlatma modülü ve boşta kalma zaman aşımı ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a6cef-540">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="a6cef-541">IIS 8.0 uygulama başlatma</span><span class="sxs-lookup"><span data-stu-id="a6cef-541">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="a6cef-542">[Uygulama başlatma \<applicationInitialization >](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="a6cef-542">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="a6cef-543">[İşlem modeli ayarları için bir uygulama havuzu \<processModel >](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="a6cef-543">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

::: moniker-end

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="a6cef-544">IIS Yöneticiler için dağıtım kaynakları</span><span class="sxs-lookup"><span data-stu-id="a6cef-544">Deployment resources for IIS administrators</span></span>

<span data-ttu-id="a6cef-545">IIS IIS belgelerinde kapsamlı hakkında bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-545">Learn about IIS in-depth in the IIS documentation.</span></span>  
[<span data-ttu-id="a6cef-546">IIS belgeleri</span><span class="sxs-lookup"><span data-stu-id="a6cef-546">IIS documentation</span></span>](/iis)

<span data-ttu-id="a6cef-547">.NET Core uygulaması dağıtım modelleri hakkında bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-547">Learn about .NET Core app deployment models.</span></span>  
[<span data-ttu-id="a6cef-548">.NET core uygulama dağıtımı</span><span class="sxs-lookup"><span data-stu-id="a6cef-548">.NET Core application deployment</span></span>](/dotnet/core/deploying/)

<span data-ttu-id="a6cef-549">ASP.NET Core modülü Kestrel web sunucusu, bir ters proxy sunucusu olarak IIS veya IIS Express kullanacak şekilde nasıl olanak tanıdığını öğrenin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-549">Learn how the ASP.NET Core Module allows the Kestrel web server to use IIS or IIS Express as a reverse proxy server.</span></span>  
[<span data-ttu-id="a6cef-550">ASP.NET Core Modülü</span><span class="sxs-lookup"><span data-stu-id="a6cef-550">ASP.NET Core Module</span></span>](xref:host-and-deploy/aspnet-core-module)

<span data-ttu-id="a6cef-551">ASP.NET Core uygulamaları barındırmak için gereken ASP.NET Core modülü yapılandırmayı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-551">Learn how to configure the ASP.NET Core Module for hosting ASP.NET Core apps.</span></span>  
[<span data-ttu-id="a6cef-552">ASP.NET Core Module yapılandırma başvurusu</span><span class="sxs-lookup"><span data-stu-id="a6cef-552">ASP.NET Core Module configuration reference</span></span>](xref:host-and-deploy/aspnet-core-module)

<span data-ttu-id="a6cef-553">Dizin yapısı, yayımlanmış ASP.NET Core uygulamaları hakkında bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-553">Learn about the directory structure of published ASP.NET Core apps.</span></span>  
[<span data-ttu-id="a6cef-554">Dizin yapısı</span><span class="sxs-lookup"><span data-stu-id="a6cef-554">Directory structure</span></span>](xref:host-and-deploy/directory-structure)

<span data-ttu-id="a6cef-555">ASP.NET Core uygulamaları ve IIS modüllerini yönetmek nasıl etkin ve etkin olmayan IIS modülleri keşfedin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-555">Discover active and inactive IIS modules for ASP.NET Core apps and how to manage IIS modules.</span></span>  
[<span data-ttu-id="a6cef-556">IIS modülleri</span><span class="sxs-lookup"><span data-stu-id="a6cef-556">IIS modules</span></span>](xref:host-and-deploy/iis/troubleshoot)

<span data-ttu-id="a6cef-557">ASP.NET Core uygulamaları IIS dağıtımları sorunları tanılamayı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="a6cef-557">Learn how to diagnose problems with IIS deployments of ASP.NET Core apps.</span></span>  
[<span data-ttu-id="a6cef-558">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="a6cef-558">Troubleshoot</span></span>](xref:host-and-deploy/iis/troubleshoot)

<span data-ttu-id="a6cef-559">Sık karşılaşılan barındırırken IIS üzerinde ASP.NET Core uygulamaları ayırmak.</span><span class="sxs-lookup"><span data-stu-id="a6cef-559">Distinguish common errors when hosting ASP.NET Core apps on IIS.</span></span>  
[<span data-ttu-id="a6cef-560">Azure App Service ve IIS için sık karşılaşılan hatalar başvurusu</span><span class="sxs-lookup"><span data-stu-id="a6cef-560">Common errors reference for Azure App Service and IIS</span></span>](xref:host-and-deploy/azure-iis-errors-reference)

## <a name="additional-resources"></a><span data-ttu-id="a6cef-561">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a6cef-561">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="a6cef-562">ASP.NET Core'a giriş</span><span class="sxs-lookup"><span data-stu-id="a6cef-562">Introduction to ASP.NET Core</span></span>](xref:index)
* [<span data-ttu-id="a6cef-563">Resmi Microsoft IIS sitesi</span><span class="sxs-lookup"><span data-stu-id="a6cef-563">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="a6cef-564">Windows Server Teknik İçerik Kitaplığı</span><span class="sxs-lookup"><span data-stu-id="a6cef-564">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="a6cef-565">IIS HTTP/2</span><span class="sxs-lookup"><span data-stu-id="a6cef-565">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>
