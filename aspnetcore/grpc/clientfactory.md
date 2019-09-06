---
title: .NET Core 'da gRPC istemci fabrikası tümleştirmesi
author: jamesnk
description: İstemci fabrikasını kullanarak gRPC istemcilerini oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/clientfactory
ms.openlocfilehash: a52fd397a7ed3e327938b0847af7f4e6a4a79400
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310648"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>.NET Core 'da gRPC istemci fabrikası tümleştirmesi

ile GRPC tümleştirmesi `HttpClientFactory` , GRPC istemcileri oluşturmaya yönelik merkezi bir yöntem sunar. [Tek başına gRPC istemci örneklerini yapılandırmaya](xref:grpc/client)alternatif olarak kullanılabilir. Fabrika tümleştirmesi, [GRPC .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet paketinde bulunabilir.

Fabrika aşağıdaki avantajları sunar:

* Mantıksal gRPC istemci örneklerini yapılandırmak için merkezi bir konum sağlar
* Temeldeki bir yaşam süresini yönetir`HttpClientMessageHandler`
* ASP.NET Core gRPC hizmetinde son tarih ve iptali otomatik olarak yayma

## <a name="register-grpc-clients"></a>GRPC istemcilerini kaydetme

Bir GRPC istemcisini kaydetmek için, genel `AddGrpcClient` genişletme yöntemi içinde `Startup.ConfigureServices`, GRPC türü belirtilmiş istemci sınıfı ve hizmet adresi belirtilerek kullanılabilir:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("http://localhost:5001");
});
```

GRPC istemci türü, bağımlılık ekleme (dı) ile geçici olarak kaydedilir. İstemci artık, DI tarafından oluşturulan türlere eklenebilir ve doğrudan tüketilebilir. ASP.NET Core MVC denetleyicileri, SignalR hub 'ları ve gRPC Hizmetleri, gRPC istemcilerinin otomatik olarak eklenebilir yer lardır:

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>HttpClient 'ı yapılandırma

`HttpClientFactory`GRPC istemcisi tarafından kullanılan öğesini oluşturur. `HttpClient` Standart `HttpClientFactory` Yöntemler `HttpClientHandler` ,`HttpClient`giden istek ara yazılımı eklemek veya öğesinin temelini yapılandırmak için kullanılabilir:

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.BaseAddress = new Uri("http://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

Daha fazla bilgi için bkz. [ıhttpclientfactory kullanarak http Istekleri oluşturma](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Kanalı ve Yakacıları yapılandırma

gRPC 'ye özgü Yöntemler şu şekilde kullanılabilir:

* Bir gRPC istemcisinin temel kanalını yapılandırın.
* İstemcisinin `Interceptor` GRPC çağrıları yaparken kullanacağı örnekleri ekleyin.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("http://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>Son Tarih ve iptal yayma

bir GRPC hizmetinde fabrika tarafından oluşturulan GRPC istemcileri, son tarih ve iptal belirtecini `EnableCallContextPropagation()` alt çağrılara otomatik olarak yaymak üzere ile yapılandırılabilir. Genişletme yöntemi [GRPC. aspnetcore. Server. clientfactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet paketinde bulunur. `EnableCallContextPropagation()`

Çağrı bağlamı yayma, geçerli gRPC isteği bağlamından son tarih ve iptal belirtecini okuyarak ve bunları otomatik olarak gRPC istemcisi tarafından yapılan giden çağrılara yayarak işe yarar. Çağrı bağlamı yayma, karmaşık, iç içe gRPC senaryolarının her zaman son tarihi ve iptali yaymasını sağlamaya yönelik mükemmel bir yoldur.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("http://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Son tarihler ve RPC iptali hakkında daha fazla bilgi için bkz. [RPC yaşam döngüsü](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
