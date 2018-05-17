---
title: ASP.NET Core kestrel web sunucusu uygulaması
author: rick-anderson
description: Kestrel, ASP.NET Core için platformlar arası web sunucusu hakkında bilgi edinin.
manager: wpickett
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/02/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/servers/kestrel
ms.openlocfilehash: a1162da01fad67f3e8ccb1e70bd646b39c38997f
ms.sourcegitcommit: a19261eb82b948af6e4a1664fcfb8dabb16150e3
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/14/2018
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="ac58f-103">ASP.NET Core kestrel web sunucusu uygulaması</span><span class="sxs-lookup"><span data-stu-id="ac58f-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="ac58f-104">Tarafından [zel Dykstra](https://github.com/tdykstra), [Chris fillerin](https://github.com/Tratcher), ve [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="ac58f-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

<span data-ttu-id="ac58f-105">Kestrel olan bir platformlar arası [web sunucusu için ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="ac58f-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="ac58f-106">Kestrel varsayılan ASP.NET Core proje şablonları olarak dahil edilen web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="ac58f-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="ac58f-107">Kestrel aşağıdaki özellikleri destekler:</span><span class="sxs-lookup"><span data-stu-id="ac58f-107">Kestrel supports the following features:</span></span>

* <span data-ttu-id="ac58f-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="ac58f-108">HTTPS</span></span>
* <span data-ttu-id="ac58f-109">Etkinleştirmek için kullanılan opak yükseltme [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="ac58f-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="ac58f-110">Yüksek performanslı Nginx arkasında UNIX yuvaları</span><span class="sxs-lookup"><span data-stu-id="ac58f-110">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="ac58f-111">Kestrel tüm platformlar ve .NET Core destekleyen sürümler desteklenir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-111">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="ac58f-112">[Görüntülemek veya karşıdan örnek kod](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac58f-112">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="ac58f-113">Ters proxy ile Kestrel kullanma zamanı</span><span class="sxs-lookup"><span data-stu-id="ac58f-113">When to use Kestrel with a reverse proxy</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="ac58f-114">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="ac58f-114">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="ac58f-115">Tek başına veya birlikte Kestrel kullanabileceğiniz bir *ters proxy sunucusu*, IIS, Nginx ya da Apache gibi.</span><span class="sxs-lookup"><span data-stu-id="ac58f-115">You can use Kestrel by itself or with a *reverse proxy server*, such as IIS, Nginx, or Apache.</span></span> <span data-ttu-id="ac58f-116">Ters proxy sunucusu Internet'ten HTTP isteklerini alır ve bunları Kestrel için bazı ön işleme sonra iletir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-116">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling.</span></span>

![Kestrel bir ters Ara sunucu olmadan Internet ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

![Kestrel dolaylı olarak IIS, Nginx ya da Apache gibi bir ters proxy sunucu üzerinden Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ac58f-119">Kestrel yalnızca bir iç ağ eline sürece Kestrel ters proxy sunucusu ile kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="ac58f-119">We recommend using Kestrel with a reverse proxy server unless Kestrel is only exposed to an internal network.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="ac58f-120">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="ac58f-120">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="ac58f-121">Bir uygulamayı yalnızca bir iç ağ gelen istekleri kabul ederse, Kestrel doğrudan uygulamanın sunucusu olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-121">If an app accepts requests only from an internal network, Kestrel can be used directly as the app's server.</span></span>

![Kestrel, iç ağınız ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internal.png)

<span data-ttu-id="ac58f-123">Uygulamanızı internet kullanıma sunma, IIS, Nginx ya da Apache olarak kullanırsanız bir *ters proxy sunucu*.</span><span class="sxs-lookup"><span data-stu-id="ac58f-123">If you expose your app to the Internet, use IIS, Nginx, or Apache as a *reverse proxy server*.</span></span> <span data-ttu-id="ac58f-124">Ters proxy sunucusu Internet'ten HTTP isteklerini alır ve bunları Kestrel için bazı ön işleme sonra iletir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-124">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling.</span></span>

![Kestrel dolaylı olarak IIS, Nginx ya da Apache gibi bir ters proxy sunucu üzerinden Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ac58f-126">Güvenlik nedenleriyle (trafiğini Internet'ten gösterilen) kenar dağıtımlar için ters Ara sunucu gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-126">A reverse proxy is required for edge deployments (exposed to traffic from the Internet) for security reasons.</span></span> <span data-ttu-id="ac58f-127">Kestrel 1.x sürümleri tamamlayıcı uygun zaman aşımları, boyut sınırları ve eş zamanlı bağlantı sınırlarını gibi saldırılarına karşı savunma yoktur.</span><span class="sxs-lookup"><span data-stu-id="ac58f-127">The 1.x versions of Kestrel don't have a full complement of defenses against attacks, such as appropriate timeouts, size limits, and concurrent connection limits.</span></span>

---

<span data-ttu-id="ac58f-128">Aynı IP adresini ve bağlantı noktası tek bir sunucu üzerinde çalışan paylaşan birden çok uygulama olduğunda bir ters proxy senaryosu bulunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-128">A reverse proxy scenario exists when there are multiple apps that share the same IP and port running on a single server.</span></span> <span data-ttu-id="ac58f-129">Aynı IP adresini ve birden çok işlemler arasında bağlantı noktası paylaşımı Kestrel desteklemediğinden kestrel bu senaryo desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="ac58f-129">Kestrel doesn't support this scenario because Kestrel doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="ac58f-130">Kestrel Kestrel bir bağlantı noktasında dinleyecek şekilde yapılandırıldığında, bu bağlantı noktası isteklerini ana bilgisayar üstbilgisi bağımsız olarak tüm trafiği işler.</span><span class="sxs-lookup"><span data-stu-id="ac58f-130">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' host header.</span></span> <span data-ttu-id="ac58f-131">Bağlantı noktalarını paylaşabilirsiniz ters bir proxy Kestrel, benzersiz bir IP ve bağlantı noktası isteklerini iletmek için özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-131">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="ac58f-132">Ters proxy sunucusu gerekli olmasa bile bir ters proxy sunucusu kullanmak iyi bir seçimdir olabilir:</span><span class="sxs-lookup"><span data-stu-id="ac58f-132">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice:</span></span>

* <span data-ttu-id="ac58f-133">Barındırdığı uygulamaları sunulan genel yüzey alanını sınırlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ac58f-133">It can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="ac58f-134">Ek bir yapılandırma ve koruma katmanı sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac58f-134">It provides an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="ac58f-135">Mevcut altyapısına daha iyi tümleştirme.</span><span class="sxs-lookup"><span data-stu-id="ac58f-135">It might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="ac58f-136">Yük Dengeleme ve SSL yapılandırmasını basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-136">It simplifies load balancing and SSL configuration.</span></span> <span data-ttu-id="ac58f-137">Yalnızca ters proxy sunucuyu bir SSL sertifikası gerektirir ve bu sunucu uygulama sunucularınızda düz HTTP kullanarak iç ağ ile iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-137">Only the reverse proxy server requires an SSL certificate, and that server can communicate with your app servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="ac58f-138">Etkin bir ters proxy filtreleme ana bilgisayarla kullanılmıyorsa [ana bilgisayar filtre](#host-filtering) etkinleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-138">If not using a reverse proxy with host filtering enabled, [host filtering](#host-filtering) must be enabled.</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="ac58f-139">ASP.NET Core uygulamaları Kestrel kullanma</span><span class="sxs-lookup"><span data-stu-id="ac58f-139">How to use Kestrel in ASP.NET Core apps</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="ac58f-140">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="ac58f-140">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

<span data-ttu-id="ac58f-141">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paket dahil [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="ac58f-141">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="ac58f-142">ASP.NET Core proje şablonlarını Kestrel varsayılan olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac58f-142">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="ac58f-143">İçinde *Program.cs*, şablonu kodu çağrılarının [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), çağıran [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) arka planda.</span><span class="sxs-lookup"><span data-stu-id="ac58f-143">In *Program.cs*, the template code calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), which calls [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="ac58f-144">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="ac58f-144">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

<span data-ttu-id="ac58f-145">Yükleme [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="ac58f-145">Install the [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) NuGet package.</span></span>

<span data-ttu-id="ac58f-146">Çağrı [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) genişletme yöntemi [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder?view=aspnetcore-1.1) içinde `Main` herhangi belirtme yöntemi [Kestrel seçenekleri](/dotnet/api/microsoft.aspnetcore.server.kestrel.kestrelserveroptions?view=aspnetcore-1.1) , sonraki bölümde gösterildiği gibi gerekli.</span><span class="sxs-lookup"><span data-stu-id="ac58f-146">Call the [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) extension method on [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder?view=aspnetcore-1.1) in the `Main` method, specifying any [Kestrel options](/dotnet/api/microsoft.aspnetcore.server.kestrel.kestrelserveroptions?view=aspnetcore-1.1) required, as shown in the next section.</span></span>

[!code-csharp[](kestrel/samples/1.x/Program.cs?name=snippet_Main&highlight=13-19)]

---

### <a name="kestrel-options"></a><span data-ttu-id="ac58f-147">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="ac58f-147">Kestrel options</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="ac58f-148">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="ac58f-148">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

<span data-ttu-id="ac58f-149">Kestrel web sunucusu Internet'e yönelik dağıtımlarda özellikle yararlı kısıtlaması yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-149">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span> <span data-ttu-id="ac58f-150">Özelleştirilebilir bir birkaç önemli sınırları:</span><span class="sxs-lookup"><span data-stu-id="ac58f-150">A few important limits that can be customized:</span></span>

* <span data-ttu-id="ac58f-151">En fazla istemci bağlantıları</span><span class="sxs-lookup"><span data-stu-id="ac58f-151">Maximum client connections</span></span>
* <span data-ttu-id="ac58f-152">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="ac58f-152">Maximum request body size</span></span>
* <span data-ttu-id="ac58f-153">En düşük istek gövdesi veri oranı</span><span class="sxs-lookup"><span data-stu-id="ac58f-153">Minimum request body data rate</span></span>

<span data-ttu-id="ac58f-154">Bunlar ve diğer kısıtlamaları ayarlamak [sınırları](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.limits) özelliği [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions) sınıfı.</span><span class="sxs-lookup"><span data-stu-id="ac58f-154">Set these and other constraints on the [Limits](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.limits) property of the [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions) class.</span></span> <span data-ttu-id="ac58f-155">`Limits` Özelliği bir örneğini tutan [KestrelServerLimits](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits) sınıfı.</span><span class="sxs-lookup"><span data-stu-id="ac58f-155">The `Limits` property holds an instance of the [KestrelServerLimits](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits) class.</span></span>

<span data-ttu-id="ac58f-156">**En fazla istemci bağlantıları**</span><span class="sxs-lookup"><span data-stu-id="ac58f-156">**Maximum client connections**</span></span>

[<span data-ttu-id="ac58f-157">MaxConcurrentConnections</span><span class="sxs-lookup"><span data-stu-id="ac58f-157">MaxConcurrentConnections</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxconcurrentconnections)  
[<span data-ttu-id="ac58f-158">MaxConcurrentUpgradedConnections</span><span class="sxs-lookup"><span data-stu-id="ac58f-158">MaxConcurrentUpgradedConnections</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxconcurrentupgradedconnections)

<span data-ttu-id="ac58f-159">Eşzamanlı açık TCP bağlantısı sayısı tüm uygulama aşağıdaki kod ile ayarlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ac58f-159">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="ac58f-160">Başka bir protokol (örneğin, WebSockets istek üzerine) için HTTP veya HTTPS yükseltildi bağlantıları için ayrı bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="ac58f-160">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="ac58f-161">Bir bağlantı yükseltildikten sonra onu karşı sayılan değil `MaxConcurrentConnections` sınırı.</span><span class="sxs-lookup"><span data-stu-id="ac58f-161">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="ac58f-162">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null) olur.</span><span class="sxs-lookup"><span data-stu-id="ac58f-162">The maximum number of connections is unlimited (null) by default.</span></span>

<span data-ttu-id="ac58f-163">**En büyük istek gövdesi boyutu**</span><span class="sxs-lookup"><span data-stu-id="ac58f-163">**Maximum request body size**</span></span>

[<span data-ttu-id="ac58f-164">MaxRequestBodySize</span><span class="sxs-lookup"><span data-stu-id="ac58f-164">MaxRequestBodySize</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize)

<span data-ttu-id="ac58f-165">Varsayılan en büyük istek gövdesi boyutu 30.000.000, hangi 28.6 MB'dir bayttır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-165">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="ac58f-166">Bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım kullanmaktır [RequestSizeLimit](/dotnet/api/microsoft.aspnetcore.mvc.requestsizelimitattribute) bir eylem yönteminin özniteliği:</span><span class="sxs-lookup"><span data-stu-id="ac58f-166">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the [RequestSizeLimit](/dotnet/api/microsoft.aspnetcore.mvc.requestsizelimitattribute) attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ac58f-167">Aşağıda, her istek için uygulama kısıtlama yapılandırmak nasıl oluşturulduğunu gösteren bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="ac58f-167">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="ac58f-168">Belirli bir istek Ara yazılımında ayarı geçersiz kılabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ac58f-168">You can override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="ac58f-169">Uygulama isteği okumak başlatıldıktan sonra bir istekte sınırını yapılandırmak çalışırsanız özel durum oluşur.</span><span class="sxs-lookup"><span data-stu-id="ac58f-169">An exception is thrown if you attempt to configure the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="ac58f-170">Var. bir `IsReadOnly` gösterir özelliği `MaxRequestBodySize` özelliği olan salt okunur durumda olduğu çok geç sınırını yapılandırmak için anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-170">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="ac58f-171">**En düşük istek gövdesi veri oranı**</span><span class="sxs-lookup"><span data-stu-id="ac58f-171">**Minimum request body data rate**</span></span>

[<span data-ttu-id="ac58f-172">MinRequestBodyDataRate</span><span class="sxs-lookup"><span data-stu-id="ac58f-172">MinRequestBodyDataRate</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.minrequestbodydatarate)  
[<span data-ttu-id="ac58f-173">MinResponseDataRate</span><span class="sxs-lookup"><span data-stu-id="ac58f-173">MinResponseDataRate</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.minresponsedatarate)

<span data-ttu-id="ac58f-174">Kestrel veri belirtilen hızını saniye başına baytların geliş saniyede denetler.</span><span class="sxs-lookup"><span data-stu-id="ac58f-174">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="ac58f-175">Hızı en düşerse, bağlantı zaman aşımına. Yetkisiz kullanım süresi Kestrel en düşük kadar gönderme oranını artırmak için istemcinin verir süreyi belirtir; oranı, bu süre boyunca işaretli değil.</span><span class="sxs-lookup"><span data-stu-id="ac58f-175">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="ac58f-176">Yetkisiz kullanım süresi başlangıçta TCP yavaş başlatma nedeniyle yavaş bir hızda veri gönderme bağlantıları bırakarak önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="ac58f-176">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="ac58f-177">Varsayılan en az 5 saniye yetkisiz kullanım süresi ile 240 bayt/saniye hızıdır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-177">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="ac58f-178">Minimum oran yanıt için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-178">A minimum rate also applies to the response.</span></span> <span data-ttu-id="ac58f-179">İstek sınırı ve yanıt sınırı ayarlamak için kod olması dışında aynı olduğundan `RequestBody` veya `Response` özelliği ve arabirim adlarını.</span><span class="sxs-lookup"><span data-stu-id="ac58f-179">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="ac58f-180">En az veri hızları yapılandırmak nasıl oluşturulduğunu gösteren bir örnek şudur *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac58f-180">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/Program.cs?name=snippet_Limits&highlight=6-7)]

<span data-ttu-id="ac58f-181">İstek başına oranları Ara yazılımında yapılandırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ac58f-181">You can configure the rates per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/Startup.cs?name=snippet_Limits&highlight=5-8)]

<span data-ttu-id="ac58f-182">Diğer Kestrel seçenekleri ve sınırları hakkında daha fazla bilgi için bkz:</span><span class="sxs-lookup"><span data-stu-id="ac58f-182">For information about other Kestrel options and limits, see:</span></span>

* [<span data-ttu-id="ac58f-183">KestrelServerOptions</span><span class="sxs-lookup"><span data-stu-id="ac58f-183">KestrelServerOptions</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions)
* [<span data-ttu-id="ac58f-184">KestrelServerLimits</span><span class="sxs-lookup"><span data-stu-id="ac58f-184">KestrelServerLimits</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits)
* [<span data-ttu-id="ac58f-185">ListenOptions</span><span class="sxs-lookup"><span data-stu-id="ac58f-185">ListenOptions</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.listenoptions)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="ac58f-186">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="ac58f-186">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

<span data-ttu-id="ac58f-187">Kestrel seçenekleri ve sınırları hakkında daha fazla bilgi için bkz:</span><span class="sxs-lookup"><span data-stu-id="ac58f-187">For information about Kestrel options and limits, see:</span></span>

* [<span data-ttu-id="ac58f-188">KestrelServerOptions sınıfı</span><span class="sxs-lookup"><span data-stu-id="ac58f-188">KestrelServerOptions class</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.kestrelserveroptions?view=aspnetcore-1.1)
* [<span data-ttu-id="ac58f-189">KestrelServerLimits</span><span class="sxs-lookup"><span data-stu-id="ac58f-189">KestrelServerLimits</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.kestrelserverlimits?view=aspnetcore-1.1)

---

### <a name="endpoint-configuration"></a><span data-ttu-id="ac58f-190">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ac58f-190">Endpoint configuration</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="ac58f-191">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="ac58f-191">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

::: moniker range="= aspnetcore-2.0"
<span data-ttu-id="ac58f-192">Varsayılan olarak, ASP.NET Core bağlar `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-192">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="ac58f-193">Çağrı [dinleme](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listen) veya [ListenUnixSocket](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listenunixsocket) yöntemlere [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions) Kestrel için URL öneklerini ve bağlantı noktalarını yapılandırmak için.</span><span class="sxs-lookup"><span data-stu-id="ac58f-193">Call [Listen](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listen) or [ListenUnixSocket](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listenunixsocket) methods on [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions) to configure URL prefixes and ports for Kestrel.</span></span> <span data-ttu-id="ac58f-194">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni ayrıca iş ancak daha sonra bu bölümde belirtildiği sınırlamalar vardır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-194">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section.</span></span>

<span data-ttu-id="ac58f-195">`urls` Ana bilgisayar yapılandırma anahtarı konak yapılandırması, uygulama yapılandırması gelmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-195">The `urls` host configuration key must come from the host configuration, not the app configuration.</span></span> <span data-ttu-id="ac58f-196">Ekleme bir `urls` anahtarı ve değeri *appsettings.json* ana bilgisayar yapılandırma yapılandırma dosyasından okunur zamana göre tamamen başlatılmış olduğundan ana bilgisayar yapılandırması etkilemez.</span><span class="sxs-lookup"><span data-stu-id="ac58f-196">Adding a `urls` key and value to *appsettings.json* doesn't affect host configuration because the host is completely initialized by the time the configuration is read from the configuration file.</span></span> <span data-ttu-id="ac58f-197">Ancak, bir `urls` anahtarını *appsettings.json* ile kullanılan [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) ana bilgisayarı yapılandırmak için konak oluşturucu üzerinde:</span><span class="sxs-lookup"><span data-stu-id="ac58f-197">However, a `urls` key in *appsettings.json* can be used with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) on the host builder to configure the host:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("appSettings.json", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseKestrel()
    .UseConfiguration(config)
    .UseContentRoot(Directory.GetCurrentDirectory())
    .UseStartup<Startup>()
    .Build();
```

::: moniker-end
::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ac58f-198">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="ac58f-198">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="ac58f-199">`https://localhost:5001` (bir yerel geliştirme sertifikası var olduğunda)</span><span class="sxs-lookup"><span data-stu-id="ac58f-199">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="ac58f-200">Geliştirme sertifikası oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="ac58f-200">A development certificate is created:</span></span>

* <span data-ttu-id="ac58f-201">Zaman [.NET Core SDK](/dotnet/core/sdk) yüklenir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-201">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="ac58f-202">[Geliştirme sertifikaları aracı](https://github.com/aspnet/DotNetTools/tree/dev/src/dotnet-dev-certs) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-202">The [dev-certs tool](https://github.com/aspnet/DotNetTools/tree/dev/src/dotnet-dev-certs) is used to create a certificate.</span></span>

<span data-ttu-id="ac58f-203">Bazı tarayıcılar, yerel geliştirme sertifika güven açık izni tarayıcıya vermenizi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-203">Some browsers require that you grant explicit permission to the browser to trust the local development certificate.</span></span>

<span data-ttu-id="ac58f-204">ASP.NET Core 2.1 ve üzeri proje şablonları HTTPS üzerinde varsayılan olarak çalışır ve dahil etmek için uygulamaları yapılandırma [HTTPS yeniden yönlendirmesi ve HSTS Destek](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ac58f-204">ASP.NET Core 2.1 and later project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ac58f-205">Çağrı [dinleme](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listen) veya [ListenUnixSocket](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listenunixsocket) yöntemlere [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions) Kestrel için URL öneklerini ve bağlantı noktalarını yapılandırmak için.</span><span class="sxs-lookup"><span data-stu-id="ac58f-205">Call [Listen](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listen) or [ListenUnixSocket](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listenunixsocket) methods on [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions) to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="ac58f-206">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni ayrıca iş ancak (varsayılan sertifikayı HTTPS uç noktası için kullanılabilir olmalıdır Bu bölümde daha sonra belirtilen sınırlamalara sahip Yapılandırma).</span><span class="sxs-lookup"><span data-stu-id="ac58f-206">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="ac58f-207">ASP.NET Core 2.1 `KestrelServerOptions` yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="ac58f-207">ASP.NET Core 2.1 `KestrelServerOptions` configuration:</span></span>

<span data-ttu-id="ac58f-208">**ConfigureEndpointDefaults (Eylem&lt;ListenOptions&gt;)**</span><span class="sxs-lookup"><span data-stu-id="ac58f-208">**ConfigureEndpointDefaults(Action&lt;ListenOptions&gt;)**</span></span>  
<span data-ttu-id="ac58f-209">Bir yapılandırma belirtir `Action` belirtilen her uç nokta için çalıştırılacak.</span><span class="sxs-lookup"><span data-stu-id="ac58f-209">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="ac58f-210">Çağırma `ConfigureEndpointDefaults` öncesinde birden çok kez değiştirir `Action`son s `Action` belirtilen.</span><span class="sxs-lookup"><span data-stu-id="ac58f-210">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

<span data-ttu-id="ac58f-211">**ConfigureHttpsDefaults (Eylem&lt;HttpsConnectionAdapterOptions&gt;)**</span><span class="sxs-lookup"><span data-stu-id="ac58f-211">**ConfigureHttpsDefaults(Action&lt;HttpsConnectionAdapterOptions&gt;)**</span></span>  
<span data-ttu-id="ac58f-212">Bir yapılandırma belirtir `Action` her HTTPS uç noktası için çalıştırılacak.</span><span class="sxs-lookup"><span data-stu-id="ac58f-212">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="ac58f-213">Çağırma `ConfigureHttpsDefaults` öncesinde birden çok kez değiştirir `Action`son s `Action` belirtilen.</span><span class="sxs-lookup"><span data-stu-id="ac58f-213">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

<span data-ttu-id="ac58f-214">**Configure(IConfiguration)**</span><span class="sxs-lookup"><span data-stu-id="ac58f-214">**Configure(IConfiguration)**</span></span>  
<span data-ttu-id="ac58f-215">Alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur bir [IConfiguration](/dotnet/api/microsoft.extensions.configuration.iconfiguration) giriş olarak.</span><span class="sxs-lookup"><span data-stu-id="ac58f-215">Creates a configuration loader for setting up Kestrel that takes an [IConfiguration](/dotnet/api/microsoft.extensions.configuration.iconfiguration) as input.</span></span> <span data-ttu-id="ac58f-216">Yapılandırma yapılandırma bölümü için Kestrel kapsamlı gerekir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-216">The configuration must be scoped to the configuration section for Kestrel.</span></span>

<span data-ttu-id="ac58f-217">**ListenOptions.UseHttps**</span><span class="sxs-lookup"><span data-stu-id="ac58f-217">**ListenOptions.UseHttps**</span></span>  
<span data-ttu-id="ac58f-218">Kestrel HTTPS kullanmak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ac58f-218">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="ac58f-219">`ListenOptions.UseHttps` uzantılar:</span><span class="sxs-lookup"><span data-stu-id="ac58f-219">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="ac58f-220">`UseHttps` &ndash; Kestrel varsayılan sertifika ile HTTPS kullanmak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ac58f-220">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="ac58f-221">Varsayılan Sertifika yapılandırılmışsa, bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ac58f-221">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="ac58f-222">`ListenOptions.UseHttps` Parametreler:</span><span class="sxs-lookup"><span data-stu-id="ac58f-222">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="ac58f-223">`filename` uygulamanın içerik dosyalarını içeren dizine göreli bir sertifika dosyası yolu ve dosya adı değil.</span><span class="sxs-lookup"><span data-stu-id="ac58f-223">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="ac58f-224">`password` X.509 sertifikası verilere erişmek için gerekli paroladır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-224">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="ac58f-225">`configureOptions` olan bir `Action` yapılandırmak için `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-225">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="ac58f-226">Döndürür `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-226">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="ac58f-227">`storeName` sertifika deposundan sertifika yükleneceği olur.</span><span class="sxs-lookup"><span data-stu-id="ac58f-227">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="ac58f-228">`subject` Sertifikanın konu adı var.</span><span class="sxs-lookup"><span data-stu-id="ac58f-228">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="ac58f-229">`allowInvalid` Geçersiz sertifikalar, otomatik olarak imzalanan sertifikalar gibi düşünülmesi gereken olmadığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-229">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="ac58f-230">`location` sertifika yüklemek için depolama konumudur.</span><span class="sxs-lookup"><span data-stu-id="ac58f-230">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="ac58f-231">`serverCertificate` X.509 sertifika yok.</span><span class="sxs-lookup"><span data-stu-id="ac58f-231">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="ac58f-232">Üretimde HTTPS açıkça yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-232">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="ac58f-233">En az bir varsayılan sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-233">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="ac58f-234">Sonraki bölümde açıklandığı desteklenen yapılandırmalar:</span><span class="sxs-lookup"><span data-stu-id="ac58f-234">Supported configurations described next:</span></span>

* <span data-ttu-id="ac58f-235">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="ac58f-235">No configuration</span></span>
* <span data-ttu-id="ac58f-236">Yapılandırmasından varsayılan sertifikayı değiştirin</span><span class="sxs-lookup"><span data-stu-id="ac58f-236">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="ac58f-237">Kodda varsayılan değerlerini değiştirme</span><span class="sxs-lookup"><span data-stu-id="ac58f-237">Change the defaults in code</span></span>

<span data-ttu-id="ac58f-238">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="ac58f-238">*No configuration*</span></span>

<span data-ttu-id="ac58f-239">Kestrel dinlediği `http://localhost:5000` ve `https://localhost:5001` (varsayılan sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="ac58f-239">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<span data-ttu-id="ac58f-240">Kullanarak URL'leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="ac58f-240">Specify URLs using the:</span></span>

* <span data-ttu-id="ac58f-241">`ASPNETCORE_URLS` Ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="ac58f-241">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="ac58f-242">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="ac58f-242">`--urls` command-line argument.</span></span>
* <span data-ttu-id="ac58f-243">`urls` ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="ac58f-243">`urls` host configuration key.</span></span>
* <span data-ttu-id="ac58f-244">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="ac58f-244">`UseUrls` extension method.</span></span>

<span data-ttu-id="ac58f-245">Daha fazla bilgi için bkz: [sunucu URL'leri](xref:fundamentals/hosting#server-urls) ve [geçersiz kılınıyor yapılandırma](xref:fundamentals/hosting#overriding-configuration).</span><span class="sxs-lookup"><span data-stu-id="ac58f-245">For more information, see [Server URLs](xref:fundamentals/hosting#server-urls) and [Overriding configuration](xref:fundamentals/hosting#overriding-configuration).</span></span>

<span data-ttu-id="ac58f-246">Bu yaklaşımı kullanarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktaları (varsayılan sertifika varsa HTTPS) olabilir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-246">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="ac58f-247">Noktalı virgülle ayrılmış liste olarak değerini yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="ac58f-247">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="ac58f-248">*Yapılandırmasından varsayılan sertifikayı değiştirin*</span><span class="sxs-lookup"><span data-stu-id="ac58f-248">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="ac58f-249">[WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) çağrıları `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` varsayılan olarak Kestrel yapılandırma yüklenemedi.</span><span class="sxs-lookup"><span data-stu-id="ac58f-249">[WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="ac58f-250">Bir varsayılan HTTPS uygulama ayarlarını yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-250">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="ac58f-251">Sertifikalar ve URL'leri, disk üzerindeki bir dosyadan veya bir sertifika deposundan kullanmak için de dahil olmak üzere birden fazla uç noktası yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ac58f-251">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="ac58f-252">Aşağıdaki *appsettings.json* örnek:</span><span class="sxs-lookup"><span data-stu-id="ac58f-252">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="ac58f-253">Ayarlama **AllowInvalid** için `true` geçersiz sertifikaları (örneğin, otomatik olarak imzalanan sertifikalar) kullanımına izin vermek için.</span><span class="sxs-lookup"><span data-stu-id="ac58f-253">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="ac58f-254">Bir sertifika belirtmeyen herhangi bir HTTPS uç nokta (**HttpsDefaultCert** aşağıdaki örnekte) altında tanımlanan sertifika için geri döner **sertifikaları** > **varsayılan**  veya geliştirme sertifikası.</span><span class="sxs-lookup"><span data-stu-id="ac58f-254">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
"Kestrel": {
  "EndPoints": {
    "Http": {
      "Url": "http://localhost:5000"
    },

    "HttpsInlineCertFile": {
      "Url": "https://localhost:5001",
      "Certificate": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    },

    "HttpsInlineCertStore": {
      "Url": "https://localhost:5002",
      "Certificate": {
        "Subject": "<subject; required>",
        "Store": "<certificate store; defaults to My>",
        "Location": "<location; defaults to CurrentUser>",
        "AllowInvalid": "<true or false; defaults to false>"
      }
    },

    "HttpsDefaultCert": {
      "Url": "https://localhost:5003"
    },

    "Https": {
      "Url": "https://*:5004",
      "Certificate": {
      "Path": "<path to .pfx file>",
      "Password": "<certificate password>"
      }
    }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="ac58f-255">Kullanmaya alternatif **yolu** ve **parola** herhangi bir sertifika için sertifika deposu alanlarını kullanarak sertifikasını belirtmek için düğümdür.</span><span class="sxs-lookup"><span data-stu-id="ac58f-255">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="ac58f-256">Örneğin, **sertifikaları** > **varsayılan** sertifika olarak belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="ac58f-256">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; defaults to My>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="ac58f-257">Şema Notlar:</span><span class="sxs-lookup"><span data-stu-id="ac58f-257">Schema notes:</span></span>

* <span data-ttu-id="ac58f-258">Uç nokta adları büyük/küçük harfe duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-258">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="ac58f-259">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-259">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="ac58f-260">`Url` Parametresi her uç noktası için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-260">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="ac58f-261">Bu parametre için aynı üst düzey biçimidir `Urls` onun dışında yapılandırma parametresi sınırlı için tek bir değer.</span><span class="sxs-lookup"><span data-stu-id="ac58f-261">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="ac58f-262">Bu uç noktalar üst düzey tanımlanan Değiştir `Urls` ekleyerek yerine yapılandırma.</span><span class="sxs-lookup"><span data-stu-id="ac58f-262">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="ac58f-263">Uç noktaları kodda tanımlanan `Listen` yapılandırma bölümünde tanımlanan uç noktaları birbirinin yerine kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-263">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="ac58f-264">`Certificate` Bölümdür isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-264">The `Certificate` section is optional.</span></span> <span data-ttu-id="ac58f-265">Varsa `Certificate` değil bölümünde belirtilen, önceki senaryolarda tanımlanan varsayılan değerler kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-265">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="ac58f-266">Varsayılan değer varsa, sunucu bir özel durum oluşturur ve başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="ac58f-266">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="ac58f-267">`Certificate` Bölüm destekler **yolu**&ndash;**parola** ve **konu**&ndash;**deposu** sertifikalar.</span><span class="sxs-lookup"><span data-stu-id="ac58f-267">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="ac58f-268">Bağlantı noktası çakışmaları neden olmayan sürece herhangi bir sayıda uç noktaları bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-268">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="ac58f-269">`serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` döndüren bir `KestrelConfigurationLoader` ile bir `.Endpoint(string name, options => { })` yapılandırılmış bir uç noktanın ayarlarını desteklemek için kullanılan yöntem:</span><span class="sxs-lookup"><span data-stu-id="ac58f-269">`serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, options => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

  ```csharp
  serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
      .Endpoint("HTTPS", opt =>
      {
          opt.HttpsOptions.SslProtocols = SslProtocols.Tls12;
      });
  ```

  <span data-ttu-id="ac58f-270">Ayrıca doğrudan erişebilirsiniz `KestrelServerOptions.ConfigurationLoader` tarafından sağlanan gibi varolan yükleyicisi yineleme tutmak için `WebHost.CreatedDeafaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-270">You can also directly access `KestrelServerOptions.ConfigurationLoader` to keep iterating on the existing loader, such as the one provided by `WebHost.CreatedDeafaultBuilder`.</span></span>

* <span data-ttu-id="ac58f-271">Her uç nokta için yapılandırma bölümü bir seçeneklerinde kullanılabilir `Endpoint` yöntemi böylece özel ayarları okuyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ac58f-271">The configuration section for each endpoint is a available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="ac58f-272">Birden çok yapılandırmayı çağırarak yüklenmemiş olabilir `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` yeniden başka bir bölüme sahip.</span><span class="sxs-lookup"><span data-stu-id="ac58f-272">Multiple configurations may be loaded by calling `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` again with another section.</span></span> <span data-ttu-id="ac58f-273">Sürece yalnızca son yapılandırma kullanılır `Load` önceki örneklerinde açık olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-273">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="ac58f-274">Metapackage çağrısı değil `Load` böylece kendi varsayılan yapılandırma bölümü değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-274">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="ac58f-275">`KestrelConfigurationLoader` yansıtmalar `Listen` API'lerden ailesi `KestrelServerOptions` olarak `Endpoint` overloads kod ve yapılandırma bitiş noktaları aynı yerde yapılandırılabilir şekilde.</span><span class="sxs-lookup"><span data-stu-id="ac58f-275">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="ac58f-276">Bu aşırı yoksa adları kullanın ve yalnızca yapılandırma varsayılan ayarları kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-276">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="ac58f-277">*Kodda varsayılan değerlerini değiştirme*</span><span class="sxs-lookup"><span data-stu-id="ac58f-277">*Change the defaults in code*</span></span>

<span data-ttu-id="ac58f-278">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` varsayılan ayarlarını değiştirmek için kullanılan `ListenOptions` ve `HttpsConnectionAdapterOptions`, önceki senaryoda belirtilen varsayılan sertifika geçersiz kılma dahil olmak üzere.</span><span class="sxs-lookup"><span data-stu-id="ac58f-278">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="ac58f-279">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-279">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
options.ConfigureEndpointDefaults(opt =>
{
    opt.NoDelay = true;
});

options.ConfigureHttpsDefaults(httpsOptions =>
{
    httpsOptions.SslProtocols = SslProtocols.Tls12;
});
```

<span data-ttu-id="ac58f-280">*SNI kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="ac58f-280">*Kestrel support for SNI*</span></span>

<span data-ttu-id="ac58f-281">[Sunucu adı göstergesi (SNI)](https://tools.ietf.org/html/rfc6066#section-3) aynı IP adresini ve bağlantı noktası üzerinde birden çok etki alanı ana bilgisayar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-281">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="ac58f-282">Böylece sunucunun doğru sertifikayı sağlayabilirler işlevi SNI için TLS anlaşması sırasında sunucuya güvenli oturum için ana bilgisayar adı istemci gönderir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-282">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="ac58f-283">İstemci furnished sertifika TLS el sıkışma izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-283">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="ac58f-284">Kestrel destekleyen aracılığıyla SNI `ServerCertificateSelector` geri çağırma.</span><span class="sxs-lookup"><span data-stu-id="ac58f-284">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="ac58f-285">Geri arama, ana bilgisayar adı inceleyin ve uygun sertifikayı seçin yazmasına izin vermek için bağlantı bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-285">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="ac58f-286">SNI destek gerektiren hedef framework üzerinde çalışan `netcoreapp2.1`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-286">SNI support requires running on target framework `netcoreapp2.1`.</span></span> <span data-ttu-id="ac58f-287">Üzerinde `netcoreapp2.0` ve `net461`, geri çağırma çağrılır ancak `name` her zaman `null`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-287">On `netcoreapp2.0` and `net461`, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="ac58f-288">`name` De `null` istemci TLS el sıkışma name parametresinde konak sağlamıyorsa.</span><span class="sxs-lookup"><span data-stu-id="ac58f-288">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>

```csharp
WebHost.CreateDefaultBuilder()
    .UseKestrel((context, options) =>
    {
        options.ListenAnyIP(5005, listenOptions =>
        {
            listenOptions.UseHttps(httpsOptions =>
            {
                var localhostCert = CertificateLoader.LoadFromStoreCert(
                    "localhost", "My", StoreLocation.CurrentUser, 
                    allowInvalid: true);
                var exampleCert = CertificateLoader.LoadFromStoreCert(
                    "example.com", "My", StoreLocation.CurrentUser, 
                    allowInvalid: true);
                var subExampleCert = CertificateLoader.LoadFromStoreCert(
                    "sub.example.com", "My", StoreLocation.CurrentUser, 
                    allowInvalid: true);
                var certs = new Dictionary(StringComparer.OrdinalIgnoreCase);
                certs["localhost"] = localhostCert;
                certs["example.com"] = exampleCert;
                certs["sub.example.com"] = subExampleCert;

                httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                {
                    if (name != null && certs.TryGetValue(name, out var cert))
                    {
                        return cert;
                    }

                    return exampleCert;
                };
            });
        });
    });
```

::: moniker-end

<span data-ttu-id="ac58f-289">**Bir TCP yuvası bağlama**</span><span class="sxs-lookup"><span data-stu-id="ac58f-289">**Bind to a TCP socket**</span></span>

<span data-ttu-id="ac58f-290">[Dinleme](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listen) yöntemi bir TCP yuvasını bağlar ve SSL sertifikası yapılandırma seçenekleri lambda verir:</span><span class="sxs-lookup"><span data-stu-id="ac58f-290">The [Listen](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listen) method binds to a TCP socket, and an options lambda permits SSL certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/Program.cs?name=snippet_DefaultBuilder&highlight=9-16)]

<span data-ttu-id="ac58f-291">Nasıl Bu örnek SSL için özel bir uç nokta kullanarak yapılandırır fark [ListenOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.listenoptions).</span><span class="sxs-lookup"><span data-stu-id="ac58f-291">Notice how this example configures SSL for a particular endpoint by using [ListenOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.listenoptions).</span></span> <span data-ttu-id="ac58f-292">Özel uç noktaları diğer Kestrel ayarlarını yapılandırmak için aynı API'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac58f-292">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

<span data-ttu-id="ac58f-293">**Bir UNIX yuvası bağlama**</span><span class="sxs-lookup"><span data-stu-id="ac58f-293">**Bind to a Unix socket**</span></span>

<span data-ttu-id="ac58f-294">Bir UNIX yuvası ile Dinlemenin [ListenUnixSocket](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listenunixsocket) Bu örnekte gösterildiği gibi Nginx ile Gelişmiş performans için:</span><span class="sxs-lookup"><span data-stu-id="ac58f-294">Listen on a Unix socket with [ListenUnixSocket](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listenunixsocket) for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/Program.cs?name=snippet_UnixSocket)]

<span data-ttu-id="ac58f-295">**Bağlantı noktası 0**</span><span class="sxs-lookup"><span data-stu-id="ac58f-295">**Port 0**</span></span>

<span data-ttu-id="ac58f-296">Bağlantı noktası numarasını `0` belirtilirse, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlar.</span><span class="sxs-lookup"><span data-stu-id="ac58f-296">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="ac58f-297">Aşağıdaki örnek, Kestrel çalışma zamanında gerçekten bağlı hangi bağlantı noktasını belirlemek gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ac58f-297">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/Startup.cs?name=snippet_Port0&highlight=3)]

<span data-ttu-id="ac58f-298">Uygulamayı çalıştırdığınızda, konsol penceresi çıktısı uygulama burada ulaşılabilen dinamik bağlantı noktası gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac58f-298">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Now listening on: http://127.0.0.1:48508
```

<span data-ttu-id="ac58f-299">**UseUrls,--URL'leri komut satırı bağımsız değişkeni, URL'ler ana bilgisayar yapılandırma anahtarı ve ASPNETCORE_URLS ortam değişkeni sınırlamaları**</span><span class="sxs-lookup"><span data-stu-id="ac58f-299">**UseUrls, --urls command-line argument, urls host configuration key, and ASPNETCORE_URLS environment variable limitations**</span></span>

<span data-ttu-id="ac58f-300">Uç noktaları ile aşağıdaki yaklaşımlardan yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="ac58f-300">Configure endpoints with the following approaches:</span></span>

* [<span data-ttu-id="ac58f-301">UseUrls</span><span class="sxs-lookup"><span data-stu-id="ac58f-301">UseUrls</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useurls)
* <span data-ttu-id="ac58f-302">`--urls` Komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="ac58f-302">`--urls` command-line argument</span></span>
* <span data-ttu-id="ac58f-303">`urls` ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="ac58f-303">`urls` host configuration key</span></span>
* <span data-ttu-id="ac58f-304">`ASPNETCORE_URLS` Ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="ac58f-304">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="ac58f-305">Bu yöntemleri Kestrel dışında sunucularıyla iş kodu yapmak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-305">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="ac58f-306">Ancak, bu sınırlamaları unutmayın:</span><span class="sxs-lookup"><span data-stu-id="ac58f-306">However, be aware of these limitations:</span></span>

* <span data-ttu-id="ac58f-307">SSL HTTPS uç nokta yapılandırmasında bir varsayılan sertifika sağlanmadığı sürece bu yaklaşımlar ile kullanılamaz (örneğin, kullanarak `KestrelServerOptions` yapılandırma veya bu konuda daha önce gösterildiği gibi bir yapılandırma dosyası).</span><span class="sxs-lookup"><span data-stu-id="ac58f-307">SSL can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="ac58f-308">Zaman hem `Listen` ve `UseUrls` yaklaşımlar eşzamanlı olarak kullanılan `Listen` uç noktaları geçersiz kılma `UseUrls` uç noktaları.</span><span class="sxs-lookup"><span data-stu-id="ac58f-308">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

<span data-ttu-id="ac58f-309">**IIS bitiş noktası yapılandırması**</span><span class="sxs-lookup"><span data-stu-id="ac58f-309">**IIS endpoint configuration**</span></span>

<span data-ttu-id="ac58f-310">IIS geçersiz kılmak için IIS, URL bağlamaları kullanılırken bağlamaları tarafından ayarlanan `Listen` veya `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-310">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="ac58f-311">Daha fazla bilgi için bkz: [ASP.NET Core Modülü](xref:fundamentals/servers/aspnet-core-module) konu.</span><span class="sxs-lookup"><span data-stu-id="ac58f-311">For more information, see the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) topic.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="ac58f-312">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="ac58f-312">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

<span data-ttu-id="ac58f-313">Varsayılan olarak, ASP.NET Core bağlar `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-313">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="ac58f-314">URL öneklerini ve Kestrel kullanarak bağlantı noktalarını yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="ac58f-314">Configure URL prefixes and ports for Kestrel using:</span></span>

* <span data-ttu-id="ac58f-315">[UseUrls](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useurls?view=aspnetcore-1.1) genişletme yöntemi</span><span class="sxs-lookup"><span data-stu-id="ac58f-315">[UseUrls](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useurls?view=aspnetcore-1.1) extension method</span></span>
* <span data-ttu-id="ac58f-316">`--urls` Komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="ac58f-316">`--urls` command-line argument</span></span>
* <span data-ttu-id="ac58f-317">`urls` ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="ac58f-317">`urls` host configuration key</span></span>
* <span data-ttu-id="ac58f-318">ASP.NET Core yapılandırma sistemi de dahil olmak üzere `ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="ac58f-318">ASP.NET Core configuration system, including `ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="ac58f-319">Bu yöntemleri hakkında daha fazla bilgi için bkz: [barındırma](xref:fundamentals/hosting).</span><span class="sxs-lookup"><span data-stu-id="ac58f-319">For more information on these methods, see [Hosting](xref:fundamentals/hosting).</span></span>

<span data-ttu-id="ac58f-320">**IIS bitiş noktası yapılandırması**</span><span class="sxs-lookup"><span data-stu-id="ac58f-320">**IIS endpoint configuration**</span></span>

<span data-ttu-id="ac58f-321">IIS kullanırken, IIS için URL bağlamaları tarafından belirlenen bağlamaları geçersiz kılma `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-321">When using IIS, the URL bindings for IIS override bindings set by `UseUrls`.</span></span> <span data-ttu-id="ac58f-322">Daha fazla bilgi için bkz: [ASP.NET Core Modülü](xref:fundamentals/servers/aspnet-core-module) konu.</span><span class="sxs-lookup"><span data-stu-id="ac58f-322">For more information, see the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) topic.</span></span>

---

::: moniker range=">= aspnetcore-2.1"

## <a name="transport-configuration"></a><span data-ttu-id="ac58f-323">Taşıyıcı Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ac58f-323">Transport configuration</span></span>

<span data-ttu-id="ac58f-324">ASP.NET Core 2.1 sürümünde Kestrel'ın varsayılan aktarım artık Libuv üzerinde temel ancak bunun yerine yönetilen yuvalarda dayalı.</span><span class="sxs-lookup"><span data-stu-id="ac58f-324">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="ac58f-325">Bu çağrı 2.1 yükseltme ASP.NET Core 2.0 uygulamaları için önemli bir değişiklik olduğunu [WebHostBuilderLibuvExtensions.UseLibuv](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderlibuvextensions.uselibuv) ve aşağıdaki paketlerden birini bağlıdır:</span><span class="sxs-lookup"><span data-stu-id="ac58f-325">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call [WebHostBuilderLibuvExtensions.UseLibuv](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderlibuvextensions.uselibuv) and depend on either of the following packages:</span></span>

* <span data-ttu-id="ac58f-326">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paketi Başvurusu)</span><span class="sxs-lookup"><span data-stu-id="ac58f-326">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="ac58f-327">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="ac58f-327">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="ac58f-328">ASP.NET Core 2.1 veya sonrasını kullanan projeleri `Microsoft.AspNetCore.App` metapackage ve Libuv kullanılmasını gerektirir:</span><span class="sxs-lookup"><span data-stu-id="ac58f-328">For ASP.NET Core 2.1 or later projects that use the `Microsoft.AspNetCore.App` metapackage and require the use of Libuv:</span></span>

* <span data-ttu-id="ac58f-329">İçin bağımlılık ekleme [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) uygulamanın proje dosyası paketi:</span><span class="sxs-lookup"><span data-stu-id="ac58f-329">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv" 
                    Version="2.1.0" />
    ```

* <span data-ttu-id="ac58f-330">Çağrı [WebHostBuilderLibuvExtensions.UseLibuv](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderlibuvextensions.uselibuv):</span><span class="sxs-lookup"><span data-stu-id="ac58f-330">Call [WebHostBuilderLibuvExtensions.UseLibuv](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderlibuvextensions.uselibuv):</span></span>

    ```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateWebHostBuilder(args).Build().Run();
        }

        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseLibuv()
                .UseStartup<Startup>();
    }
    ```

::: moniker-end

### <a name="url-prefixes"></a><span data-ttu-id="ac58f-331">URL öneklerini</span><span class="sxs-lookup"><span data-stu-id="ac58f-331">URL prefixes</span></span>

<span data-ttu-id="ac58f-332">Kullanırken `UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı, veya `ASPNETCORE_URLS` ortam değişkeni URL öneklerini olabilir aşağıdaki biçimlerden birini.</span><span class="sxs-lookup"><span data-stu-id="ac58f-332">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="ac58f-333">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="ac58f-333">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

<span data-ttu-id="ac58f-334">Yalnızca HTTP URL öneklerini geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-334">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="ac58f-335">Kestrel SSL'yi desteklemez kullanarak URL bağlamaları yapılandırılırken `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-335">Kestrel doesn't support SSL when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="ac58f-336">Bağlantı noktası numarası ile birlikte IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="ac58f-336">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="ac58f-337">`0.0.0.0` tüm IPv4 adreslerine bağlar özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="ac58f-337">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="ac58f-338">Bağlantı noktası numarası ile birlikte IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="ac58f-338">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="ac58f-339">`[::]` IPv4 IPv6 eşdeğerdir `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-339">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="ac58f-340">Bağlantı noktası numarası ile ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="ac58f-340">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="ac58f-341">Ana makine adları, `*`, ve `+`, özel değildir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-341">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="ac58f-342">Herhangi bir şey geçerli bir IP adresi tanınmıyor veya `localhost` tüm IPv4 ve IPv6 IP bağlar.</span><span class="sxs-lookup"><span data-stu-id="ac58f-342">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="ac58f-343">Farklı ana bilgisayar adları aynı bağlantı noktasında farklı ASP.NET Core uygulamaları bağlamak için kullanın [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, Nginx ya da Apache gibi bir ters proxy sunucusu.</span><span class="sxs-lookup"><span data-stu-id="ac58f-343">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="ac58f-344">Etkin bir ters proxy filtreleme ana bilgisayarla kullanmayan etkinleştirirseniz [ana bilgisayar filtre](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ac58f-344">If not using a reverse proxy with host filtering enabled, enable [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="ac58f-345">Ana bilgisayar `localhost` bağlantı noktası numarası veya geri döngü IP bağlantı noktası numarası ile birlikte ad</span><span class="sxs-lookup"><span data-stu-id="ac58f-345">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="ac58f-346">Zaman `localhost` belirtilirse, IPv4 ve IPv6 geri döngü arabirimlere bağlamak Kestrel çalışır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-346">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="ac58f-347">İstenen bağlantı noktası başka bir hizmet ya da geri döngü arabirimde tarafından kullanılıyor Kestrel başlatmak başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="ac58f-347">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="ac58f-348">Herhangi bir nedenden dolayı ya da geri döngü arabirimi kullanılabilir olup olmadığını (genellikle IPv6 desteklenmediğinden çoğu), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="ac58f-348">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="ac58f-349">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="ac58f-349">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

* <span data-ttu-id="ac58f-350">Bağlantı noktası numarası ile birlikte IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="ac58f-350">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  https://65.55.39.10:443/
  ```

  <span data-ttu-id="ac58f-351">`0.0.0.0` tüm IPv4 adreslerine bağlar özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="ac58f-351">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="ac58f-352">Bağlantı noktası numarası ile birlikte IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="ac58f-352">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  https://[0:0:0:0:0:ffff:4137:270a]:443/
  ```

  <span data-ttu-id="ac58f-353">`[::]` IPv4 IPv6 eşdeğerdir `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-353">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="ac58f-354">Bağlantı noktası numarası ile ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="ac58f-354">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  https://contoso.com:443/
  https://*:443/
  ```

  <span data-ttu-id="ac58f-355">Ana makine adları, `*`, ve `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-355">Host names, `*`, and `+` aren't special.</span></span> <span data-ttu-id="ac58f-356">Tanınan bir IP adresi olmayan bir şey veya `localhost` tüm IPv4 ve IPv6 IP bağlar.</span><span class="sxs-lookup"><span data-stu-id="ac58f-356">Anything that isn't a recognized IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="ac58f-357">Farklı ana bilgisayar adları aynı bağlantı noktasında farklı ASP.NET Core uygulamaları bağlamak için kullanın [WebListener](xref:fundamentals/servers/weblistener) veya IIS, Nginx ya da Apache gibi bir ters proxy sunucusu.</span><span class="sxs-lookup"><span data-stu-id="ac58f-357">To bind different host names to different ASP.NET Core apps on the same port, use [WebListener](xref:fundamentals/servers/weblistener) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

* <span data-ttu-id="ac58f-358">Ana bilgisayar `localhost` bağlantı noktası numarası veya geri döngü IP bağlantı noktası numarası ile birlikte ad</span><span class="sxs-lookup"><span data-stu-id="ac58f-358">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="ac58f-359">Zaman `localhost` belirtilirse, IPv4 ve IPv6 geri döngü arabirimlere bağlamak Kestrel çalışır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-359">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="ac58f-360">İstenen bağlantı noktası başka bir hizmet ya da geri döngü arabirimde tarafından kullanılıyor Kestrel başlatmak başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="ac58f-360">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="ac58f-361">Herhangi bir nedenden dolayı ya da geri döngü arabirimi kullanılabilir olup olmadığını (genellikle IPv6 desteklenmediğinden çoğu), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="ac58f-361">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

* <span data-ttu-id="ac58f-362">UNIX yuva</span><span class="sxs-lookup"><span data-stu-id="ac58f-362">Unix socket</span></span>

  ```
  http://unix:/run/dan-live.sock
  ```

<span data-ttu-id="ac58f-363">**Bağlantı noktası 0**</span><span class="sxs-lookup"><span data-stu-id="ac58f-363">**Port 0**</span></span>

<span data-ttu-id="ac58f-364">Bağlantı noktası numarasını olduğunda `0` belirtilirse, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlar.</span><span class="sxs-lookup"><span data-stu-id="ac58f-364">When the port number is `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="ac58f-365">Bağlantı noktasına bağlama `0` herhangi bir ana bilgisayar adı veya IP dışında için izin verilen `localhost`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-365">Binding to port `0` is allowed for any host name or IP except for `localhost`.</span></span>

<span data-ttu-id="ac58f-366">Uygulamayı çalıştırdığınızda, konsol penceresi çıktısı uygulama burada ulaşılabilen dinamik bağlantı noktası gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac58f-366">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Now listening on: http://127.0.0.1:48508
```

<span data-ttu-id="ac58f-367">**SSL için URL öneklerini**</span><span class="sxs-lookup"><span data-stu-id="ac58f-367">**URL prefixes for SSL**</span></span>

<span data-ttu-id="ac58f-368">Çağırma varsa `UseHttps` genişletme yöntemi ile URL öneklerini eklediğinizden emin olun `https:`:</span><span class="sxs-lookup"><span data-stu-id="ac58f-368">If calling the `UseHttps` extension method, be sure to include URL prefixes with `https:`:</span></span>

```csharp
var host = new WebHostBuilder()
    .UseKestrel(options =>
    {
        options.UseHttps("testCert.pfx", "testPassword");
    })
   .UseUrls("http://localhost:5000", "https://localhost:5001")
   .UseContentRoot(Directory.GetCurrentDirectory())
   .UseStartup<Startup>()
   .Build();
```

> [!NOTE]
> <span data-ttu-id="ac58f-369">Aynı bağlantı noktasında HTTPS ve HTTP barındırılamaz.</span><span class="sxs-lookup"><span data-stu-id="ac58f-369">HTTPS and HTTP can't be hosted on the same port.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

---

## <a name="host-filtering"></a><span data-ttu-id="ac58f-370">Ana bilgisayar filtre</span><span class="sxs-lookup"><span data-stu-id="ac58f-370">Host filtering</span></span>

<span data-ttu-id="ac58f-371">Kestrel gibi ön eklerine göre yapılandırma desteklerken `http://example.com:5000`, Kestrel büyük ölçüde ana bilgisayar adı yok sayar.</span><span class="sxs-lookup"><span data-stu-id="ac58f-371">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="ac58f-372">Ana bilgisayar `localhost` bağlama geri döngü adresleri için kullanılan özel bir durum.</span><span class="sxs-lookup"><span data-stu-id="ac58f-372">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="ac58f-373">Açık bir IP adresi tüm ortak IP adresine bağlar daha herhangi diğer barındırır.</span><span class="sxs-lookup"><span data-stu-id="ac58f-373">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="ac58f-374">Bu bilgilerin hiçbiri isteği doğrulamak için kullanılan `Host` üstbilgileri.</span><span class="sxs-lookup"><span data-stu-id="ac58f-374">None of this information is used to validate request `Host` headers.</span></span>

<span data-ttu-id="ac58f-375">İki geçici çözüm vardır:</span><span class="sxs-lookup"><span data-stu-id="ac58f-375">There are two workarounds:</span></span>

* <span data-ttu-id="ac58f-376">Ana bilgisayar üstbilgisi filtreleme ile ters Ara sunucu arkasındaki ana bilgisayarı.</span><span class="sxs-lookup"><span data-stu-id="ac58f-376">Host behind a reverse proxy with host header filtering.</span></span> <span data-ttu-id="ac58f-377">ASP.NET Core Kestrel için desteklenen tek senaryo, bu 1.x.</span><span class="sxs-lookup"><span data-stu-id="ac58f-377">This was the only supported scenario for Kestrel in ASP.NET Core 1.x.</span></span>
* <span data-ttu-id="ac58f-378">Filtre uygulamak için bir ara yazılım istekleri tarafından kullanım `Host` üstbilgi.</span><span class="sxs-lookup"><span data-stu-id="ac58f-378">Use a middleware to filter requests by the `Host` header.</span></span> <span data-ttu-id="ac58f-379">Bir örnek ara yazılımı aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="ac58f-379">A sample middleware follows:</span></span>

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Primitives;
using Microsoft.Net.Http.Headers;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

// A normal middleware would provide an options type, config binding, extension methods, etc..
// This intentionally does all of the work inside of the middleware so it can be
// easily copy-pasted into docs and other projects.
public class HostFilteringMiddleware
{
    private readonly RequestDelegate _next;
    private readonly IList<string> _hosts;
    private readonly ILogger<HostFilteringMiddleware> _logger;

    public HostFilteringMiddleware(RequestDelegate next, IConfiguration config, ILogger<HostFilteringMiddleware> logger)
    {
        if (config == null)
        {
            throw new ArgumentNullException(nameof(config));
        }

        _next = next ?? throw new ArgumentNullException(nameof(next));
        _logger = logger ?? throw new ArgumentNullException(nameof(logger));

        // A semicolon separated list of host names without the port numbers.
        // IPv6 addresses must use the bounding brackets and be in their normalized form.
        _hosts = config["AllowedHosts"]?.Split(new[] { ';' }, StringSplitOptions.RemoveEmptyEntries);
        if (_hosts == null || _hosts.Count == 0)
        {
            throw new InvalidOperationException("No configuration entry found for AllowedHosts.");
        }
    }

    public Task Invoke(HttpContext context)
    {
        if (!ValidateHost(context))
        {
            context.Response.StatusCode = 400;
            _logger.LogDebug("Request rejected due to incorrect Host header.");
            return Task.CompletedTask;
        }

        return _next(context);
    }

    // This does not duplicate format validations that are expected to be performed by the host.
    private bool ValidateHost(HttpContext context)
    {
        StringSegment host = context.Request.Headers[HeaderNames.Host].ToString().Trim();

        if (StringSegment.IsNullOrEmpty(host))
        {
            // Http/1.0 does not require the Host header.
            // Http/1.1 requires the header but the value may be empty.
            return true;
        }

        // Drop the port

        var colonIndex = host.LastIndexOf(':');

        // IPv6 special case
        if (host.StartsWith("[", StringComparison.Ordinal))
        {
            var endBracketIndex = host.IndexOf(']');
            if (endBracketIndex < 0)
            {
                // Invalid format
                return false;
            }
            if (colonIndex < endBracketIndex)
            {
                // No port, just the IPv6 Host
                colonIndex = -1;
            }
        }

        if (colonIndex > 0)
        {
            host = host.Subsegment(0, colonIndex);
        }

        foreach (var allowedHost in _hosts)
        {
            if (StringSegment.Equals(allowedHost, host, StringComparison.OrdinalIgnoreCase))
            {
                return true;
            }

            // Sub-domain wildcards: *.example.com
            if (allowedHost.StartsWith("*.", StringComparison.Ordinal) && host.Length >= allowedHost.Length)
            {
                // .example.com
                var allowedRoot = new StringSegment(allowedHost, 1, allowedHost.Length - 1);

                var hostRoot = host.Subsegment(host.Length - allowedRoot.Length, allowedRoot.Length);
                if (hostRoot.Equals(allowedRoot, StringComparison.OrdinalIgnoreCase))
                {
                    return true;
                }
            }
        }

        return false;
    }
}
```

<span data-ttu-id="ac58f-380">Yukarıdaki kayıt `HostFilteringMiddleware` içinde `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ac58f-380">Register the preceding `HostFilteringMiddleware` in `Startup.Configure`.</span></span> <span data-ttu-id="ac58f-381">Unutmayın [ara yazılım kaydı sıralama](xref:fundamentals/middleware/index#ordering) önemlidir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-381">Note that the [ordering of middleware registration](xref:fundamentals/middleware/index#ordering) is important.</span></span> <span data-ttu-id="ac58f-382">Kayıt hemen tanılama Ara kayıttan sonra gerçekleşmelidir (örneğin, `app.UseExceptionHandler`).</span><span class="sxs-lookup"><span data-stu-id="ac58f-382">Registration should occur immediately after Diagnostic Middleware registration (for example, `app.UseExceptionHandler`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseBrowserLink();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseMiddleware<HostFilteringMiddleware>();

    app.UseMvcWithDefaultRoute();
}
```

<span data-ttu-id="ac58f-383">Önceki Ara bekliyor bir `AllowedHosts` anahtarını *appsettings.\< EnvironmentName > .json*.</span><span class="sxs-lookup"><span data-stu-id="ac58f-383">The preceding middleware expects an `AllowedHosts` key in *appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="ac58f-384">Bu anahtarın değeri, bağlantı noktası numaralarını olmadan ana bilgisayar adlarını noktalı virgülle ayrılmış bir listesidir.</span><span class="sxs-lookup"><span data-stu-id="ac58f-384">This key's value is a semicolon-delimited list of host names without the port numbers.</span></span> <span data-ttu-id="ac58f-385">Dahil `AllowedHosts` anahtar-değer çifti *appsettings. Production.JSON*:</span><span class="sxs-lookup"><span data-stu-id="ac58f-385">Include the `AllowedHosts` key-value pair in *appsettings.Production.json*:</span></span>

```json
{
  "AllowedHosts": "example.com"
}
```

<span data-ttu-id="ac58f-386">*appSettings. Development.JSON* (localhost yapılandırma dosyası):</span><span class="sxs-lookup"><span data-stu-id="ac58f-386">*appsettings.Development.json* (localhost configuration file):</span></span>

```json
{
  "AllowedHosts": "localhost"
}
```

## <a name="additional-resources"></a><span data-ttu-id="ac58f-387">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ac58f-387">Additional resources</span></span>

* [<span data-ttu-id="ac58f-388">HTTPS'yi Zorunlu Kılma</span><span class="sxs-lookup"><span data-stu-id="ac58f-388">Enforce HTTPS</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="ac58f-389">Kestrel kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="ac58f-389">Kestrel source code</span></span>](https://github.com/aspnet/KestrelHttpServer)
