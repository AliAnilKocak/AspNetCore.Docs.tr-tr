---
title: ASP.NET Core Web sunucu uygulamalarında
author: guardrex
description: ASP.NET Core için web sunucuları Kestrel ve HTTP.sys keşfedin. Bir sunucu seçin ve ne zaman bir ters proxy sunucusu kullanmayı öğrenin.
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/servers/index
ms.openlocfilehash: 2c209942ed219b6d6ca309d8aba94b264d421158
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637748"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="33fd8-104">ASP.NET Core Web sunucu uygulamalarında</span><span class="sxs-lookup"><span data-stu-id="33fd8-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="33fd8-105">Tarafından [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), ve [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="33fd8-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="33fd8-106">ASP.NET Core uygulaması bir işlemde HTTP sunucusu uygulamasını ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="33fd8-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="33fd8-107">HTTP istekleri ve bunları uygulamaya bir dizi ortaya çıkarır sunucusu uygulaması dinler [istek özellikleri](xref:fundamentals/request-features) içine oluşan bir <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="33fd8-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="33fd8-108">Windows</span><span class="sxs-lookup"><span data-stu-id="33fd8-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="33fd8-109">ASP.NET Core aşağıdaki ile birlikte gelir:</span><span class="sxs-lookup"><span data-stu-id="33fd8-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="33fd8-110">[Kestrel'i sunucu](xref:fundamentals/servers/kestrel) platformlar arası HTTP sunucusu uygulamasını, varsayılan olan.</span><span class="sxs-lookup"><span data-stu-id="33fd8-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="33fd8-111">IIS HTTP sunucusu bir [işlem sunucusu](#in-process-hosting-model) IIS için.</span><span class="sxs-lookup"><span data-stu-id="33fd8-111">IIS HTTP Server is an [in-process server](#in-process-hosting-model) for IIS.</span></span>
* <span data-ttu-id="33fd8-112">[HTTP.sys sunucu](xref:fundamentals/servers/httpsys) yalnızca Windows HTTP sunucu dayanır [HTTP.sys çekirdek sürücüsü ve HTTP Sunucusu API](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="33fd8-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="33fd8-113">Kullanırken [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), uygulama ya da çalışır:</span><span class="sxs-lookup"><span data-stu-id="33fd8-113">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="33fd8-114">IIS çalışan işlemi ile aynı işlemde ( [işlem içi barındırma modeli](#in-process-hosting-model)) ile [IIS HTTP sunucusu](#iis-http-server).</span><span class="sxs-lookup"><span data-stu-id="33fd8-114">In the same process as the IIS worker process (the [in-process hosting model](#in-process-hosting-model)) with the [IIS HTTP Server](#iis-http-server).</span></span> <span data-ttu-id="33fd8-115">*İşlem içi* önerilen yapılandırmadır.</span><span class="sxs-lookup"><span data-stu-id="33fd8-115">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="33fd8-116">Bir işlemde ayrı IIS çalışan işleminden ( [işlem dışı barındırma modeli](#out-of-process-hosting-model)) ile [Kestrel sunucu](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="33fd8-116">In a process separate from the IIS worker process (the [out-of-process hosting model](#out-of-process-hosting-model)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="33fd8-117">[ASP.NET Core Modülü](xref:host-and-deploy/aspnet-core-module) IIS ve işlem içi IIS HTTP sunucusu veya Kestrel arasında yerel IIS istekleri işleyen yerel bir IIS modülüdür.</span><span class="sxs-lookup"><span data-stu-id="33fd8-117">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="33fd8-118">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="33fd8-118">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="33fd8-119">Barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="33fd8-119">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="33fd8-120">İşlem içi barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="33fd8-120">In-process hosting model</span></span>

<span data-ttu-id="33fd8-121">İşlemdeki barındırma, bir ASP.NET Core kullanarak uygulama IIS çalışan işlemi ile aynı işlemde çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="33fd8-121">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="33fd8-122">Bu, geri döngü bağdaştırıcı, giden ağ trafiğini aynı makinede geri döndüren bir ağ arabirimi üzerinden işlem dışı performans cezası proxy istekleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="33fd8-122">This removes the out-of-process performance penalty of proxying requests over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="33fd8-123">IIS işleme Süreci Yönetimi ile [Windows İşlem Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="33fd8-123">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="33fd8-124">ASP.NET Core Modülü:</span><span class="sxs-lookup"><span data-stu-id="33fd8-124">The ASP.NET Core Module:</span></span>

* <span data-ttu-id="33fd8-125">Uygulama başlatmayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-125">Performs app initialization.</span></span>
  * <span data-ttu-id="33fd8-126">Yükleri [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="33fd8-126">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="33fd8-127">Çağrıları `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="33fd8-127">Calls `Program.Main`.</span></span>
* <span data-ttu-id="33fd8-128">Yerel IIS istek ömrünü işler.</span><span class="sxs-lookup"><span data-stu-id="33fd8-128">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="33fd8-129">Aşağıdaki diyagram IIS, ASP.NET Core modülü arasındaki ilişkiyi gösterir ve uygulama işlemde barındırılan:</span><span class="sxs-lookup"><span data-stu-id="33fd8-129">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![ASP.NET Core Modülü](_static/ancm-inprocess.png)

<span data-ttu-id="33fd8-131">Bir istek için çekirdek modu HTTP.sys sürücüsünü Web'den ulaşır.</span><span class="sxs-lookup"><span data-stu-id="33fd8-131">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="33fd8-132">Sürücü IIS Web sitesinin yapılandırılan bağlantı noktası, genellikle 80 (HTTP) veya 443 (HTTPS) üzerinde yerel istek yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-132">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="33fd8-133">Modülün yerel isteği alır ve IIS HTTP sunucusuna geçirir (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="33fd8-133">The module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="33fd8-134">IIS HTTP isteği Yerelden yönetilene dönüştürür IIS için bir işlem sunucusu uygulama sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="33fd8-134">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="33fd8-135">IIS HTTP sunucusu isteği işledikten sonra ASP.NET Core ara yazılım ardışık düzende isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-135">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="33fd8-136">Ara yazılım ardışık düzenini isteği işler ve olarak geçirir bir `HttpContext` örneği uygulama mantığına.</span><span class="sxs-lookup"><span data-stu-id="33fd8-136">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="33fd8-137">Uygulamanın yanıt IIS, yeniden istek başlatılan istemciye hangi bildirim geçirilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-137">The app's response is passed back to IIS, which pushes it back out to the client that initiated the request.</span></span>

<span data-ttu-id="33fd8-138">Barındırma işlemi içinde olan mevcut uygulamalar için katılımı ancak [yeni dotnet](/dotnet/core/tools/dotnet-new) işlemdeki tüm IIS ve IIS Express senaryoları için barındırma modelini varsayılan şablonları.</span><span class="sxs-lookup"><span data-stu-id="33fd8-138">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="33fd8-139">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="33fd8-139">Out-of-process hosting model</span></span>

<span data-ttu-id="33fd8-140">Bir işlem içinde çalıştırmak, ASP.NET Core uygulamaları IIS çalışan işleminden ayrı olduğundan, işlem yönetimi modül işler.</span><span class="sxs-lookup"><span data-stu-id="33fd8-140">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="33fd8-141">İlk istek ulaştığında ve kapatılır veya çöküyor uygulama yeniden başlatmalarını modülü ASP.NET Core uygulaması için bir işlem başlar.</span><span class="sxs-lookup"><span data-stu-id="33fd8-141">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="33fd8-142">Bu aslında aynı işlemde çalışan tarafından yönetilen uygulamalarla görüldüğü gibi davranıştır [Windows İşlem Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="33fd8-142">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="33fd8-143">Aşağıdaki diyagram IIS, ASP.NET Core modülü arasındaki ilişkiyi gösterir ve uygulama barındırılan işlem dışı:</span><span class="sxs-lookup"><span data-stu-id="33fd8-143">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core Modülü](_static/ancm-outofprocess.png)

<span data-ttu-id="33fd8-145">İstekleri için çekirdek modu HTTP.sys sürücüsünü Web'den ulaşır.</span><span class="sxs-lookup"><span data-stu-id="33fd8-145">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="33fd8-146">Sürücü istekler IIS Web sitesinin yapılandırılan bağlantı noktası, genellikle 80 (HTTP) veya 443 (HTTPS) üzerinde yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-146">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="33fd8-147">Modül Kestrel rastgele bağlantı noktası için 80 veya 443 bağlantı noktası olmadığından uygulama isteklerini iletir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-147">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="33fd8-148">Modülü başlatma sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve IIS tümleştirme ara yazılımı üzerinde dinlemek üzere yapılandırır `http://localhost:{PORT}`.</span><span class="sxs-lookup"><span data-stu-id="33fd8-148">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="33fd8-149">Ek denetimler gerçekleştirilir ve modülünden değilsiniz kaynaklı istekler reddedilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-149">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="33fd8-150">İstekler HTTP üzerinden HTTPS üzerinden IIS tarafından alınan bile iletilir modülü HTTPS iletmeyi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="33fd8-150">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="33fd8-151">Modül istekten Kestrel seçer sonra ASP.NET Core ara yazılım ardışık düzende isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-151">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="33fd8-152">Ara yazılım ardışık düzenini isteği işler ve olarak geçirir bir `HttpContext` örneği uygulama mantığına.</span><span class="sxs-lookup"><span data-stu-id="33fd8-152">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="33fd8-153">IIS tümleştirme tarafından eklenen bir ara yazılım istek için Kestrel iletmek için hesap için şema, uzak IP ve pathbase güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-153">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="33fd8-154">Uygulamanın yanıt IIS, yeniden istek başlatılan HTTP istemcisi için hangi bildirim geçirilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-154">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="33fd8-155">IIS ve ASP.NET Core Module yapılandırma yönergeleri için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="33fd8-155">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="33fd8-156">macOS</span><span class="sxs-lookup"><span data-stu-id="33fd8-156">macOS</span></span>](#tab/macos)

<span data-ttu-id="33fd8-157">ASP.NET Core ile birlikte gelir [Kestrel sunucu](xref:fundamentals/servers/kestrel), platformlar arası bir HTTP sunucusu, varsayılan olan.</span><span class="sxs-lookup"><span data-stu-id="33fd8-157">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="33fd8-158">Linux</span><span class="sxs-lookup"><span data-stu-id="33fd8-158">Linux</span></span>](#tab/linux)

<span data-ttu-id="33fd8-159">ASP.NET Core ile birlikte gelir [Kestrel sunucu](xref:fundamentals/servers/kestrel), platformlar arası bir HTTP sunucusu, varsayılan olan.</span><span class="sxs-lookup"><span data-stu-id="33fd8-159">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="33fd8-160">Windows</span><span class="sxs-lookup"><span data-stu-id="33fd8-160">Windows</span></span>](#tab/windows)

<span data-ttu-id="33fd8-161">ASP.NET Core aşağıdaki ile birlikte gelir:</span><span class="sxs-lookup"><span data-stu-id="33fd8-161">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="33fd8-162">[Kestrel'i sunucu](xref:fundamentals/servers/kestrel) olduğundan varsayılan, platformlar arası bir HTTP sunucusu.</span><span class="sxs-lookup"><span data-stu-id="33fd8-162">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="33fd8-163">[HTTP.sys sunucu](xref:fundamentals/servers/httpsys) yalnızca Windows HTTP sunucu dayanır [HTTP.sys çekirdek sürücüsü ve HTTP Sunucusu API](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="33fd8-163">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="33fd8-164">Kullanırken [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), uygulama IIS çalışan işleminden ayrı bir işlemde çalışır (*işlem dışı*) ile [Kestrel sunucu](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="33fd8-164">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="33fd8-165">Bir işlem içinde çalıştırmak, ASP.NET Core uygulamaları IIS çalışan işleminden ayrı olduğundan, işlem yönetimi modül işler.</span><span class="sxs-lookup"><span data-stu-id="33fd8-165">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="33fd8-166">İlk istek ulaştığında ve kapatılır veya çöküyor uygulama yeniden başlatmalarını modülü ASP.NET Core uygulaması için bir işlem başlar.</span><span class="sxs-lookup"><span data-stu-id="33fd8-166">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="33fd8-167">Bu aslında aynı işlemde çalışan tarafından yönetilen uygulamalarla görüldüğü gibi davranıştır [Windows İşlem Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="33fd8-167">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="33fd8-168">Aşağıdaki diyagram IIS, ASP.NET Core modülü arasındaki ilişkiyi gösterir ve uygulama barındırılan işlem dışı:</span><span class="sxs-lookup"><span data-stu-id="33fd8-168">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core Modülü](_static/ancm-outofprocess.png)

<span data-ttu-id="33fd8-170">İstekleri için çekirdek modu HTTP.sys sürücüsünü Web'den ulaşır.</span><span class="sxs-lookup"><span data-stu-id="33fd8-170">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="33fd8-171">Sürücü istekler IIS Web sitesinin yapılandırılan bağlantı noktası, genellikle 80 (HTTP) veya 443 (HTTPS) üzerinde yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-171">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="33fd8-172">Modül Kestrel rastgele bağlantı noktası için 80 veya 443 bağlantı noktası olmadığından uygulama isteklerini iletir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-172">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="33fd8-173">Modülü başlatma sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve IIS tümleştirme ara yazılımı üzerinde dinlemek üzere yapılandırır `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="33fd8-173">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="33fd8-174">Ek denetimler gerçekleştirilir ve modülünden değilsiniz kaynaklı istekler reddedilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-174">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="33fd8-175">İstekler HTTP üzerinden HTTPS üzerinden IIS tarafından alınan bile iletilir modülü HTTPS iletmeyi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="33fd8-175">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="33fd8-176">Modül istekten Kestrel seçer sonra ASP.NET Core ara yazılım ardışık düzende isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-176">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="33fd8-177">Ara yazılım ardışık düzenini isteği işler ve olarak geçirir bir `HttpContext` örneği uygulama mantığına.</span><span class="sxs-lookup"><span data-stu-id="33fd8-177">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="33fd8-178">IIS tümleştirme tarafından eklenen bir ara yazılım istek için Kestrel iletmek için hesap için şema, uzak IP ve pathbase güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-178">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="33fd8-179">Uygulamanın yanıt IIS, yeniden istek başlatılan HTTP istemcisi için hangi bildirim geçirilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-179">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="33fd8-180">IIS ve ASP.NET Core Module yapılandırma yönergeleri için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="33fd8-180">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="33fd8-181">macOS</span><span class="sxs-lookup"><span data-stu-id="33fd8-181">macOS</span></span>](#tab/macos)

<span data-ttu-id="33fd8-182">ASP.NET Core ile birlikte gelir [Kestrel sunucu](xref:fundamentals/servers/kestrel), platformlar arası bir HTTP sunucusu, varsayılan olan.</span><span class="sxs-lookup"><span data-stu-id="33fd8-182">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="33fd8-183">Linux</span><span class="sxs-lookup"><span data-stu-id="33fd8-183">Linux</span></span>](#tab/linux)

<span data-ttu-id="33fd8-184">ASP.NET Core ile birlikte gelir [Kestrel sunucu](xref:fundamentals/servers/kestrel), platformlar arası bir HTTP sunucusu, varsayılan olan.</span><span class="sxs-lookup"><span data-stu-id="33fd8-184">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="33fd8-185">Kestrel'i</span><span class="sxs-lookup"><span data-stu-id="33fd8-185">Kestrel</span></span>

<span data-ttu-id="33fd8-186">Kestrel'i ASP.NET Core proje şablonları dahil varsayılan web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="33fd8-186">Kestrel is the default web server included in ASP.NET Core project templates.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="33fd8-187">Kestrel'i kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="33fd8-187">Kestrel can be used:</span></span>

* <span data-ttu-id="33fd8-188">Tek başına bir uç sunucusu olarak doğrudan Internet dahil olmak üzere, bir ağ isteği işleniyor.</span><span class="sxs-lookup"><span data-stu-id="33fd8-188">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel'i ters Ara sunucu olmadan Internet ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="33fd8-190">İle bir *ters Ara sunucu*, gibi [Internet Information Services (IIS)](https://www.iis.net/), [Ngınx](http://nginx.org), veya [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="33fd8-190">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="33fd8-191">Bir tersine Ara sunucunun Internet'ten HTTP isteklerini alır ve bunları Kestrel için iletir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-191">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel'i dolaylı olarak IIS, Ngınx veya Apache gibi bir ters Ara sunucu üzerinden Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="33fd8-193">Barındırma ya da yapılandırma&mdash;ile veya ters Ara sunucu olmadan&mdash;ASP.NET Core 2.1 veya daha sonraki uygulamalar için desteklenir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-193">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported for ASP.NET Core 2.1 or later apps.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="33fd8-194">Uygulamayı yalnızca bir iç ağ gelen istekleri kabul ederse Kestrel kendisi tarafından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-194">If the app only accepts requests from an internal network, Kestrel can be used by itself.</span></span>

![Kestrel'i iç ağa ile doğrudan iletişim kurar.](kestrel/_static/kestrel-to-internal.png)

<span data-ttu-id="33fd8-196">Uygulamayı Internet erişimine açıktır, Kestrel kullanmalısınız bir *ters Ara sunucu*, gibi [Internet Information Services (IIS)](https://www.iis.net/), [Ngınx](http://nginx.org), veya [Apache ](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="33fd8-196">If the app is exposed to the Internet, Kestrel must use a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="33fd8-197">Bir tersine Ara sunucunun Internet'ten HTTP isteklerini alır ve bunları Kestrel için iletir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-197">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

![Kestrel'i dolaylı olarak IIS, Ngınx veya Apache gibi bir ters Ara sunucu üzerinden Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="33fd8-199">Internet güvenlik olduğundan doğrudan sunulan genel kullanıma yönelik uç sunucusu dağıtımları için ters Ara sunucu kullanmak için en önemli nedeni.</span><span class="sxs-lookup"><span data-stu-id="33fd8-199">The most important reason for using a reverse proxy for public-facing edge server deployments that are exposed directly the Internet is security.</span></span> <span data-ttu-id="33fd8-200">Kestrel'i 1.x sürümlerini Internet'ten saldırılarına karşı korumak için önemli güvenlik özellikleri dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-200">The 1.x versions of Kestrel don't include important security features to defend against attacks from the Internet.</span></span> <span data-ttu-id="33fd8-201">Bu, içerir, ancak bunlarla sınırlı uygun bir zaman aşımı, istek boyutu sınırları ve eş zamanlı bağlantı sınırları değildir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-201">This includes, but isn't limited to, appropriate timeouts, request size limits, and concurrent connection limits.</span></span>

::: moniker-end

<span data-ttu-id="33fd8-202">Kestrel'i Yapılandırma Kılavuzu ve ne zaman bir ters proxy yapılandırma Kestrel kullanılacağı hakkında bilgi için bkz. <xref:fundamentals/servers/kestrel>.</span><span class="sxs-lookup"><span data-stu-id="33fd8-202">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

### <a name="nginx-with-kestrel"></a><span data-ttu-id="33fd8-203">Ngınx Kestrel ile</span><span class="sxs-lookup"><span data-stu-id="33fd8-203">Nginx with Kestrel</span></span>

<span data-ttu-id="33fd8-204">Ngınx Linux üzerinde bir ters proxy sunucusu olarak Kestrel için nasıl kullanılacağı hakkında daha fazla bilgi için bkz: <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="33fd8-204">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="33fd8-205">Kestrel'i Apache</span><span class="sxs-lookup"><span data-stu-id="33fd8-205">Apache with Kestrel</span></span>

<span data-ttu-id="33fd8-206">Apache Linux üzerinde bir ters proxy sunucusu olarak Kestrel için nasıl kullanılacağı hakkında daha fazla bilgi için bkz: <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="33fd8-206">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="iis-http-server"></a><span data-ttu-id="33fd8-207">IIS HTTP sunucusu</span><span class="sxs-lookup"><span data-stu-id="33fd8-207">IIS HTTP Server</span></span>

<span data-ttu-id="33fd8-208">IIS HTTP sunucusu bir [işlem sunucusu](#in-process-hosting-model) IIS ve işlem içi dağıtımlar için gerekli.</span><span class="sxs-lookup"><span data-stu-id="33fd8-208">IIS HTTP Server is an [in-process server](#in-process-hosting-model) for IIS and required for in-process deployments.</span></span> <span data-ttu-id="33fd8-209">[ASP.NET Core Modülü](xref:host-and-deploy/aspnet-core-module) IIS ve IIS HTTP sunucusu arasındaki yerel IIS isteklerini işler.</span><span class="sxs-lookup"><span data-stu-id="33fd8-209">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) handles native IIS requests between IIS and IIS HTTP Server.</span></span> <span data-ttu-id="33fd8-210">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="33fd8-210">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="33fd8-211">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="33fd8-211">HTTP.sys</span></span>

<span data-ttu-id="33fd8-212">Windows üzerinde çalışan ASP.NET Core uygulamaları, HTTP.sys Kestrel alternatif olur.</span><span class="sxs-lookup"><span data-stu-id="33fd8-212">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="33fd8-213">Kestrel'i genellikle en iyi performans için önerilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-213">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="33fd8-214">HTTP.sys burada uygulama Internet erişimine açıktır ve gerekli özellikleri HTTP.sys ancak değil Kestrel tarafından desteklenen senaryolarda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-214">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="33fd8-215">Daha fazla bilgi için bkz. <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="33fd8-215">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys doğrudan Internet ile iletişim kurar.](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="33fd8-217">HTTP.sys, yalnızca iç ağa sunulan uygulamalar için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-217">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys iç ağa ile doğrudan iletişim kurar.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="33fd8-219">HTTP.sys yapılandırma yönergeleri için bkz <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="33fd8-219">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="33fd8-220">ASP.NET Core sunucu altyapısı</span><span class="sxs-lookup"><span data-stu-id="33fd8-220">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="33fd8-221">[IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) bulunan `Startup.Configure` yöntemi kullanıma sunan [ServerFeatures](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.serverfeatures) türünün özelliği [IFeatureCollection](/dotnet/api/microsoft.aspnetcore.http.features.ifeaturecollection).</span><span class="sxs-lookup"><span data-stu-id="33fd8-221">The [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) available in the `Startup.Configure` method exposes the [ServerFeatures](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.serverfeatures) property of type [IFeatureCollection](/dotnet/api/microsoft.aspnetcore.http.features.ifeaturecollection).</span></span> <span data-ttu-id="33fd8-222">Kestrel ve HTTP.sys yalnızca tek bir özelliği her kullanıma [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature), ancak farklı bir sunucu uygulamaları, ek işlevler sunarsınız.</span><span class="sxs-lookup"><span data-stu-id="33fd8-222">Kestrel and HTTP.sys only expose a single feature each, [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature), but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="33fd8-223">`IServerAddressesFeature` Sunucu Uygulama çalışma zamanında bağlı bağlantı noktasını bulmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-223">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="33fd8-224">Özel sunucuları</span><span class="sxs-lookup"><span data-stu-id="33fd8-224">Custom servers</span></span>

<span data-ttu-id="33fd8-225">Özel sunucu uygulaması, yerleşik sunucuları uygulamanın gereksinimleri karşılamıyorsanız oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-225">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="33fd8-226">[.NET (OWIN) kılavuzu için açık Web arabirimi](xref:fundamentals/owin) nasıl yazılacağını gösteren bir [Nowin](https://github.com/Bobris/Nowin)-tabanlı [IServer](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) uygulaması.</span><span class="sxs-lookup"><span data-stu-id="33fd8-226">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based [IServer](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) implementation.</span></span> <span data-ttu-id="33fd8-227">Uygulamanın kullandığı özellik arabirimler uygulama, en az olsa gerektirir. yalnızca [IHttpRequestFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttprequestfeature) ve [IHttpResponseFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpresponsefeature) desteklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-227">Only the feature interfaces that the app uses require implementation, though at a minimum [IHttpRequestFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttprequestfeature) and [IHttpResponseFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpresponsefeature) must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="33fd8-228">Sunucu başlangıç</span><span class="sxs-lookup"><span data-stu-id="33fd8-228">Server startup</span></span>

<span data-ttu-id="33fd8-229">Sunucu tümleşik geliştirme ortamı (IDE) veya düzenleyicide uygulama başlatıldığında başlatılır:</span><span class="sxs-lookup"><span data-stu-id="33fd8-229">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="33fd8-230">[Visual Studio](https://www.visualstudio.com/vs/) &ndash; başlatma profilleri, uygulama ve sunucu ile başlatmak için kullanılabilir [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Modülü](xref:host-and-deploy/aspnet-core-module) veya Konsolu.</span><span class="sxs-lookup"><span data-stu-id="33fd8-230">[Visual Studio](https://www.visualstudio.com/vs/) &ndash; Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="33fd8-231">[Visual Studio Code](https://code.visualstudio.com/) &ndash; uygulama ve sunucu başlatılır [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), CoreCLR hata ayıklayıcı etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-231">[Visual Studio Code](https://code.visualstudio.com/) &ndash; The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="33fd8-232">[Mac için Visual Studio](https://www.visualstudio.com/vs/mac/) &ndash; uygulama ve sunucu başlatılır [Mono modu geçici hata ayıklayıcı](http://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="33fd8-232">[Visual Studio for Mac](https://www.visualstudio.com/vs/mac/) &ndash; The app and server are started by the [Mono Soft-Mode Debugger](http://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="33fd8-233">Proje klasöründeki bir komut isteminden uygulamayı başlatırken [çalıştırma dotnet](/dotnet/core/tools/dotnet-run) uygulama ve sunucu (Kestrel ve yalnızca HTTP.sys) başlatır.</span><span class="sxs-lookup"><span data-stu-id="33fd8-233">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="33fd8-234">Yapılandırması tarafından belirtilen `-c|--configuration` ayarlandığından seçeneği `Debug` (varsayılan) veya `Release`.</span><span class="sxs-lookup"><span data-stu-id="33fd8-234">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span> <span data-ttu-id="33fd8-235">Başlatma profili varsa bir *launchSettings.json* dosya, kullanın `--launch-profile <NAME>` başlatma profili ayarlamak için seçeneği (örneğin, `Development` veya `Production`).</span><span class="sxs-lookup"><span data-stu-id="33fd8-235">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile <NAME>` option to set the launch profile (for example, `Development` or `Production`).</span></span> <span data-ttu-id="33fd8-236">Daha fazla bilgi için [çalıştırma dotnet](/dotnet/core/tools/dotnet-run) ve [.NET Core dağıtımı paketleme](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="33fd8-236">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="33fd8-237">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="33fd8-237">HTTP/2 support</span></span>

<span data-ttu-id="33fd8-238">[HTTP/2](https://httpwg.org/specs/rfc7540.html) ile ASP.NET Core aşağıdaki dağıtım senaryolarında desteklenir:</span><span class="sxs-lookup"><span data-stu-id="33fd8-238">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="33fd8-239">Kestrel</span><span class="sxs-lookup"><span data-stu-id="33fd8-239">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="33fd8-240">İşletim sistemi</span><span class="sxs-lookup"><span data-stu-id="33fd8-240">Operating system</span></span>
    * <span data-ttu-id="33fd8-241">Windows Server 2016/Windows 10 veya üzeri&dagger;</span><span class="sxs-lookup"><span data-stu-id="33fd8-241">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="33fd8-242">Linux OpenSSL 1.0.2 veya daha sonra (örneğin, Ubuntu 16.04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="33fd8-242">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="33fd8-243">HTTP/2 macos'ta gelecek sürümlerde desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-243">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="33fd8-244">Hedef çerçeve: .NET Core 2.2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="33fd8-244">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="33fd8-245">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="33fd8-245">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="33fd8-246">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="33fd8-246">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="33fd8-247">Hedef çerçeve: HTTP.sys dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-247">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="33fd8-248">IIS (işlem içi)</span><span class="sxs-lookup"><span data-stu-id="33fd8-248">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="33fd8-249">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="33fd8-249">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="33fd8-250">Hedef çerçeve: .NET Core 2.2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="33fd8-250">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="33fd8-251">IIS (giden işlem)</span><span class="sxs-lookup"><span data-stu-id="33fd8-251">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="33fd8-252">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="33fd8-252">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="33fd8-253">HTTP/2 genel kullanıma yönelik uç sunucu bağlantılarını kullanın, ancak HTTP/1.1 Kestrel ters proxy bağlantı kullanır.</span><span class="sxs-lookup"><span data-stu-id="33fd8-253">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="33fd8-254">Hedef çerçeve: IIS işlem dışı dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-254">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="33fd8-255">&dagger;Kestrel'i HTTP/2 Windows Server 2012 R2 ve Windows 8.1 için destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="33fd8-255">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="33fd8-256">Bu işletim sistemlerinde desteklenen TLS şifre paketlerinin listesini sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="33fd8-256">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="33fd8-257">Bir Eliptik Eğri Dijital imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika, TLS bağlantıları güvenli hale getirmek için gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-257">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="33fd8-258">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="33fd8-258">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="33fd8-259">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="33fd8-259">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="33fd8-260">Hedef çerçeve: HTTP.sys dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-260">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="33fd8-261">IIS (giden işlem)</span><span class="sxs-lookup"><span data-stu-id="33fd8-261">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="33fd8-262">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="33fd8-262">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="33fd8-263">HTTP/2 genel kullanıma yönelik uç sunucu bağlantılarını kullanın, ancak HTTP/1.1 Kestrel ters proxy bağlantı kullanır.</span><span class="sxs-lookup"><span data-stu-id="33fd8-263">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="33fd8-264">Hedef çerçeve: IIS işlem dışı dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="33fd8-264">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="33fd8-265">Bir HTTP/2 bağlantı kullanmalısınız [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ve TLS 1.2 veya sonraki bir sürümü.</span><span class="sxs-lookup"><span data-stu-id="33fd8-265">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="33fd8-266">Daha fazla bilgi için sunucu dağıtım senaryolarınız için ilgili konulara bakın.</span><span class="sxs-lookup"><span data-stu-id="33fd8-266">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="33fd8-267">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="33fd8-267">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
