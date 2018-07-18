---
title: SignalR HubContext
author: tdykstra
description: Bir hub dışında istemcilere bildirimleri gönderme için ASP.NET Core SignalR HubContext hizmetini kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/13/2018
uid: signalr/hubcontext
ms.openlocfilehash: 6b955c2064d7d6a045594e56326e2f7df282675f
ms.sourcegitcommit: 3ca527f27c88cfc9d04688db5499e372fbc2c775
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39095313"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="de733-103">Bir hub dışında ileti gönderme</span><span class="sxs-lookup"><span data-stu-id="de733-103">Send messages from outside a hub</span></span>

<span data-ttu-id="de733-104">Tarafından [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="de733-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="de733-105">SignalR hub'ı, SignalR sunucuya bağlı istemcilere ileti göndermek için çekirdek soyutlamadır.</span><span class="sxs-lookup"><span data-stu-id="de733-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="de733-106">Diğer yerlerden uygulama kullanarak ileti göndermek mümkündür `IHubContext` hizmeti.</span><span class="sxs-lookup"><span data-stu-id="de733-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="de733-107">Bu makalede, bir SignalR erişmeye açıklanmaktadır `IHubContext` hub dışında istemcilere bildirimleri göndermek için.</span><span class="sxs-lookup"><span data-stu-id="de733-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="de733-108">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(karşıdan yükleme)](xref:tutorials/index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="de733-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:tutorials/index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="de733-109">Örneği Al `IHubContext`</span><span class="sxs-lookup"><span data-stu-id="de733-109">Get an instance of `IHubContext`</span></span>

<span data-ttu-id="de733-110">ASP.NET Core SignalR öğesinde bir örneğini erişebileceğiniz `IHubContext` aracılığıyla bağımlılık ekleme.</span><span class="sxs-lookup"><span data-stu-id="de733-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="de733-111">Örneği ekleyebilir `IHubContext` bir denetleyici, ara yazılım veya diğer DI hizmeti.</span><span class="sxs-lookup"><span data-stu-id="de733-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="de733-112">İstemcilere göndermek için örneği kullanın.</span><span class="sxs-lookup"><span data-stu-id="de733-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="de733-113">Bu GlobalHost erişim sağlamak için kullanılan ASP.NET SignalR farklıdır `IHubContext`.</span><span class="sxs-lookup"><span data-stu-id="de733-113">This differs from ASP.NET SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="de733-114">ASP.NET Core, bu genel tekil gereksinimini ortadan kaldırır, bir bağımlılık ekleme çerçeve vardır.</span><span class="sxs-lookup"><span data-stu-id="de733-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="de733-115">Bir örneğini ekleme `IHubContext` bir denetleyicide</span><span class="sxs-lookup"><span data-stu-id="de733-115">Inject an instance of `IHubContext` in a controller</span></span>

<span data-ttu-id="de733-116">Örneği ekleyebilir `IHubContext` , oluşturucuya ekleyerek bir denetleyici içinde:</span><span class="sxs-lookup"><span data-stu-id="de733-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="de733-117">Şimdi, örneğine erişimi olan `IHubContext`, hub içinde değilmiş gibi hub yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="de733-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="de733-118">Bir kopyasını almak `IHubContext` Ara yazılımında</span><span class="sxs-lookup"><span data-stu-id="de733-118">Get an instance of `IHubContext` in middleware</span></span>

<span data-ttu-id="de733-119">Erişim `IHubContext` ara yazılım ardışık düzenini içinde şu şekilde:</span><span class="sxs-lookup"><span data-stu-id="de733-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(next => (context) =>
{
    var hubContext = (IHubContext<MyHub>)context
                        .RequestServices
                        .GetServices<IHubContext<MyHub>>();
    //...
});
```

> [!NOTE]
> <span data-ttu-id="de733-120">Ne zaman hub yöntemleri çağrıldığında gelen dışında `Hub` çağırmayla ilgili hiçbir arayan olduğunda, sınıf.</span><span class="sxs-lookup"><span data-stu-id="de733-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="de733-121">Bu nedenle, erişim yoktur `ConnectionId`, `Caller`, ve `Others` özellikleri.</span><span class="sxs-lookup"><span data-stu-id="de733-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

## <a name="related-resources"></a><span data-ttu-id="de733-122">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="de733-122">Related resources</span></span>

* [<span data-ttu-id="de733-123">Kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="de733-123">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="de733-124">Merkezler</span><span class="sxs-lookup"><span data-stu-id="de733-124">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="de733-125">Azure'a Yayımlama</span><span class="sxs-lookup"><span data-stu-id="de733-125">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
