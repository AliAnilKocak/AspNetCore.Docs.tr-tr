---
title: ASP.NET Core SignalR yapılandırma
author: bradygaster
description: ASP.NET Core SignalR uygulamalarını yapılandırmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/07/2019
uid: signalr/configuration
ms.openlocfilehash: c5921db895a732c9663c9d962195a2c0635f5aa0
ms.sourcegitcommit: 6ddd8a7675c1c1d997c8ab2d4498538e44954cac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57400664"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="b8f7e-103">ASP.NET Core SignalR yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b8f7e-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b8f7e-104">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="b8f7e-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b8f7e-105">ASP.NET Core SignalR iletileri kodlama için iki protokol destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b8f7e-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b8f7e-106">Her protokolü, serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b8f7e-107">JSON seri hale getirme kullanılarak sunucuda yapılandırılabilir [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) sonra eklenen genişletme yöntemi [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) içinde `Startup.ConfigureServices` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b8f7e-108">`AddJsonProtocol` Aldığında bir temsilci yöntemi alır bir `options` nesne.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b8f7e-109">[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği bu nesne üzerinde bir JSON.NET `JsonSerializerSettings` bağımsız değişkenleri serileştirilmesi yapılandırmak ve dönüş değerleri için kullanılan nesne.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b8f7e-110">Bkz: [JSON.NET belgeleri](https://www.newtonsoft.com/json/help/html/Introduction.htm) daha fazla ayrıntı için.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="b8f7e-111">Örneğin, "PascalCase" özellik adları, varsayılan "camelCase" adları yerine kullanılacak serileştirici yapılandırmak için aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver = 
        new DefaultContractResolver();
    });
```

<span data-ttu-id="b8f7e-112">.NET istemci, aynı `AddJsonProtocol` genişletme yöntemi var. [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b8f7e-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b8f7e-113">`Microsoft.Extensions.DependencyInjection` Ad alanı içe, genişletme yönteminin çözmek için:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver = 
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="b8f7e-114">JSON seri hale getirme, şu anda JavaScript istemci yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b8f7e-115">MessagePack seri hale getirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="b8f7e-115">MessagePack serialization options</span></span>

<span data-ttu-id="b8f7e-116">MessagePack serileştirme için temsilci sağlayarak yapılandırılabilir [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağırın.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b8f7e-117">Bkz: [signalr'da MessagePack](xref:signalr/messagepackhubprotocol) daha fazla ayrıntı için.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b8f7e-118">MessagePack serileştirme JavaScript istemci şu anda yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b8f7e-119">Sunucu seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b8f7e-119">Configure server options</span></span>

<span data-ttu-id="b8f7e-120">Aşağıdaki tabloda, SignalR hub'ları yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-120">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b8f7e-121">Seçenek</span><span class="sxs-lookup"><span data-stu-id="b8f7e-121">Option</span></span> | <span data-ttu-id="b8f7e-122">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="b8f7e-122">Default Value</span></span> | <span data-ttu-id="b8f7e-123">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b8f7e-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b8f7e-124">30 saniye</span><span class="sxs-lookup"><span data-stu-id="b8f7e-124">30 seconds</span></span> | <span data-ttu-id="b8f7e-125">Sunucunun istemci dikkate alacaktır (tutma dahil) bir ileti bu aralıkta yer aldığı edilmemiş olursa bağlantısı kesildi.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b8f7e-126">Aslında, nasıl bu uygulandığını nedeniyle, bağlantısız işaretlenmesini istemcisi için bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b8f7e-127">Önerilen değer çift `KeepAliveInterval` değeri.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b8f7e-128">15 saniye</span><span class="sxs-lookup"><span data-stu-id="b8f7e-128">15 seconds</span></span> | <span data-ttu-id="b8f7e-129">İstemci, bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermediği durumlarda bağlantı kapalı.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b8f7e-130">Bu zaman aşımı hataları el sıkışması ciddi ağ gecikmesi nedeniyle gerçekleşiyor, yalnızca değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b8f7e-131">Anlaşma işlemi hakkında daha fazla ayrıntı için [SignalR hub'ı protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b8f7e-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b8f7e-132">15 saniye</span><span class="sxs-lookup"><span data-stu-id="b8f7e-132">15 seconds</span></span> | <span data-ttu-id="b8f7e-133">Sunucu bu aralıkta bir ileti gönderdi taşınmadığından, PING iletisi bağlantıyı açık tutma için otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b8f7e-134">Değiştirilirken `KeepAliveInterval`, değiştirme `ServerTimeout` / `serverTimeoutInMilliseconds` istemcide ayarlama.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b8f7e-135">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değeri çift `KeepAliveInterval` değeri.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b8f7e-136">Yüklü tüm protokolleri</span><span class="sxs-lookup"><span data-stu-id="b8f7e-136">All installed protocols</span></span> | <span data-ttu-id="b8f7e-137">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-137">Protocols supported by this hub.</span></span> <span data-ttu-id="b8f7e-138">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak belirli protokoller için tek tek hub'ları devre dışı bırakmak için bu listedeki protokolleri kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b8f7e-139">Varsa `true`, ayrıntılı bir Hub yöntemi bir özel durum oluştuğunda, özel durum iletileri istemcilere döndürülür.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b8f7e-140">Varsayılan `false`gibi bu özel durum iletileri, hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="b8f7e-141">Seçenekler yapılandırılabilir tüm hub'ları için bir seçenekler temsilciye sağlayarak `AddSignalR` Çağır `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-141">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="b8f7e-142">Tek bir hub seçeneklerini geçersiz kılmak, sağlanan genel seçenekleri `AddSignalR` ve kullanılarak yapılandırılabilir [AddHubOptions\<T >](/dotnet/api/microsoft.extensions.dependencyinjection.huboptionsdependencyinjectionextensions.addhuboptions):</span><span class="sxs-lookup"><span data-stu-id="b8f7e-142">Options for a single hub override the global options provided in `AddSignalR` and can be configured using [AddHubOptions\<T>](/dotnet/api/microsoft.extensions.dependencyinjection.huboptionsdependencyinjectionextensions.addhuboptions):</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b8f7e-143">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="b8f7e-143">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b8f7e-144">Kullanım `HttpConnectionDispatcherOptions` aktarımları ve bellek arabellek yönetimi ile ilgili gelişmiş ayarları yapılandırmak için.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-144">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b8f7e-145">Bu seçenekler bir temsilciye geçirerek yapılandırılan [MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) içinde `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-145">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) => 
    {
        var desiredTransports = 
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) => 
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="b8f7e-146">Aşağıdaki tabloda, ASP.NET Core SignalR Gelişmiş HTTP OPTIONS yapılandırmak için seçenekleri tanımlar:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-146">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b8f7e-147">Seçenek</span><span class="sxs-lookup"><span data-stu-id="b8f7e-147">Option</span></span> | <span data-ttu-id="b8f7e-148">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="b8f7e-148">Default Value</span></span> | <span data-ttu-id="b8f7e-149">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b8f7e-149">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b8f7e-150">32 KB</span><span class="sxs-lookup"><span data-stu-id="b8f7e-150">32 KB</span></span> | <span data-ttu-id="b8f7e-151">İstemciden alınan bayt sayısı, sunucu arabellekleri.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-151">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="b8f7e-152">Bu değeri artırmak, daha büyük iletileri almasına olanak sağlar, ancak bellek tüketimi olumsuz yönde etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-152">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b8f7e-153">Verileri otomatik olarak toplanan `Authorize` Hub sınıfına uygulanan öznitelikleri.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-153">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b8f7e-154">Listesini [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) bir istemciye hub'a bağlanmak için yetki verilip verilmediğini belirlemek için kullanılan nesne.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-154">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b8f7e-155">32 KB</span><span class="sxs-lookup"><span data-stu-id="b8f7e-155">32 KB</span></span> | <span data-ttu-id="b8f7e-156">Uygulama tarafından gönderilen bayt sayısı, sunucu arabellekleri.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-156">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="b8f7e-157">Bu değeri artırmak daha büyük iletiler göndermek sunucuyı sağlar, ancak bellek tüketimi olumsuz yönde etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-157">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b8f7e-158">Tüm aktarımları etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-158">All Transports are enabled.</span></span> | <span data-ttu-id="b8f7e-159">Bir bit maskesi, `HttpTransportType` taşımalar kısıtlayabilirsiniz değerleri bir istemci bağlanmak için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-159">A bitmask of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b8f7e-160">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-160">See below.</span></span> | <span data-ttu-id="b8f7e-161">Uzun yoklama taşıma imkanı kullanılarak belirli ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-161">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b8f7e-162">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-162">See below.</span></span> | <span data-ttu-id="b8f7e-163">Ek seçenekler belirli WebSockets taşıma imkanı.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-163">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="b8f7e-164">Uzun yoklama taşıma kullanılarak yapılandırılabilir ek seçenekler sahip `LongPolling` özelliği:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-164">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b8f7e-165">Seçenek</span><span class="sxs-lookup"><span data-stu-id="b8f7e-165">Option</span></span> | <span data-ttu-id="b8f7e-166">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="b8f7e-166">Default Value</span></span> | <span data-ttu-id="b8f7e-167">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b8f7e-167">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b8f7e-168">90 saniye</span><span class="sxs-lookup"><span data-stu-id="b8f7e-168">90 seconds</span></span> | <span data-ttu-id="b8f7e-169">En uzun süreyi sunucunun tek yoklama isteği sonlandırmadan önce istemciye gönderilecek bir ileti bekler.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-169">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b8f7e-170">Bu değeri azaltmak daha sık verme yeni bir yoklama isteği göndermek istemci neden olur.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-170">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b8f7e-171">WebSocket taşıma kullanılarak yapılandırılabilir ek seçenekler sahip `WebSockets` özelliği:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-171">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b8f7e-172">Seçenek</span><span class="sxs-lookup"><span data-stu-id="b8f7e-172">Option</span></span> | <span data-ttu-id="b8f7e-173">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="b8f7e-173">Default Value</span></span> | <span data-ttu-id="b8f7e-174">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b8f7e-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b8f7e-175">5 saniye</span><span class="sxs-lookup"><span data-stu-id="b8f7e-175">5 seconds</span></span> | <span data-ttu-id="b8f7e-176">Bu zaman aralığı içinde kapatmak istemci başarısız olursa sunucunun kapatıldıktan sonra bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-176">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b8f7e-177">Ayarlamak için kullanılan bir temsilci `Sec-WebSocket-Protocol` üst bilgisi için özel bir değer.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-177">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b8f7e-178">Temsilci, giriş olarak istemci tarafından istenen değerleri alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-178">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b8f7e-179">İstemci seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="b8f7e-179">Configure client options</span></span>

<span data-ttu-id="b8f7e-180">İstemci seçenekleri yapılandırılabilir `HubConnectionBuilder` türü (.NET ve JavaScript istemcilerden kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="b8f7e-180">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b8f7e-181">Ayrıca Java istemcisinde kullanılabilir ancak `HttpHubConnectionBuilder` Oluşturucusu yapılandırma seçeneklerini de itibariyle ne içerdiğini sınıfıdır `HubConnection` kendisi.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-181">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b8f7e-182">Günlük tutmayı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b8f7e-182">Configure logging</span></span>

<span data-ttu-id="b8f7e-183">Günlük kaydı kullanarak .NET istemci yapılandırılmış `ConfigureLogging` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-183">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b8f7e-184">Günlüğe kaydetme sağlayıcıları ve filtreleri sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-184">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b8f7e-185">Bkz: [ASP.NET Core günlüğü](xref:fundamentals/logging/index) daha fazla bilgi için belgelere bakın.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-185">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b8f7e-186">Günlüğe kaydetme sağlayıcıları kaydetmek için gerekli paketleri yüklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-186">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b8f7e-187">Bkz: [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) tam listesi için belgelere bölümü.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-187">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b8f7e-188">Örneğin, konsol günlüğe kaydetmeyi etkinleştirmek için yükleme `Microsoft.Extensions.Logging.Console` NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-188">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b8f7e-189">Çağrı `AddConsole` genişletme yöntemi:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-189">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b8f7e-190">Benzer bir JavaScript istemci `configureLogging` yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-190">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b8f7e-191">Sağlayan bir `LogLevel` günlük iletilerini oluşturmak için en düşük düzeyini belirten değer.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-191">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b8f7e-192">Tarayıcı konsol penceresine günlüklerine yazılır.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-192">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b8f7e-193">Tamamen günlüğünü devre dışı bırakmanız belirtin `signalR.LogLevel.None` içinde `configureLogging` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-193">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b8f7e-194">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [SignalR tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b8f7e-194">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b8f7e-195">SignalR Java istemcinin kullandığı [SLF4J](https://www.slf4j.org/) günlüğe kaydetme için kitaplığı.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-195">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b8f7e-196">Seçtiğiniz kendi özel günlük uygulama özel günlük bağımlılık olarak getirerek kullanıcıların Kitaplığı'nın izin veren bir üst düzey günlüğe kaydetme API'si var.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-196">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b8f7e-197">Aşağıdaki kod parçacığını nasıl kullanılacağını gösterir `java.util.logging` SignalR Java istemcisi ile.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-197">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b8f7e-198">Bağımlılıklarınızı içinde günlüğü yapılandırmazsanız, aşağıdaki uyarı iletisi ile bir varsayılan yok-işlem günlükçü SLF4J yükler:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-198">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b8f7e-199">Bu güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-199">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b8f7e-200">İzin verilen taşımalar yapılandırın</span><span class="sxs-lookup"><span data-stu-id="b8f7e-200">Configure allowed transports</span></span>

<span data-ttu-id="b8f7e-201">SignalR tarafından kullanılan taşımalar yapılandırılabilir `WithUrl` çağırın (`withUrl` JavaScript dilinde).</span><span class="sxs-lookup"><span data-stu-id="b8f7e-201">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b8f7e-202">Bir bit düzeyinde OR değerlerinin `HttpTransportType` istemciye yalnızca belirtilen taşımalar kullanmasını kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-202">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b8f7e-203">Tüm aktarımları varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-203">All transports are enabled by default.</span></span>

<span data-ttu-id="b8f7e-204">Örneğin, Server-Sent olayları taşıma devre dışı bırakmak için ancak WebSockets ve uzun yoklama bağlantılarına izin ver:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-204">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b8f7e-205">JavaScript istemci taşımaları ayarlayarak yapılandırılmış `transport` için sağlanan seçenekleri nesne ile sekmesindeki `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-205">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b8f7e-206">Bu Java sürümünde istemci websockets'i yalnızca Aktarım ' dir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-206">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="b8f7e-207">Java istemci ile taşıma seçili `withTransport` metodunda `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-207">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="b8f7e-208">Java istemci WebSockets aktarım varsayılan olarak.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-208">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```
> [!NOTE]
> <span data-ttu-id="b8f7e-209">SignalR Java istemci taşıma geri dönüş henüz desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-209">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b8f7e-210">Taşıyıcı kimlik doğrulaması yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b8f7e-210">Configure bearer authentication</span></span>

<span data-ttu-id="b8f7e-211">SignalR istekleri ile birlikte kimlik doğrulama verilerini sağlamak için kullanmayı `AccessTokenProvider` seçeneği (`accessTokenFactory` javascript'teki) istenen erişim belirteci döndüren bir işlev belirtmek için.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-211">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b8f7e-212">.NET istemci, bu erişim belirteci bir HTTP "Taşıyıcı kimlik doğrulaması" geçirilen belirteç (kullanma `Authorization` başlığı türünde `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="b8f7e-212">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b8f7e-213">JavaScript istemci erişim belirtecini bir taşıyıcı belirteç kullanılan **dışında** tarayıcı API'leri kısıtlama (özellikle de Server-Sent olayları ve WebSockets istekleri) üst bilgileri uygulama özelliği burada birkaç durumda.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-213">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b8f7e-214">Bu durumlarda, erişim belirtecini bir sorgu dizesi değeri sağlanan `access_token`.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-214">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b8f7e-215">.NET istemci `AccessTokenProvider` seçenekleri Temsilcide kullanılarak seçeneği belirtilebilir `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-215">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b8f7e-216">JavaScript istemcisinde ayarlayarak erişim belirteci yapılandırılmıştır `accessTokenFactory` seçenekleri nesnesinde ile sekmesindeki `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-216">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```


<span data-ttu-id="b8f7e-217">SignalR Java istemci, bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabileceğiniz [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b8f7e-217">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b8f7e-218">Kullanım [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) sağlamak için bir [RxJava](https://github.com/ReactiveX/RxJava) [tek<String>](http://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="b8f7e-218">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single<String>](http://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b8f7e-219">Çağrısıyla [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), istemciniz için erişim belirteci üretmek için mantığı yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-219">With a call to [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b8f7e-220">Zaman aşımı ve tutma seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="b8f7e-220">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b8f7e-221">Zaman aşımı ve tutma davranışını yapılandırmak için ek seçenekler bulunur `HubConnection` nesnenin kendisini:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-221">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="b8f7e-222">.NET</span><span class="sxs-lookup"><span data-stu-id="b8f7e-222">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b8f7e-223">Seçenek</span><span class="sxs-lookup"><span data-stu-id="b8f7e-223">Option</span></span> | <span data-ttu-id="b8f7e-224">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="b8f7e-224">Default value</span></span> | <span data-ttu-id="b8f7e-225">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b8f7e-225">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b8f7e-226">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="b8f7e-226">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b8f7e-227">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-227">Timeout for server activity.</span></span> <span data-ttu-id="b8f7e-228">Sunucu bir ileti bu aralık içinde gönderilebilen taşınmadığından, istemci sunucusu bağlantısı kesildi ve Tetikleyicileri dikkate `Closed` olay (`onclose` JavaScript dilinde).</span><span class="sxs-lookup"><span data-stu-id="b8f7e-228">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b8f7e-229">Bu değer sunucudan gönderilecek PING iletisi için yeterince büyük **ve** zaman aşımı aralığı içinde istemci tarafından alındı.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-229">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b8f7e-230">Önerilen değer: bir sayının en az iki sunucu `KeepAliveInterval` ping gelmesi için zaman tanınması için değer.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-230">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b8f7e-231">15 saniye</span><span class="sxs-lookup"><span data-stu-id="b8f7e-231">15 seconds</span></span> | <span data-ttu-id="b8f7e-232">İlk sunucu el sıkışma için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-232">Timeout for initial server handshake.</span></span> <span data-ttu-id="b8f7e-233">Sunucu bu aralığı el sıkışması yanıt göndermediği durumlarda, istemciyi el sıkışması ve tetikleyiciler iptal `Closed` olay (`onclose` JavaScript dilinde).</span><span class="sxs-lookup"><span data-stu-id="b8f7e-233">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b8f7e-234">Bu zaman aşımı hataları el sıkışması ciddi ağ gecikmesi nedeniyle gerçekleşiyor, yalnızca değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-234">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b8f7e-235">Anlaşma işlemi hakkında daha fazla ayrıntı için [SignalR hub'ı protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b8f7e-235">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="b8f7e-236">.NET istemci zaman aşımı değerlerini olarak belirtilen `TimeSpan` değerleri.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-236">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="b8f7e-237">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b8f7e-237">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b8f7e-238">Seçenek</span><span class="sxs-lookup"><span data-stu-id="b8f7e-238">Option</span></span> | <span data-ttu-id="b8f7e-239">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="b8f7e-239">Default value</span></span> | <span data-ttu-id="b8f7e-240">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b8f7e-240">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b8f7e-241">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="b8f7e-241">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b8f7e-242">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-242">Timeout for server activity.</span></span> <span data-ttu-id="b8f7e-243">Sunucu bir ileti bu aralık içinde gönderilebilen taşınmadığından, istemci sunucusu bağlantısı kesildi ve Tetikleyicileri dikkate `onclose` olay.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-243">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b8f7e-244">Bu değer sunucudan gönderilecek PING iletisi için yeterince büyük **ve** zaman aşımı aralığı içinde istemci tarafından alındı.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-244">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b8f7e-245">Önerilen değer: bir sayının en az iki sunucu `KeepAliveInterval` ping gelmesi için zaman tanınması için değer.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-245">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="b8f7e-246">Java</span><span class="sxs-lookup"><span data-stu-id="b8f7e-246">Java</span></span>](#tab/java)

| <span data-ttu-id="b8f7e-247">Seçenek</span><span class="sxs-lookup"><span data-stu-id="b8f7e-247">Option</span></span> | <span data-ttu-id="b8f7e-248">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="b8f7e-248">Default value</span></span> | <span data-ttu-id="b8f7e-249">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b8f7e-249">Description</span></span> |
| ----------- | ------------- | ----------- |
|<span data-ttu-id="b8f7e-250">`getServerTimeout``setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b8f7e-250">`getServerTimeout` `setServerTimeout`</span></span> | <span data-ttu-id="b8f7e-251">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="b8f7e-251">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b8f7e-252">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-252">Timeout for server activity.</span></span> <span data-ttu-id="b8f7e-253">Sunucu bir ileti bu aralık içinde gönderilebilen taşınmadığından, istemci sunucusu bağlantısı kesildi ve Tetikleyicileri dikkate `onClose` olay.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-253">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b8f7e-254">Bu değer sunucudan gönderilecek PING iletisi için yeterince büyük **ve** zaman aşımı aralığı içinde istemci tarafından alındı.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-254">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b8f7e-255">Önerilen değer: bir sayının en az iki sunucu `KeepAliveInterval` ping gelmesi için zaman tanınması için değer.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-255">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b8f7e-256">15 saniye</span><span class="sxs-lookup"><span data-stu-id="b8f7e-256">15 seconds</span></span> | <span data-ttu-id="b8f7e-257">İlk sunucu el sıkışma için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-257">Timeout for initial server handshake.</span></span> <span data-ttu-id="b8f7e-258">Sunucu bu aralığı el sıkışması yanıt göndermediği durumlarda, istemciyi el sıkışması ve tetikleyiciler iptal `onClose` olay.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-258">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b8f7e-259">Bu zaman aşımı hataları el sıkışması ciddi ağ gecikmesi nedeniyle gerçekleşiyor, yalnızca değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-259">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b8f7e-260">Anlaşma işlemi hakkında daha fazla ayrıntı için [SignalR hub'ı protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b8f7e-260">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b8f7e-261">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b8f7e-261">Configure additional options</span></span>

<span data-ttu-id="b8f7e-262">Ek seçenekler yapılandırılabilir `WithUrl` (`withUrl` JavaScript'te) metodunda `HubConnectionBuilder` veya çeşitli yapılandırma API'leri üzerinde `HttpHubConnectionBuilder` Java istemci:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-262">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="b8f7e-263">.NET</span><span class="sxs-lookup"><span data-stu-id="b8f7e-263">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b8f7e-264">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="b8f7e-264">.NET Option</span></span> |  <span data-ttu-id="b8f7e-265">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="b8f7e-265">Default value</span></span> | <span data-ttu-id="b8f7e-266">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b8f7e-266">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b8f7e-267">HTTP isteklerinde bir taşıyıcı kimlik doğrulaması belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-267">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b8f7e-268">Bu ayar `true` anlaşma adımı atlamak için.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-268">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b8f7e-269">**WebSockets aktarım yalnızca etkin aktarım olduğunda yalnızca desteklenen**.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-269">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b8f7e-270">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-270">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b8f7e-271">boş</span><span class="sxs-lookup"><span data-stu-id="b8f7e-271">Empty</span></span> | <span data-ttu-id="b8f7e-272">TLS sertifikalarını isteklerinde kimlik doğrulamak üzere göndermek için bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-272">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b8f7e-273">boş</span><span class="sxs-lookup"><span data-stu-id="b8f7e-273">Empty</span></span> | <span data-ttu-id="b8f7e-274">Her HTTP isteği göndermek için HTTP tanımlama bilgileri koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-274">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b8f7e-275">boş</span><span class="sxs-lookup"><span data-stu-id="b8f7e-275">Empty</span></span> | <span data-ttu-id="b8f7e-276">Her HTTP isteği göndermek için kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-276">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b8f7e-277">5 saniye</span><span class="sxs-lookup"><span data-stu-id="b8f7e-277">5 seconds</span></span> | <span data-ttu-id="b8f7e-278">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-278">WebSockets only.</span></span> <span data-ttu-id="b8f7e-279">En uzun süreyi sunucusunun Kapat isteği onaylamak Kapanıştan sonra istemci bekler.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-279">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b8f7e-280">Bu süre içinde sunucu kapatma bildirimi değil, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-280">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b8f7e-281">boş</span><span class="sxs-lookup"><span data-stu-id="b8f7e-281">Empty</span></span> | <span data-ttu-id="b8f7e-282">Her HTTP isteği göndermek için ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-282">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b8f7e-283">Yapılandırma veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` HTTP istekleri göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-283">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b8f7e-284">WebSocket bağlantılarını için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-284">Not used for WebSocket connections.</span></span> <span data-ttu-id="b8f7e-285">Bu temsilci, bir null olmayan değer döndürmelidir ve varsayılan değer bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-285">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b8f7e-286">Bu varsayılan ayarları değiştirmek ve onu döndürür ya da yeni bir dönüş `HttpMessageHandler` örneği.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-286">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b8f7e-287">**Aksi takdirde işleyici değiştirerek yaptığınızda, sağlanan işleyicisinden tutmak istediğiniz ayarları kopyaladığınızdan emin (örneğin, tanımlama bilgileri ve üst) yapılandırılmış seçenekler için yeni işleyici uygulanmayacak.**</span><span class="sxs-lookup"><span data-stu-id="b8f7e-287">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b8f7e-288">HTTP istekleri gönderirken HTTP proxy.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-288">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b8f7e-289">HTTP ve Websocket'istekleri için varsayılan kimlik bilgilerini göndermek için bu boolean ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-289">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b8f7e-290">Bu, Windows kimlik doğrulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-290">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b8f7e-291">Ek WebSocket seçeneklerini yapılandırmak için kullanılan bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-291">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b8f7e-292">Örneğini alır [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-292">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="b8f7e-293">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b8f7e-293">JavaScript</span></span>](#tab/javascript)
| <span data-ttu-id="b8f7e-294">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="b8f7e-294">JavaScript Option</span></span> | <span data-ttu-id="b8f7e-295">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="b8f7e-295">Default Value</span></span> | <span data-ttu-id="b8f7e-296">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b8f7e-296">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b8f7e-297">HTTP isteklerinde bir taşıyıcı kimlik doğrulaması belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-297">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b8f7e-298">Bu ayar `true` anlaşma adımı atlamak için.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-298">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b8f7e-299">**WebSockets aktarım yalnızca etkin aktarım olduğunda yalnızca desteklenen**.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-299">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b8f7e-300">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-300">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="b8f7e-301">Java</span><span class="sxs-lookup"><span data-stu-id="b8f7e-301">Java</span></span>](#tab/java)
| <span data-ttu-id="b8f7e-302">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="b8f7e-302">Java Option</span></span> | <span data-ttu-id="b8f7e-303">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="b8f7e-303">Default Value</span></span> | <span data-ttu-id="b8f7e-304">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b8f7e-304">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b8f7e-305">HTTP isteklerinde bir taşıyıcı kimlik doğrulaması belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-305">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b8f7e-306">Bu ayar `true` anlaşma adımı atlamak için.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-306">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b8f7e-307">**WebSockets aktarım yalnızca etkin aktarım olduğunda yalnızca desteklenen**.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-307">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b8f7e-308">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-308">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b8f7e-309">`withHeader``withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b8f7e-309">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b8f7e-310">boş</span><span class="sxs-lookup"><span data-stu-id="b8f7e-310">Empty</span></span> | <span data-ttu-id="b8f7e-311">Her HTTP isteği göndermek için ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="b8f7e-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b8f7e-312">.NET istemci, bu seçenekler için sağlanan seçenekleri temsilci tarafından değiştirilebilir `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-312">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b8f7e-313">JavaScript istemci, bu seçenekler için sağlanan bir JavaScript nesnesi içinde sağlanabilir `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="b8f7e-313">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b8f7e-314">Java istemci, bu seçenekler yöntemleriyle yapılandırılabilir `HttpHubConnectionBuilder` döndürüldüğü `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b8f7e-314">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>


```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b8f7e-315">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b8f7e-315">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
