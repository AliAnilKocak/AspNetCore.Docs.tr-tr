---
title: "Web server ASP.NET Core uygulamalarında"
author: tdykstra
description: "Web sunucuları Kestrel ve WebListener için ASP.NET Core tanıtır. Birini konusunda rehberlik sağlar ve ne zaman bir ters proxy sunucusu ile kullanılır."
ms.author: tdykstra
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/index
ms.openlocfilehash: 807e60e61d4ce4d5755987cffe65d130c9bbbd42
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/19/2018
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="06da8-104">Web server ASP.NET Core uygulamalarında</span><span class="sxs-lookup"><span data-stu-id="06da8-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="06da8-105">Tarafından [zel Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), ve [Chris fillerin](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="06da8-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="06da8-106">Bir ASP.NET Core uygulama işlem içi HTTP sunucusu uygulamasını ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="06da8-106">An ASP.NET Core application runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="06da8-107">HTTP istekleri ve bunları uygulamasına kümesi olarak ortaya çıkarır sunucusu uygulaması dinler [istek özellikleri](https://docs.microsoft.com/aspnet/core/fundamentals/request-features) içine oluşan bir `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="06da8-107">The server implementation listens for HTTP requests and surfaces them to the application as sets of [request features](https://docs.microsoft.com/aspnet/core/fundamentals/request-features) composed into an `HttpContext`.</span></span>

<span data-ttu-id="06da8-108">ASP.NET Core iki sunucu uygulamaları gelir:</span><span class="sxs-lookup"><span data-stu-id="06da8-108">ASP.NET Core ships two server implementations:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="06da8-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="06da8-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

* <span data-ttu-id="06da8-110">[Kestrel](kestrel.md) platformlar arası HTTP sunucusu dayanır [libuv](https://github.com/libuv/libuv), platformlar arası zaman uyumsuz g/ç kitaplığı.</span><span class="sxs-lookup"><span data-stu-id="06da8-110">[Kestrel](kestrel.md) is a cross-platform HTTP server based on [libuv](https://github.com/libuv/libuv), a cross-platform asynchronous I/O library.</span></span>

* <span data-ttu-id="06da8-111">[HTTP.sys](httpsys.md) yalnızca Windows HTTP sunucu dayanır [Http.Sys çekirdek sürücüsü](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span><span class="sxs-lookup"><span data-stu-id="06da8-111">[HTTP.sys](httpsys.md) is a Windows-only HTTP server based on the [Http.Sys kernel driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="06da8-112">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="06da8-112">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

* <span data-ttu-id="06da8-113">[Kestrel](kestrel.md) platformlar arası HTTP sunucusu dayanır [libuv](https://github.com/libuv/libuv), platformlar arası zaman uyumsuz g/ç kitaplığı.</span><span class="sxs-lookup"><span data-stu-id="06da8-113">[Kestrel](kestrel.md) is a cross-platform HTTP server based on [libuv](https://github.com/libuv/libuv), a cross-platform asynchronous I/O library.</span></span>

* <span data-ttu-id="06da8-114">[WebListener](weblistener.md) yalnızca Windows HTTP sunucu dayanır [Http.Sys çekirdek sürücüsü](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span><span class="sxs-lookup"><span data-stu-id="06da8-114">[WebListener](weblistener.md) is a Windows-only HTTP server based on the [Http.Sys kernel driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span>

---

## <a name="kestrel"></a><span data-ttu-id="06da8-115">kestrel</span><span class="sxs-lookup"><span data-stu-id="06da8-115">Kestrel</span></span>

<span data-ttu-id="06da8-116">Kestrel varsayılan ASP.NET Core yeni proje şablonları olarak dahil edilen web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="06da8-116">Kestrel is the web server that is included by default in ASP.NET Core new-project templates.</span></span> 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="06da8-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="06da8-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="06da8-118">Tek başına veya birlikte Kestrel kullanabileceğiniz bir *ters proxy sunucusu*, IIS, Nginx ya da Apache gibi.</span><span class="sxs-lookup"><span data-stu-id="06da8-118">You can use Kestrel by itself or with a *reverse proxy server*, such as IIS, Nginx, or Apache.</span></span> <span data-ttu-id="06da8-119">Ters proxy sunucusu Internet'ten HTTP isteklerini alır ve bunları Kestrel için bazı ön işleme sonra iletir.</span><span class="sxs-lookup"><span data-stu-id="06da8-119">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling.</span></span>

![Kestrel bir ters Ara sunucu olmadan Internet ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

![Kestrel dolaylı olarak IIS, Nginx ya da Apache gibi bir ters proxy sunucu üzerinden Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="06da8-122">Her iki yapılandırma &mdash; ile veya bir ters Ara sunucu olmadan &mdash; Kestrel yalnızca bir iç ağa açılırsa de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="06da8-122">Either configuration &mdash; with or without a reverse proxy server &mdash; can also be used if Kestrel is exposed only to an internal network.</span></span>

<span data-ttu-id="06da8-123">Ne zaman bir ters proxy ile Kestrel kullanılacağı hakkında daha fazla bilgi için bkz: [Kestrel giriş](kestrel.md).</span><span class="sxs-lookup"><span data-stu-id="06da8-123">For information about when to use Kestrel with a reverse proxy, see [Introduction to Kestrel](kestrel.md).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="06da8-124">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="06da8-124">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="06da8-125">Uygulamanızı bir iç ağ yalnızca gelen istekleri kabul ederse, tek başına Kestrel kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="06da8-125">If your application accepts requests only from an internal network, you can use Kestrel by itself.</span></span>

![Kestrel, iç ağınız ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internal.png)

<span data-ttu-id="06da8-127">Uygulamanızı internet kullanıma sunma, IIS, Nginx ya da Apache olarak kullanmalısınız bir *ters proxy sunucu*.</span><span class="sxs-lookup"><span data-stu-id="06da8-127">If you expose your application to the Internet, you must use IIS, Nginx, or Apache as a *reverse proxy server*.</span></span> <span data-ttu-id="06da8-128">Ters proxy sunucusu Internet'ten HTTP isteklerini alır ve bunları Kestrel için bazı ön işleme sonra aşağıdaki çizimde gösterildiği gibi iletir.</span><span class="sxs-lookup"><span data-stu-id="06da8-128">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling, as shown in the following diagram.</span></span>

![Kestrel dolaylı olarak IIS, Nginx ya da Apache gibi bir ters proxy sunucu üzerinden Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="06da8-130">Güvenlik (trafiğini Internet'ten gösterilen) kenar dağıtımlar için ters Ara sunucu kullanmak için en önemli nedenidir.</span><span class="sxs-lookup"><span data-stu-id="06da8-130">The most important reason for using a reverse proxy for edge deployments (exposed to traffic from the Internet) is security.</span></span> <span data-ttu-id="06da8-131">Kestrel 1.x sürümleri tamamlayıcı saldırılarına karşı savunma yoktur.</span><span class="sxs-lookup"><span data-stu-id="06da8-131">The 1.x versions of Kestrel don't have a full complement of defenses against attacks.</span></span> <span data-ttu-id="06da8-132">Bu, içerir, ancak uygun için sınırlı zaman aşımları, boyut sınırları ve eşzamanlı bağlantı sınırlarını değil.</span><span class="sxs-lookup"><span data-stu-id="06da8-132">This includes, but isn't limited to, appropriate timeouts, size limits, and concurrent connection limits.</span></span>

<span data-ttu-id="06da8-133">Ne zaman bir ters proxy ile Kestrel kullanılacağı hakkında daha fazla bilgi için bkz: [Kestrel giriş](kestrel.md).</span><span class="sxs-lookup"><span data-stu-id="06da8-133">For information about when to use Kestrel with a reverse proxy, see [Introduction to Kestrel](kestrel.md).</span></span>

---

<span data-ttu-id="06da8-134">IIS, Nginx ya da Apache Kestrel olmadan kullanamazsınız veya [özel sunucu uygulaması](#custom-servers).</span><span class="sxs-lookup"><span data-stu-id="06da8-134">You can't use IIS, Nginx, or Apache without Kestrel or a [custom server implementation](#custom-servers).</span></span> <span data-ttu-id="06da8-135">ASP.NET Core, böylece platformlar genelinde tutarlı bir şekilde davranabilir kendi işleminde çalışmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="06da8-135">ASP.NET Core was designed to run in its own process so that it can behave consistently across platforms.</span></span> <span data-ttu-id="06da8-136">IIS, Nginx ve Apache kendi başlatma işlemi ve ortam dikte; bunları kullanmak için doğrudan ASP.NET Core her biri gereksinimlerine uyarlamak gerekir.</span><span class="sxs-lookup"><span data-stu-id="06da8-136">IIS, Nginx, and Apache dictate their own startup process and environment; to use them directly, ASP.NET Core would have to adapt to the needs of each one.</span></span> <span data-ttu-id="06da8-137">Bir web sunucusu uygulaması Kestrel gibi kullanarak ASP.NET Core üzerinde denetim başlatma işlemi ve ortam sağlar.</span><span class="sxs-lookup"><span data-stu-id="06da8-137">Using a web server implementation such as Kestrel gives ASP.NET Core control over the startup process and environment.</span></span> <span data-ttu-id="06da8-138">Bu nedenle ASP.NET Core IIS, Nginx ya da Apache uyarlamak çalışırken yerine yalnızca bu web sunucularının Kestrel proxy istekleri için ayarlarsınız.</span><span class="sxs-lookup"><span data-stu-id="06da8-138">So rather than trying to adapt ASP.NET Core to IIS, Nginx, or Apache, you just set up those web servers to proxy requests to Kestrel.</span></span> <span data-ttu-id="06da8-139">Bu düzenlemenin sağlar, `Program.Main` ve `Startup` temelde dağıttığınız nerede olursa olsun aynı olması için sınıflar.</span><span class="sxs-lookup"><span data-stu-id="06da8-139">This arrangement allows your `Program.Main` and `Startup` classes to be essentially the same no matter where you deploy.</span></span>

### <a name="iis-with-kestrel"></a><span data-ttu-id="06da8-140">IIS Kestrel ile</span><span class="sxs-lookup"><span data-stu-id="06da8-140">IIS with Kestrel</span></span>

<span data-ttu-id="06da8-141">ASP.NET Core için ters proxy IIS veya IIS Express kullandığınızda, ASP.NET Core uygulama ayrı bir işlemde için IIS çalışan işlemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="06da8-141">When you use IIS or IIS Express as a reverse proxy for ASP.NET Core, the ASP.NET Core application runs in a process separate from the IIS worker process.</span></span> <span data-ttu-id="06da8-142">Ters proxy ilişki koordine etmek için özel bir IIS modül IIS işleminde çalışır.</span><span class="sxs-lookup"><span data-stu-id="06da8-142">In the IIS process, a special IIS module runs to coordinate the reverse proxy relationship.</span></span>  <span data-ttu-id="06da8-143">Bu *ASP.NET Core Modülü*.</span><span class="sxs-lookup"><span data-stu-id="06da8-143">This is the *ASP.NET Core Module*.</span></span> <span data-ttu-id="06da8-144">Birincil ASP.NET Core modülünün ASP.NET Core uygulamayı başlatmak, onu kilitlendiğinde yeniden başlatın ve HTTP trafiği iletmek için işlevlerdir.</span><span class="sxs-lookup"><span data-stu-id="06da8-144">The primary functions of the ASP.NET Core Module are to start the ASP.NET Core application, restart it when it crashes, and forward HTTP traffic to it.</span></span> <span data-ttu-id="06da8-145">Daha fazla bilgi için bkz: [ASP.NET Core Modülü](aspnet-core-module.md).</span><span class="sxs-lookup"><span data-stu-id="06da8-145">For more information, see [ASP.NET Core Module](aspnet-core-module.md).</span></span> 

### <a name="nginx-with-kestrel"></a><span data-ttu-id="06da8-146">Nginx Kestrel ile</span><span class="sxs-lookup"><span data-stu-id="06da8-146">Nginx with Kestrel</span></span>

<span data-ttu-id="06da8-147">Nginx Linux'ta bir ters proxy sunucusu olarak Kestrel için nasıl kullanılacağı hakkında daha fazla bilgi için bkz: [Nginx ile Linux konakta](xref:host-and-deploy/linux-nginx).</span><span class="sxs-lookup"><span data-stu-id="06da8-147">For information about how to use Nginx on Linux as a reverse proxy server for Kestrel, see [Host on Linux with Nginx](xref:host-and-deploy/linux-nginx).</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="06da8-148">Kestrel Apache</span><span class="sxs-lookup"><span data-stu-id="06da8-148">Apache with Kestrel</span></span>

<span data-ttu-id="06da8-149">Apache Linux'ta bir ters proxy sunucusu olarak Kestrel için nasıl kullanılacağı hakkında daha fazla bilgi için bkz: [Linux Apache ile konakta](xref:host-and-deploy/linux-apache).</span><span class="sxs-lookup"><span data-stu-id="06da8-149">For information about how to use Apache on Linux as a reverse proxy server for Kestrel, see [Host on Linux with Apache](xref:host-and-deploy/linux-apache).</span></span>

## <a name="httpsys"></a><span data-ttu-id="06da8-150">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="06da8-150">HTTP.sys</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="06da8-151">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="06da8-151">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="06da8-152">ASP.NET Core uygulamanızı Windows'ta çalıştırma, HTTP.sys alternatif Kestrel olur.</span><span class="sxs-lookup"><span data-stu-id="06da8-152">If you run your ASP.NET Core app on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="06da8-153">HTTP.sys burada uygulamanızı internet kullanıma ve Kestrel desteklemiyor HTTP.sys özelliklerine ihtiyaç senaryoları için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="06da8-153">You can use HTTP.sys for scenarios where you expose your app to the Internet and you need HTTP.sys features that Kestrel doesn't support.</span></span> 

![HTTP.sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="06da8-155">HTTP.sys, yalnızca bir iç ağ için sunulan uygulamaları için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="06da8-155">HTTP.sys can also be used for applications that are exposed only to an internal network.</span></span> 

![HTTP.sys, iç ağınız ile doğrudan iletişim kurar](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="06da8-157">İç ağ senaryoları için Kestrel genellikle en iyi performans için önerilir; Ancak, bazı senaryolarda, yalnızca HTTP.sys sağlayan bir özellik kullanmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="06da8-157">For internal network scenarios, Kestrel is generally recommended for best performance; but in some scenarios, you might want to use a feature that only HTTP.sys offers.</span></span> <span data-ttu-id="06da8-158">HTTP.sys özellikler hakkında daha fazla bilgi için bkz: [HTTP.sys](httpsys.md).</span><span class="sxs-lookup"><span data-stu-id="06da8-158">For information about HTTP.sys features, see [HTTP.sys](httpsys.md).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="06da8-159">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="06da8-159">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="06da8-160">HTTP.sys ASP.NET Core WebListener adlı 1.x.</span><span class="sxs-lookup"><span data-stu-id="06da8-160">HTTP.sys is named WebListener in ASP.NET Core 1.x.</span></span> <span data-ttu-id="06da8-161">Windows, WebListener uygulama için kullanabileceğiniz bir alternatiftir, ASP.NET Core çalıştırırsanız Internet ancak uygulamanıza kullanıma sunmak istediğiniz senaryolar IIS kullanamazsınız.</span><span class="sxs-lookup"><span data-stu-id="06da8-161">If you run your ASP.NET Core app on Windows, WebListener is an alternative that you can use for scenarios where you want to expose your app to the Internet but you can't use IIS.</span></span>

![Weblistener doğrudan Internet ile iletişim kurar](weblistener/_static/weblistener-to-internet.png)

<span data-ttu-id="06da8-163">Kestrel desteklemiyor WebListener özelliklerine gereksinim duyarsanız WebListener de Kestrel yerine yalnızca bir iç ağa, sunulan uygulamaları için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="06da8-163">WebListener can also be used in place of Kestrel for applications that are exposed only to an internal network, if you need WebListener features that Kestrel doesn't support.</span></span> 

![Weblistener, iç ağınız ile doğrudan iletişim kurar](weblistener/_static/weblistener-to-internal.png)

<span data-ttu-id="06da8-165">İç ağ senaryoları için Kestrel genellikle en iyi performans için önerilir, ancak bazı senaryolarda yalnızca WebListener sağlayan bir özellik kullanmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="06da8-165">For internal network scenarios, Kestrel is generally recommended for best performance, but in some scenarios you might want to use a feature that only WebListener offers.</span></span> <span data-ttu-id="06da8-166">WebListener özellikleri hakkında daha fazla bilgi için bkz: [WebListener](weblistener.md).</span><span class="sxs-lookup"><span data-stu-id="06da8-166">For information about WebListener features, see [WebListener](weblistener.md).</span></span>

---

## <a name="notes-about-aspnet-core-server-infrastructure"></a><span data-ttu-id="06da8-167">ASP.NET Core sunucu altyapısı ile ilgili notlar</span><span class="sxs-lookup"><span data-stu-id="06da8-167">Notes about ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="06da8-168">[ `IApplicationBuilder` ](/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder) Bulunan `Startup` sınıfı `Configure` yöntemi çıkarır `ServerFeatures` türündeki özelliği [ `IFeatureCollection` ](/aspnet/core/api/microsoft.aspnetcore.http.features.ifeaturecollection).</span><span class="sxs-lookup"><span data-stu-id="06da8-168">The [`IApplicationBuilder`](/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder) available in the `Startup` class `Configure` method exposes the `ServerFeatures` property of type [`IFeatureCollection`](/aspnet/core/api/microsoft.aspnetcore.http.features.ifeaturecollection).</span></span> <span data-ttu-id="06da8-169">Kestrel ve WebListener kullanıma yalnızca tek bir özellik olan [ `IServerAddressesFeature` ](/aspnet/core/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature), ancak farklı sunucu uygulamaları ek işlevsellik kullanıma.</span><span class="sxs-lookup"><span data-stu-id="06da8-169">Kestrel and WebListener both expose only a single feature, [`IServerAddressesFeature`](/aspnet/core/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature), but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="06da8-170">`IServerAddressesFeature`Sunucu uygulaması çalışma zamanında bağlı hangi bağlantı noktasının öğrenmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="06da8-170">`IServerAddressesFeature` can be used to find out which port the server implementation has bound to at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="06da8-171">Özel sunucuları</span><span class="sxs-lookup"><span data-stu-id="06da8-171">Custom servers</span></span>

<span data-ttu-id="06da8-172">Yerleşik sunucuları ihtiyaçlarınızı karşılamıyorsa, özel sunucu uygulaması oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="06da8-172">If the built-in servers don't meet your needs, you can create a custom server implementation.</span></span> <span data-ttu-id="06da8-173">[.NET (OWIN) kılavuzu için açık Web arabirimi](../owin.md) nasıl yazılacağını göstermektedir bir [Nowin](https://github.com/Bobris/Nowin)-tabanlı [IServer](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) uygulaması.</span><span class="sxs-lookup"><span data-stu-id="06da8-173">The [Open Web Interface for .NET (OWIN) guide](../owin.md) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based [IServer](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) implementation.</span></span> <span data-ttu-id="06da8-174">En azından desteklemeniz gereken ancak yalnızca uygulamanız gereken, özellik arabirimleri uygulamak ücretsiz [IHttpRequestFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttprequestfeature) ve [IHttpResponseFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttpresponsefeature).</span><span class="sxs-lookup"><span data-stu-id="06da8-174">You're free to implement just the feature interfaces your application needs, though at a minimum you must support [IHttpRequestFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttprequestfeature) and [IHttpResponseFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttpresponsefeature).</span></span>

## <a name="next-steps"></a><span data-ttu-id="06da8-175">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="06da8-175">Next steps</span></span>

<span data-ttu-id="06da8-176">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="06da8-176">For more information, see the following resources:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="06da8-177">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="06da8-177">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

- [<span data-ttu-id="06da8-178">Kestrel</span><span class="sxs-lookup"><span data-stu-id="06da8-178">Kestrel</span></span>](kestrel.md)
- [<span data-ttu-id="06da8-179">IIS ile kestrel</span><span class="sxs-lookup"><span data-stu-id="06da8-179">Kestrel with IIS</span></span>](aspnet-core-module.md)
- [<span data-ttu-id="06da8-180">Nginx ile Linux’ta barındırma</span><span class="sxs-lookup"><span data-stu-id="06da8-180">Host on Linux with Nginx</span></span>](xref:host-and-deploy/linux-nginx)
- [<span data-ttu-id="06da8-181">Apache ile Linux’ta barındırma</span><span class="sxs-lookup"><span data-stu-id="06da8-181">Host on Linux with Apache</span></span>](xref:host-and-deploy/linux-apache)
- [<span data-ttu-id="06da8-182">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="06da8-182">HTTP.sys</span></span>](httpsys.md)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="06da8-183">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="06da8-183">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

- [<span data-ttu-id="06da8-184">Kestrel</span><span class="sxs-lookup"><span data-stu-id="06da8-184">Kestrel</span></span>](kestrel.md)
- [<span data-ttu-id="06da8-185">IIS ile kestrel</span><span class="sxs-lookup"><span data-stu-id="06da8-185">Kestrel with IIS</span></span>](aspnet-core-module.md)
- [<span data-ttu-id="06da8-186">Nginx ile Linux’ta barındırma</span><span class="sxs-lookup"><span data-stu-id="06da8-186">Host on Linux with Nginx</span></span>](xref:host-and-deploy/linux-nginx)
- [<span data-ttu-id="06da8-187">Apache ile Linux’ta barındırma</span><span class="sxs-lookup"><span data-stu-id="06da8-187">Host on Linux with Apache</span></span>](xref:host-and-deploy/linux-apache)
- [<span data-ttu-id="06da8-188">WebListener</span><span class="sxs-lookup"><span data-stu-id="06da8-188">WebListener</span></span>](weblistener.md)

---
