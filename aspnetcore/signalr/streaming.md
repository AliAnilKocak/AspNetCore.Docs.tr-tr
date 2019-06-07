---
title: ASP.NET Core SignalR öğesinde akışı
author: bradygaster
description: İstemci ve sunucu arasında veri akışı yapmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/05/2019
uid: signalr/streaming
ms.openlocfilehash: a75156f398e113393ddb891d16eec3f09de80c09
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750194"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a>ASP.NET Core SignalR öğesinde akışı

Tarafından [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR, istemciden sunucuya ve sunucudan istemciye akışını destekler. Bu, burada zaman içinde veri parçalarını geldiğinde senaryoları için kullanışlıdır. Bu duruma hemen sonra akış, her parça istemci veya sunucu için kullanılabilir yerine tüm verileri kullanılabilir hale gelmesi bekleniyor gönderilir.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR sunucusunu yöntemlerin dönüş değerlerini akış destekler. Bu, burada zaman içinde veri parçalarını geldiğinde senaryoları için kullanışlıdır. Dönüş değeri, istemciye akışla, bu duruma hemen sonra her parça için istemci kullanılabilir yerine tüm verileri kullanılabilir hale gelmesi bekleniyor gönderilir.

::: moniker-end

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Akış hub'ı ayarlama

::: moniker range=">= aspnetcore-3.0"

Döndürür bir hub yöntemini otomatik olarak bir akış hub'yöntemini olur <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, veya `Task<ChannelReader<T>>`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bir hub yöntemini döndürür, otomatik olarak bir akış hub'yöntemini olur. bir <xref:System.Threading.Channels.ChannelReader%601> veya `Task<ChannelReader<T>>`.

::: moniker-end

### <a name="server-to-client-streaming"></a>Server istemcisi akış

::: moniker range=">= aspnetcore-3.0"

Hub yöntemlerinde akış döndürebilir `IAsyncEnumerable<T>` ek olarak `ChannelReader<T>`. Döndürülecek en basit yolu `IAsyncEnumerable<T>` aşağıdaki örnekte gösterildiği gibi hub yönteminin zaman uyumsuz bir yineleyici yöntem tarafından sunuluyor. Hub zaman uyumsuz yineleyicinin yöntemler kabul edebileceği bir `CancellationToken` akıştan istemci aboneliği kaldırma tetikleyen parametresi. Zaman uyumsuz yineleyicinin yöntemler döndürmez gibi Kanallar, ortak sorunları önlemek `ChannelReader` kadar erken veya tamamlamadan yönteminden çıkılıyor <xref:System.Threading.Channels.ChannelWriter`1>.

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

Aşağıdaki örnek, akış kanalları kullanarak bir istemciye ilişkin temel bilgileri gösterir. Her bir nesne yazılır <xref:System.Threading.Channels.ChannelWriter%601>, nesneyi hemen istemciye gönderilir. Sonunda, `ChannelWriter` Akış kapalı istemci bildirmek için tamamlandı.

> [!NOTE]
> Yazma `ChannelWriter<T>` arka plan iş parçacığı ve dönüş `ChannelReader` olabildiğince çabuk. Diğer hub çağrılarını kadar engellenen bir `ChannelReader` döndürülür.
>
> Mantık kaydırma bir `try ... catch`. Tamamlamak `Channel` içinde `catch` ve dış `catch` hub emin olmak için yöntem çağırma düzgün bir şekilde tamamlandı.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Server istemcisi akış hub yöntemleri kabul edebileceği bir `CancellationToken` akıştan istemci aboneliği kaldırma tetikleyen parametresi. Sunucu işlemi durdurmak ve akışın sonuna önce istemcinin bağlantısı kesilirse tüm kaynakları serbest bırakmak için bu belirteci kullanın.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>İstemci sunucu akış

Bir hub yöntemini veya daha fazla nesne türü kabul ettiğinde, otomatik olarak bir istemci-sunucu akış hub yöntemini olur <xref:System.Threading.Channels.ChannelReader%601> veya <xref:System.Collections.Generic.IAsyncEnumerable%601>. Aşağıdaki örnek istemci tarafından gönderilen veri akışı okuma temellerini gösterir. Her istemci Yazar <xref:System.Threading.Channels.ChannelWriter%601>, veri yazılır `ChannelReader` hub yönteminin okuma içinden sunucusunda.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

Bir <xref:System.Collections.Generic.IAsyncEnumerable%601> yöntem sürümünü izler.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<Stream> stream) 
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a>.NET istemcisi

### <a name="server-to-client-streaming"></a>Server istemcisi akış


::: moniker range=">= aspnetcore-3.0"

`StreamAsync` Ve `StreamAsChannelAsync` yöntemlerde `HubConnection` server istemcisi akış yöntemlerini çağırmak için kullanılır. Hub yönteminin adı ve bağımsız değişkenler için hub yönteminin tanımlandığı `StreamAsync` veya `StreamAsChannelAsync`. Genel parametre üzerinde `StreamAsync<T>` ve `StreamAsChannelAsync<T>` akış yöntemi tarafından döndürülen nesne türünü belirtir. Bir nesne türü `IAsyncEnumerable<T>` veya `ChannelReader<T>` stream çağrısından döndürülen ve istemcinin akışta temsil eder.

A `StreamAsync` döndüren örnek `IAsyncEnumerable<int>`:

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

Karşılık gelen `StreamAsChannelAsync` döndüren örnek `ChannelReader<int>`:

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

`StreamAsChannelAsync` Metodunda `HubConnection` sunucusu istemci akışı yöntemi çağırmak için kullanılır. Hub yönteminin adı ve bağımsız değişkenler için hub yönteminin tanımlandığı `StreamAsChannelAsync`. Genel parametre üzerinde `StreamAsChannelAsync<T>` akış yöntemi tarafından döndürülen nesne türünü belirtir. A `ChannelReader<T>` stream çağrısından döndürülen ve istemcinin akışta temsil eder.

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`StreamAsChannelAsync` Metodunda `HubConnection` sunucusu istemci akışı yöntemi çağırmak için kullanılır. Hub yönteminin adı ve bağımsız değişkenler için hub yönteminin tanımlandığı `StreamAsChannelAsync`. Genel parametre üzerinde `StreamAsChannelAsync<T>` akış yöntemi tarafından döndürülen nesne türünü belirtir. A `ChannelReader<T>` stream çağrısından döndürülen ve istemcinin akışta temsil eder.

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>İstemci sunucu akış

Bir istemci-sunucu akış hub'yöntemini .NET istemcisinden çağırmak için iki yolu vardır. Ya da geçişinde olabilir bir `IAsyncEnumerable<T>` veya `ChannelReader` bağımsız değişkeni olarak `SendAsync`, `InvokeAsync`, veya `StreamAsChannelAsync`hub yönteminin çağrılması bağlı olarak.

Her veri yazılır `IAsyncEnumerable` veya `ChannelWriter` nesnesi, sunucudaki hub yönteminin istemciden veri içeren yeni bir öğe alır.

Kullanılıyorsa bir `IAsyncEnumerable` nesnesi, akışın akış öğelerini çıkar döndüren yöntem sonra sona erer.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Veya kullanıyorsanız bir `ChannelWriter`, kanalıyla tamamlamak `channel.Writer.Complete()`:

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>JavaScript istemcisi

### <a name="server-to-client-streaming"></a>Server istemcisi akış

JavaScript istemcilerinin hub'larla üzerinde sunucu istemci akış yöntemleri çağırmak `connection.stream`. `stream` Yöntemi iki bağımsız değişkeni kabul eder:

* Hub yönteminin adı. Aşağıdaki örnekte, hub'yöntemini addır `Counter`.
* Hub yönteminin içinde tanımlanan bir bağımsız değişken. Aşağıdaki örnekte, akış öğeleri almaya ve akış öğeleri arasındaki gecikmeyi sayısını sayısını bağımsız değişkenler.

`connection.stream` döndürür bir `IStreamResult`, içeren bir `subscribe` yöntemi. Başarılı bir `IStreamSubscriber` için `subscribe` ve `next`, `error`, ve `complete` bildirimleri almak için geri çağırmaları `stream` çağırma.

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

İstemci akıştan sona erdirmek için çağrı `dispose` metodunda `ISubscription` sağlayıcıdan döndürülen `subscribe` yöntemi. Bu yöntemin çağrılması iptaline neden `CancellationToken` sağlandıysa bir Hub yöntemi parametresi.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

İstemci akıştan sona erdirmek için çağrı `dispose` metodunda `ISubscription` sağlayıcıdan döndürülen `subscribe` yöntemi.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>İstemci sunucu akış

JavaScript istemcilerinin geçirerek hub'larında istemci-sunucu akış yöntemleri çağırmak bir `Subject` bağımsız değişkeni olarak `send`, `invoke`, veya `stream`hub yönteminin çağrılması bağlı olarak. `Subject` Şuna benzer bir sınıf olan bir `Subject`. Örneğin RxJS içinde kullanabilirsiniz [konu](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) bu kitaplıktan sınıfı.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

Çağırma `subject.next(item)` ile bir öğe öğesi akışa yazar ve hub yönteminin sunucuda öğeyi alır.

Akış sonuna çağrı `subject.complete()`.

## <a name="java-client"></a>Java istemcisi

### <a name="server-to-client-streaming"></a>Server istemcisi akış

SignalR Java istemcinin kullandığı `stream` akış yöntemlerini çağırmak için yöntem. `stream` üç veya daha fazla bağımsız değişken kabul eder:

* Akış öğeleri beklenen türü.
* Hub yönteminin adı.
* Hub yönteminin içinde tanımlanan bir bağımsız değişken.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

`stream` Metodunda `HubConnection` Observable akış öğesi türünü döndürür. Gözlemlenebilir türün `subscribe` yöntemdir nerede `onNext`, `onError` ve `onCompleted` işleyicileri tanımlanır.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [Merkezler](xref:signalr/hubs)
* [.NET istemcisi](xref:signalr/dotnet-client)
* [JavaScript istemcisi](xref:signalr/javascript-client)
* [Azure'a Yayımlama](xref:signalr/publish-to-azure-web-app)
