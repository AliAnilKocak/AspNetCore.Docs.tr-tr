---
title: ASP.NET Core signalr'da hubs'ı kullanma
author: tdykstra
description: İçinde ASP.NET Core SignalR hub'ı kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 09/12/2018
uid: signalr/hubs
ms.openlocfilehash: 27aedc5b2f2060d961070fbd1ff5304eaa3956d1
ms.sourcegitcommit: fc7eb4243188950ae1f1b52669edc007e9d0798d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51225362"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a><span data-ttu-id="b2a62-103">ASP.NET Core signalr'da hubs'ı kullanma</span><span class="sxs-lookup"><span data-stu-id="b2a62-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="b2a62-104">Tarafından [Rachel Appel](https://twitter.com/rachelappel) ve [Kevin Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="b2a62-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="b2a62-105">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(karşıdan yükleme)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b2a62-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-signalr-hub"></a><span data-ttu-id="b2a62-106">Bir SignalR hub'ı nedir</span><span class="sxs-lookup"><span data-stu-id="b2a62-106">What is a SignalR hub</span></span>

<span data-ttu-id="b2a62-107">SignalR hub'ları API sunucudan bağlı istemciler üzerinde yöntemleri çağırmak sağlar.</span><span class="sxs-lookup"><span data-stu-id="b2a62-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="b2a62-108">Sunucu kodunda istemci tarafından çağrılan yöntemlere tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="b2a62-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="b2a62-109">İstemci kodda sunucudan çağrılan yöntemlere tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="b2a62-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="b2a62-110">SignalR gerçek zamanlı istemci-sunucu ve sunucu istemci iletişimi olanaklı kılan arka planda her şeyin üstlenir.</span><span class="sxs-lookup"><span data-stu-id="b2a62-110">SignalR takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-signalr-hubs"></a><span data-ttu-id="b2a62-111">SignalR hub'ları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b2a62-111">Configure SignalR hubs</span></span>

<span data-ttu-id="b2a62-112">SignalR ara yazılım çağırarak yapılandırılan bazı hizmetler gerektirir `services.AddSignalR`.</span><span class="sxs-lookup"><span data-stu-id="b2a62-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

<span data-ttu-id="b2a62-113">ASP.NET Core uygulaması için SignalR işlevselliği ekleme, SignalR yollar çağırarak Kurulum `app.UseSignalR` içinde `Startup.Configure` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="b2a62-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

## <a name="create-and-use-hubs"></a><span data-ttu-id="b2a62-114">Oluşturma ve hub'ları kullanma</span><span class="sxs-lookup"><span data-stu-id="b2a62-114">Create and use hubs</span></span>

<span data-ttu-id="b2a62-115">Öğesinden devralınan bir sınıf bildirerek oluşturma `Hub`ve genel yöntemleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b2a62-115">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="b2a62-116">İstemcileri olarak tanımlanmış olan yöntemleri çağırabilir `public`.</span><span class="sxs-lookup"><span data-stu-id="b2a62-116">Clients can call methods that are defined as `public`.</span></span>

[!code-csharp[Create and use hubs](hubs/sample/hubs/chathub.cs?range=8-37)]

<span data-ttu-id="b2a62-117">Dönüş türü ve parametreleri, tüm C# yönteminde olduğu gibi karmaşık türler ve diziler de dahil olmak üzere belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b2a62-117">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="b2a62-118">SignalR serileştirme ve seri durumundan çıkarma karmaşık nesneler ve diziler parametreleri ve dönüş değerleri işler.</span><span class="sxs-lookup"><span data-stu-id="b2a62-118">SignalR handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="b2a62-119">Hub geçici:</span><span class="sxs-lookup"><span data-stu-id="b2a62-119">Hubs are transient:</span></span>
> * <span data-ttu-id="b2a62-120">Durum hub sınıfına bir özellik içinde depolamayın.</span><span class="sxs-lookup"><span data-stu-id="b2a62-120">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="b2a62-121">Her bir hub yöntemi çağrısı, yeni bir hub örneği üzerinde yürütülür.</span><span class="sxs-lookup"><span data-stu-id="b2a62-121">Every hub method call is executed on a new hub instance.</span></span>  
> * <span data-ttu-id="b2a62-122">Kullanım `await` Canlı kalma hub'da bağımlı zaman uyumsuz yöntemleri çağrılırken.</span><span class="sxs-lookup"><span data-stu-id="b2a62-122">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="b2a62-123">Örneğin, bir yöntem gibi `Clients.All.SendAsync(...)` olmadan çağrılırsa başarısız olabilir `await` ve önce hub yöntemi tamamlayan `SendAsync` tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="b2a62-123">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="b2a62-124">Bağlam nesnesi</span><span class="sxs-lookup"><span data-stu-id="b2a62-124">The Context object</span></span>

<span data-ttu-id="b2a62-125">`Hub` Sınıfında bir `Context` bağlantı hakkında bilgi içeren aşağıdaki özellikleri içeren özellik:</span><span class="sxs-lookup"><span data-stu-id="b2a62-125">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="b2a62-126">Özellik</span><span class="sxs-lookup"><span data-stu-id="b2a62-126">Property</span></span> | <span data-ttu-id="b2a62-127">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b2a62-127">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="b2a62-128">SignalR tarafından atanan bağlantı için benzersiz kimlik alır.</span><span class="sxs-lookup"><span data-stu-id="b2a62-128">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="b2a62-129">Her bağlantı için bir bağlantı kimliği yok.</span><span class="sxs-lookup"><span data-stu-id="b2a62-129">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="b2a62-130">Alır [kullanıcı tanımlayıcısı](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="b2a62-130">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="b2a62-131">Varsayılan olarak, SignalR kullanır `ClaimTypes.NameIdentifier` gelen `ClaimsPrincipal` kullanıcı tanımlayıcısı olarak bağlantı ile ilişkili.</span><span class="sxs-lookup"><span data-stu-id="b2a62-131">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="b2a62-132">Alır `ClaimsPrincipal` geçerli kullanıcı ile ilişkili.</span><span class="sxs-lookup"><span data-stu-id="b2a62-132">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="b2a62-133">Bu bağlantının kapsamı içinde veri paylaşmak için kullanılan bir anahtar/değer koleksiyonunu alır.</span><span class="sxs-lookup"><span data-stu-id="b2a62-133">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="b2a62-134">Bu koleksiyonda veri depolanabilir ve farklı bir hub yöntemi çağrılarını arasında bağlantı için korunur.</span><span class="sxs-lookup"><span data-stu-id="b2a62-134">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="b2a62-135">Bağlantıda özelliklerin koleksiyonunu alır.</span><span class="sxs-lookup"><span data-stu-id="b2a62-135">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="b2a62-136">Ayrıntılı olarak henüz belgelenmemiştir için şu an için çoğu senaryoda, bu koleksiyon gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="b2a62-136">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="b2a62-137">Alır bir `CancellationToken` bağlantı iptal edildiğinde bildirir.</span><span class="sxs-lookup"><span data-stu-id="b2a62-137">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="b2a62-138">`Hub.Context` Ayrıca aşağıdaki yöntemleri içerir:</span><span class="sxs-lookup"><span data-stu-id="b2a62-138">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="b2a62-139">Yöntem</span><span class="sxs-lookup"><span data-stu-id="b2a62-139">Method</span></span> | <span data-ttu-id="b2a62-140">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b2a62-140">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="b2a62-141">Döndürür `HttpContext` bir bağlantı veya `null` bağlantı bir HTTP isteği ile ilişkili değilse.</span><span class="sxs-lookup"><span data-stu-id="b2a62-141">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="b2a62-142">HTTP bağlantıları için HTTP üst bilgileri ve sorgu dizeleri gibi bilgileri almak için bu yöntemi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b2a62-142">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="b2a62-143">Bağlantıyı durdurur.</span><span class="sxs-lookup"><span data-stu-id="b2a62-143">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="b2a62-144">İstemciler nesnesi</span><span class="sxs-lookup"><span data-stu-id="b2a62-144">The Clients object</span></span>

<span data-ttu-id="b2a62-145">`Hub` Sınıfında bir `Clients` sunucu ve istemci arasındaki iletişim için aşağıdaki özellikleri içeren özelliği:</span><span class="sxs-lookup"><span data-stu-id="b2a62-145">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="b2a62-146">Özellik</span><span class="sxs-lookup"><span data-stu-id="b2a62-146">Property</span></span> | <span data-ttu-id="b2a62-147">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b2a62-147">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="b2a62-148">Bağlanan tüm istemciler üzerinde bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="b2a62-148">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="b2a62-149">Bir hub yöntemini çağırmış istemciye bir metod çağırır</span><span class="sxs-lookup"><span data-stu-id="b2a62-149">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="b2a62-150">Yöntemini çağırmış istemciye dışındaki bağlanan tüm istemciler üzerinde bir yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="b2a62-150">Calls a method on all connected clients except the client that invoked the method</span></span> |


<span data-ttu-id="b2a62-151">`Hub.Clients` Ayrıca aşağıdaki yöntemleri içerir:</span><span class="sxs-lookup"><span data-stu-id="b2a62-151">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="b2a62-152">Yöntem</span><span class="sxs-lookup"><span data-stu-id="b2a62-152">Method</span></span> | <span data-ttu-id="b2a62-153">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b2a62-153">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="b2a62-154">Bağlanan tüm istemciler belirtilen bağlantılar dışında bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="b2a62-154">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="b2a62-155">Belirli bir bağlı istemci üzerinde bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="b2a62-155">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="b2a62-156">Belirli bir bağlı istemciler üzerinde bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="b2a62-156">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="b2a62-157">Belirtilen grubun tüm bağlantılar için bir yöntem çağırır</span><span class="sxs-lookup"><span data-stu-id="b2a62-157">Calls a method to all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="b2a62-158">Belirtilen gruptaki belirtilen bağlantılar dışında tüm bağlantılar için bir yöntem çağırır.</span><span class="sxs-lookup"><span data-stu-id="b2a62-158">Calls a method to all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="b2a62-159">Birden çok gruba bağlantılarının bir metod çağırır</span><span class="sxs-lookup"><span data-stu-id="b2a62-159">Calls a method to multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="b2a62-160">Bir hub yöntemini çağırmış istemciye hariç bağlantıları, grup için bir yöntem çağırır</span><span class="sxs-lookup"><span data-stu-id="b2a62-160">Calls a method to a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="b2a62-161">Belirli bir kullanıcıyla ilişkili tüm bağlantıları için bir yöntem çağırır</span><span class="sxs-lookup"><span data-stu-id="b2a62-161">Calls a method to all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="b2a62-162">Belirtilen kullanıcılar ile ilişkili tüm bağlantıları için bir yöntem çağırır</span><span class="sxs-lookup"><span data-stu-id="b2a62-162">Calls a method to all connections associated with the specified users</span></span> |

<span data-ttu-id="b2a62-163">Her bir özellik veya yöntemin önceki tablolarda sahip bir nesne döndürür. bir `SendAsync` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="b2a62-163">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="b2a62-164">`SendAsync` Yöntemi çağırmak için istemci yönteminin parametreleri ve adını girmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="b2a62-164">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="b2a62-165">İstemciler için iletileri gönder</span><span class="sxs-lookup"><span data-stu-id="b2a62-165">Send messages to clients</span></span>

<span data-ttu-id="b2a62-166">Özel istemciler çağrı yapmak için özelliklerini kullanmak `Clients` nesne.</span><span class="sxs-lookup"><span data-stu-id="b2a62-166">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="b2a62-167">Aşağıdaki örnekte, `SendMessageToCaller` yöntemi gösterir hub yöntemini çağırmış bağlantıya ileti gönderme.</span><span class="sxs-lookup"><span data-stu-id="b2a62-167">In the following example, the `SendMessageToCaller` method demonstrates sending a message to the connection that invoked the hub method.</span></span> <span data-ttu-id="b2a62-168">`SendMessageToGroups` Yöntemi depolanan gruplara bir ileti gönderen bir `List` adlı `groups`.</span><span class="sxs-lookup"><span data-stu-id="b2a62-168">The `SendMessageToGroups` method sends a message to the groups stored in a `List` named `groups`.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?range=15-24)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="b2a62-169">Kesin türü belirtilmiş hub'ları</span><span class="sxs-lookup"><span data-stu-id="b2a62-169">Strongly typed hubs</span></span>

<span data-ttu-id="b2a62-170">Kullanmanın bir dezavantajı `SendAsync` olan çağrılacak istemci yöntemi belirtmek için Sihirli bir dize kullanır.</span><span class="sxs-lookup"><span data-stu-id="b2a62-170">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="b2a62-171">Bu yöntem adı yanlış yazılmış, çalışma zamanı hataları için kod açık ya da istemciden eksik bırakır.</span><span class="sxs-lookup"><span data-stu-id="b2a62-171">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="b2a62-172">Kullanmaya alternatif `SendAsync` kesin yazmaktır `Hub` ile <xref:Microsoft.AspNetCore.SignalR.Hub`1>.</span><span class="sxs-lookup"><span data-stu-id="b2a62-172">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub`1>.</span></span> <span data-ttu-id="b2a62-173">Aşağıdaki örnekte, `ChatHub` istemci yöntemleri ayıklandı out adlı bir arabirim `IChatClient`.</span><span class="sxs-lookup"><span data-stu-id="b2a62-173">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>  

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="b2a62-174">Bu arabirim, önceki yeniden kullanılabilir `ChatHub` örnek.</span><span class="sxs-lookup"><span data-stu-id="b2a62-174">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="b2a62-175">Kullanarak `Hub<IChatClient>` derleme zamanı istemci yöntemleri denetimini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="b2a62-175">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="b2a62-176">Bu Sihirli dize beri kullanımından kaynaklanan sorunları önler `Hub<T>` yalnızca arabirim içinde tanımlanmış yöntemleri erişim sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="b2a62-176">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="b2a62-177">Türü kesin belirlenmiş kullanarak `Hub<T>` kullanma yeteneği devre dışı bırakır `SendAsync`.</span><span class="sxs-lookup"><span data-stu-id="b2a62-177">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span>

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="b2a62-178">Bağlantı olayları işleme</span><span class="sxs-lookup"><span data-stu-id="b2a62-178">Handle events for a connection</span></span>

<span data-ttu-id="b2a62-179">SignalR hub'ları API sağlar `OnConnectedAsync` ve `OnDisconnectedAsync` yönetmek ve bağlantıları izlemek için sanal yöntemler.</span><span class="sxs-lookup"><span data-stu-id="b2a62-179">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="b2a62-180">Geçersiz kılma `OnConnectedAsync` bir istemci bir gruba ekleme gibi Hub'ına bağlandığında eylemleri gerçekleştirmek için sanal bir yöntem.</span><span class="sxs-lookup"><span data-stu-id="b2a62-180">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle events](hubs/sample/hubs/chathub.cs?range=26-36)]

## <a name="handle-errors"></a><span data-ttu-id="b2a62-181">Hatalarını işleme</span><span class="sxs-lookup"><span data-stu-id="b2a62-181">Handle errors</span></span>

<span data-ttu-id="b2a62-182">Özel durumlar, hub yöntemlerinde oluşturulan yöntemini çağırmış istemciye gönderilir.</span><span class="sxs-lookup"><span data-stu-id="b2a62-182">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="b2a62-183">JavaScript istemci `invoke` yöntemi döndürür bir [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span><span class="sxs-lookup"><span data-stu-id="b2a62-183">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="b2a62-184">İstemci bir işleyici ile bir hata aldığında bağlı promise kullanmaya `catch`, çağrılan ve bir JavaScript olarak geçirilen `Error` nesne.</span><span class="sxs-lookup"><span data-stu-id="b2a62-184">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

## <a name="related-resources"></a><span data-ttu-id="b2a62-185">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b2a62-185">Related resources</span></span>

* [<span data-ttu-id="b2a62-186">ASP.NET Core signalr'a giriş</span><span class="sxs-lookup"><span data-stu-id="b2a62-186">Intro to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="b2a62-187">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="b2a62-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="b2a62-188">Azure'a Yayımlama</span><span class="sxs-lookup"><span data-stu-id="b2a62-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
