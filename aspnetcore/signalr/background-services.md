---
title: SignalRArka plan hizmetlerinde ana bilgisayar ASP.NET Core
author: bradygaster
description: SignalR.NET Core BackgroundService sınıflarından istemcilere ileti gönderme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: 7bc3b9535055e3fccf23ffa4638bd29676910348
ms.sourcegitcommit: e87dfa08fec0be1008249b1be678e5f79dcc5acb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382575"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>SignalRArka plan hizmetlerinde ana bilgisayar ASP.NET Core

, [Brady Gaster](https://twitter.com/bradygaster) tarafından

Bu makalede şu yönergelere kılavuzluk sunulmaktadır:

* SignalRASP.NET Core ile barındırılan bir arka plan çalışan işlemi kullanarak hub 'ları barındırma.
* .NET Core [Backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService)içinden bağlı istemcilere ileti gönderme.

::: moniker range=">= aspnetcore-3.0"

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)

::: moniker-end

## <a name="enable-signalr-in-startup"></a>SignalRBaşlangıçta etkinleştir

::: moniker range=">= aspnetcore-3.0"

SignalRArka plan çalışan işleminin bağlamında ASP.NET Core hub 'ların barındırılması, bir ASP.NET Core Web uygulamasında hub barındırmakla aynıdır. `Startup.ConfigureServices`Yöntemi, çağırma, `services.AddSignalR` gerekli hizmetleri desteklemek Için ASP.NET Core bağımlılık ekleme (dı) katmanına ekler SignalR . `Startup.Configure`' De, `MapHub` yöntemi, `UseEndpoints` ASP.NET Core Isteği ardışık düzeninde hub uç noktalarına bağlanmak için geri çağırmada çağrılır.

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

SignalRArka plan çalışan işleminin bağlamında ASP.NET Core hub 'ların barındırılması, bir ASP.NET Core Web uygulamasında hub barındırmakla aynıdır. `Startup.ConfigureServices`Yöntemi, çağırma, `services.AddSignalR` gerekli hizmetleri desteklemek Için ASP.NET Core bağımlılık ekleme (dı) katmanına ekler SignalR . `Startup.Configure`' De, `UseSignalR` yöntemi, ASP.NET Core isteği ardışık düzeninde hub uç noktaları bağlamak için çağrılır.

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

Önceki örnekte, `ClockHub` sınıfı `Hub<T>` türü kesin belirlenmiş bir hub oluşturmak için sınıfı uygular. , `ClockHub` `Startup` Sınıfında, uç noktasındaki isteklere yanıt vermek için yapılandırılmıştır `/hubs/clock` .

Türü kesin belirlenmiş hub 'Lar hakkında daha fazla bilgi için bkz. [ SignalR ASP.NET Core için hub 'ları kullanma](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Bu işlevsellik, [hub \< t>](xref:Microsoft.AspNetCore.SignalR.Hub`1) sınıfıyla sınırlı değildir. [Dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)gibi [hub](xref:Microsoft.AspNetCore.SignalR.Hub)'dan devralan herhangi bir sınıf, işe yarar.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

Kesin olarak yazılan tarafından kullanılan arabirim `ClockHub` `IClock` arabirimidir.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a>SignalRArka plan hizmetinden hub çağırma

Başlangıç sırasında, `Worker` a sınıfı `BackgroundService` kullanılarak etkindir `AddHostedService` .

```csharp
services.AddHostedService<Worker>();
```

SignalRAyrıca `Startup` , her hub 'ıN ASP.NET Core http isteği ardışık düzeninde tek bir uç noktaya eklendiği için, her hub, sunucu üzerinde bir ile temsil edilir `IHubContext<T>` . ASP.NET Core dı özelliklerini kullanarak, `BackgroundService` sınıflar, MVC denetleyici sınıfları veya sayfa modelleri gibi barındırma katmanı tarafından oluşturulan diğer sınıflar, Razor oluşturma sırasında örneklerini kabul ederek sunucu tarafı hub 'larına başvurular alabilir `IHubContext<ClockHub, IClock>` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

`ExecuteAsync`Yöntemi arka plan hizmetinde yinelemeli olarak çağrıldığı için sunucunun geçerli tarih ve saati kullanılarak bağlı istemcilere gönderilir `ClockHub` .

## <a name="react-to-signalr-events-with-background-services"></a>SignalRArka plan hizmetleriyle olaylara tepki verme

JavaScript istemcisi veya bir .NET masaüstü uygulaması kullanan tek sayfalı bir uygulama gibi kullanarak, SignalR <xref:signalr/dotnet-client> `BackgroundService` `IHostedService` SignalR hub 'lara bağlanmak ve olaylara yanıt vermek için bir veya uygulaması da kullanılabilir.

`ClockHubClient`Sınıfı hem arabirimini hem de `IClock` `IHostedService` arabirimini uygular. Bu sayede `Startup` , sürekli olarak çalıştırılmak ve sunucudan hub olaylarına yanıt vermek için etkinleştirilebilir.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Başlatma sırasında, `ClockHubClient` bir örneğini oluşturur `HubConnection` ve `IClock.ShowTime` hub 'ın olayının işleyicisi olarak yöntemi sunar `ShowTime` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

Uygulamada, `IHostedService.StartAsync` `HubConnection` zaman uyumsuz olarak başlatılır.

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Yöntemi sırasında, `IHostedService.StopAsync` `HubConnection` zaman uyumsuz olarak bırakıldı.

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

Uygulamada, `IHostedService.StartAsync` `HubConnection` zaman uyumsuz olarak başlatılır.

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Yöntemi sırasında, `IHostedService.StopAsync` `HubConnection` zaman uyumsuz olarak bırakıldı.

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [Kullanmaya başlayın](xref:tutorials/signalr)
* [Merkezler](xref:signalr/hubs)
* [Azure’da Yayımlama](xref:signalr/publish-to-azure-web-app)
* [Türü kesin belirlenmiş hub 'Lar](xref:signalr/hubs#strongly-typed-hubs)
