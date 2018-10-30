---
title: SignalR ve ASP.NET Core SignalR arasındaki farklar
author: tdykstra
description: SignalR ve ASP.NET Core SignalR arasındaki farklar
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.date: 09/10/2018
uid: signalr/version-differences
ms.openlocfilehash: 3cec37719b743b3c805ada77249f526278e44599
ms.sourcegitcommit: 2ef32676c16f76282f7c23154d13affce8c8bf35
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50234611"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="d7a64-103">ASP.NET SignalR ile ASP.NET Core SignalR arasındaki farklar</span><span class="sxs-lookup"><span data-stu-id="d7a64-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="d7a64-104">ASP.NET Core SignalR istemciler veya sunucular için ASP.NET SignalR ile uyumlu değildir.</span><span class="sxs-lookup"><span data-stu-id="d7a64-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="d7a64-105">Bu makalede, kaldırılmış veya ASP.NET Core SignalR öğesinde değiştirilen özellikler açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d7a64-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="d7a64-106">SignalR sürüm belirleme</span><span class="sxs-lookup"><span data-stu-id="d7a64-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="d7a64-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="d7a64-107">ASP.NET SignalR</span></span> | <span data-ttu-id="d7a64-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="d7a64-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="d7a64-109">Sunucu NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="d7a64-109">Server NuGet Package</span></span> | [<span data-ttu-id="d7a64-110">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="d7a64-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="d7a64-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="d7a64-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="d7a64-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="d7a64-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span></span> |
| <span data-ttu-id="d7a64-113">İstemci NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="d7a64-113">Client NuGet Packages</span></span> | [<span data-ttu-id="d7a64-114">Microsoft.AspNet.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="d7a64-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="d7a64-115">Microsoft.AspNet.SignalR.JS</span><span class="sxs-lookup"><span data-stu-id="d7a64-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="d7a64-116">Microsoft.AspNetCore.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="d7a64-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="d7a64-117">İstemci npm paket</span><span class="sxs-lookup"><span data-stu-id="d7a64-117">Client npm Package</span></span> | [<span data-ttu-id="d7a64-118">signalr</span><span class="sxs-lookup"><span data-stu-id="d7a64-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="d7a64-119">Sunucu uygulama türü</span><span class="sxs-lookup"><span data-stu-id="d7a64-119">Server App Type</span></span> | <span data-ttu-id="d7a64-120">ASP.NET (System.Web) veya OWIN barındırma</span><span class="sxs-lookup"><span data-stu-id="d7a64-120">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="d7a64-121">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7a64-121">ASP.NET Core</span></span> |
| <span data-ttu-id="d7a64-122">Desteklenen sunucu platformları</span><span class="sxs-lookup"><span data-stu-id="d7a64-122">Supported Server Platforms</span></span> | <span data-ttu-id="d7a64-123">.NET framework 4.5 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="d7a64-123">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="d7a64-124">.NET framework 4.6.1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="d7a64-124">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="d7a64-125">.NET core 2.1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="d7a64-125">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="d7a64-126">Özellik farkları</span><span class="sxs-lookup"><span data-stu-id="d7a64-126">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="d7a64-127">Otomatik yeniden bağlantılar</span><span class="sxs-lookup"><span data-stu-id="d7a64-127">Automatic reconnects</span></span>

<span data-ttu-id="d7a64-128">ASP.NET Core SignalR öğesinde otomatik yeniden bağlantılar desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="d7a64-128">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="d7a64-129">İstemci bağlantısı kesilmişse, kullanıcı yeniden bağlamak isterseniz yeni bir bağlantı açıkça başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d7a64-129">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="d7a64-130">ASP.NET SignalR SignalR bağlantısı kesilirse, sunucuya yeniden dener.</span><span class="sxs-lookup"><span data-stu-id="d7a64-130">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

### <a name="protocol-support"></a><span data-ttu-id="d7a64-131">Protokol desteği</span><span class="sxs-lookup"><span data-stu-id="d7a64-131">Protocol support</span></span>

<span data-ttu-id="d7a64-132">ASP.NET Core Signalr'yi destekleyen dayalı yeni bir ikili Protokolü yanı sıra JSON [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="d7a64-132">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="d7a64-133">Ayrıca, özel protokoller oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="d7a64-133">Additionally, custom protocols can be created.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="d7a64-134">Sunucuda farkları</span><span class="sxs-lookup"><span data-stu-id="d7a64-134">Differences on the server</span></span>

<span data-ttu-id="d7a64-135">ASP.NET Core SignalR sunucu tarafı kitaplıklar dahil [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) parçası olan paket **ASP.NET Core Web uygulaması** Razor hem MVC şablonu projeleri.</span><span class="sxs-lookup"><span data-stu-id="d7a64-135">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="d7a64-136">ASP.NET Core SignalR çağırarak yapılandırılmalıdır bir ASP.NET Core ara yazılımını olduğundan [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) içinde `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d7a64-136">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

<span data-ttu-id="d7a64-137">Yönlendirmeyi yapılandırmak için eşleme rotaları hub içinde [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) yöntem çağrısı `Startup.Configure` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="d7a64-137">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

### <a name="sticky-sessions"></a><span data-ttu-id="d7a64-138">Yapışkan oturumlar</span><span class="sxs-lookup"><span data-stu-id="d7a64-138">Sticky sessions</span></span>

<span data-ttu-id="d7a64-139">ASP.NET SignalR ölçeğini genişletme modeli yeniden ve gruptaki herhangi bir sunucuya ileti gönderme etmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="d7a64-139">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="d7a64-140">ASP.NET Core SignalR öğesinde, istemci ile aynı sunucuya bağlantı süresi boyunca etkileşim kurmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d7a64-140">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="d7a64-141">Redis kullanarak genişletme için Yapışkan oturumlar gerekli olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="d7a64-141">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="d7a64-142">Genişletme kullanma [Azure SignalR hizmeti](/azure/azure-signalr/), Yapışkan oturumlar bulunmamaktır hizmet bağlantıları istemcilere hallettiğinden.</span><span class="sxs-lookup"><span data-stu-id="d7a64-142">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span> 

### <a name="single-hub-per-connection"></a><span data-ttu-id="d7a64-143">Bağlantı başına tek bir hub</span><span class="sxs-lookup"><span data-stu-id="d7a64-143">Single hub per connection</span></span>

<span data-ttu-id="d7a64-144">ASP.NET Core SignalR öğesinde bağlantı modeli basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d7a64-144">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="d7a64-145">Bağlantıları, doğrudan erişim birden çok hub'a paylaşmak için kullanılan tek bir bağlantı yerine tek bir hub için gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="d7a64-145">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="d7a64-146">Akış</span><span class="sxs-lookup"><span data-stu-id="d7a64-146">Streaming</span></span>

<span data-ttu-id="d7a64-147">ASP.NET Core SignalR destekler [akış verileri](xref:signalr/streaming) istemciye hub'ından.</span><span class="sxs-lookup"><span data-stu-id="d7a64-147">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="d7a64-148">Durum</span><span class="sxs-lookup"><span data-stu-id="d7a64-148">State</span></span>

<span data-ttu-id="d7a64-149">İlerleme durumu iletileri için destek yanı sıra hub'ı (HubState olarak da adlandırılır) ve istemciler arasında rastgele bir durum geçirme özelliği kaldırıldı.</span><span class="sxs-lookup"><span data-stu-id="d7a64-149">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="d7a64-150">Şu anda hiçbir hub proxy karşılığı yoktur.</span><span class="sxs-lookup"><span data-stu-id="d7a64-150">There is no counterpart of hub proxies at the moment.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="d7a64-151">İstemci üzerinde farkları</span><span class="sxs-lookup"><span data-stu-id="d7a64-151">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="d7a64-152">TypeScript</span><span class="sxs-lookup"><span data-stu-id="d7a64-152">TypeScript</span></span>

<span data-ttu-id="d7a64-153">ASP.NET Core SignalR istemci yazıldığı [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="d7a64-153">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="d7a64-154">JavaScript veya TypeScript kullanırken yazabileceğiniz [JavaScript istemci](xref:signalr/javascript-client).</span><span class="sxs-lookup"><span data-stu-id="d7a64-154">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="d7a64-155">JavaScript istemcisi, barındırılan [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="d7a64-155">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="d7a64-156">Önceki sürümlerde, Visual Studio'da bir NuGet paketi aracılığıyla JavaScript istemci alındı.</span><span class="sxs-lookup"><span data-stu-id="d7a64-156">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="d7a64-157">Çekirdek sürümleri için [ @aspnet/signalr ](https://www.npmjs.com/package/@aspnet/signalr) npm paket JavaScript kitaplıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="d7a64-157">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="d7a64-158">Bu paket bulunup bulunmadığına **ASP.NET Core Web uygulaması** şablonu.</span><span class="sxs-lookup"><span data-stu-id="d7a64-158">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="d7a64-159">Elde etmek ve yüklemek için npm kullanın `@aspnet/signalr` npm paketi.</span><span class="sxs-lookup"><span data-stu-id="d7a64-159">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="d7a64-160">jQuery</span><span class="sxs-lookup"><span data-stu-id="d7a64-160">jQuery</span></span>

<span data-ttu-id="d7a64-161">JQuery bağımlı projeler jQuery kullanmaya devam edebilirsiniz ancak kaldırıldı.</span><span class="sxs-lookup"><span data-stu-id="d7a64-161">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="d7a64-162">JavaScript istemci yöntem sözdizimi</span><span class="sxs-lookup"><span data-stu-id="d7a64-162">JavaScript client method syntax</span></span>

<span data-ttu-id="d7a64-163">JavaScript sözdizimi SignalR önceki sürümünden değişmiştir.</span><span class="sxs-lookup"><span data-stu-id="d7a64-163">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="d7a64-164">Yerine `$connection` nesne, bir bağlantı kullanarak oluşturduğunuz [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span><span class="sxs-lookup"><span data-stu-id="d7a64-164">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="d7a64-165">Kullanım [üzerinde](/javascript/api/@aspnet/signalr/HubConnection#on) hub çağırabilirsiniz istemci yöntemlerini belirtmek için yöntemi.</span><span class="sxs-lookup"><span data-stu-id="d7a64-165">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="d7a64-166">İstemci yöntemi oluşturduktan sonra hub Bağlantısı'nı başlatın.</span><span class="sxs-lookup"><span data-stu-id="d7a64-166">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="d7a64-167">Zincir bir [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) oturum veya hataları işlemek için bir yöntem.</span><span class="sxs-lookup"><span data-stu-id="d7a64-167">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="d7a64-168">Hub proxy</span><span class="sxs-lookup"><span data-stu-id="d7a64-168">Hub proxies</span></span>

<span data-ttu-id="d7a64-169">Hub proxy artık otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d7a64-169">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="d7a64-170">Bunun yerine, yöntem adı yöntemlere geçirilen [çağırma](/javascript/api/%40aspnet/signalr/hubconnection#invoke) dize olarak API.</span><span class="sxs-lookup"><span data-stu-id="d7a64-170">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="d7a64-171">.NET ve diğer istemciler</span><span class="sxs-lookup"><span data-stu-id="d7a64-171">.NET and other clients</span></span>

<span data-ttu-id="d7a64-172">`Microsoft.AspNetCore.SignalR.Client` NuGet paketi, ASP.NET Core SignalR için .NET istemci kitaplıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="d7a64-172">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="d7a64-173">Kullanım [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) ve hub'ına bağlantı örneğini oluşturmak için.</span><span class="sxs-lookup"><span data-stu-id="d7a64-173">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="d7a64-174">Genişletme farkları</span><span class="sxs-lookup"><span data-stu-id="d7a64-174">Scaleout differences</span></span>

<span data-ttu-id="d7a64-175">ASP.NET SignalR, SQL Server ve Redis destekler.</span><span class="sxs-lookup"><span data-stu-id="d7a64-175">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="d7a64-176">Azure SignalR hizmeti ve Redis ASP.NET Core SignalR destekler.</span><span class="sxs-lookup"><span data-stu-id="d7a64-176">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="d7a64-177">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="d7a64-177">ASP.NET</span></span>

* [<span data-ttu-id="d7a64-178">Azure Service Bus ile SignalR ölçeğini genişletme</span><span class="sxs-lookup"><span data-stu-id="d7a64-178">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="d7a64-179">Redis ile SignalR ölçeğini genişletme</span><span class="sxs-lookup"><span data-stu-id="d7a64-179">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="d7a64-180">SQL Server ile SignalR ölçeğini genişletme</span><span class="sxs-lookup"><span data-stu-id="d7a64-180">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="d7a64-181">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7a64-181">ASP.NET Core</span></span>

* [<span data-ttu-id="d7a64-182">Azure SignalR hizmeti</span><span class="sxs-lookup"><span data-stu-id="d7a64-182">Azure SignalR Service</span></span>](/azure/azure-signalr/)

## <a name="additional-resources"></a><span data-ttu-id="d7a64-183">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d7a64-183">Additional resources</span></span>

* [<span data-ttu-id="d7a64-184">Merkezler</span><span class="sxs-lookup"><span data-stu-id="d7a64-184">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="d7a64-185">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="d7a64-185">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="d7a64-186">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="d7a64-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="d7a64-187">Desteklenen platformlar</span><span class="sxs-lookup"><span data-stu-id="d7a64-187">Supported platforms</span></span>](xref:signalr/supported-platforms)
