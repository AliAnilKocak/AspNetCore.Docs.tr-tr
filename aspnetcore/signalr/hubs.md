---
title: ASP.NET Core SignalR 'de hub 'ları kullanma
author: bradygaster
description: ASP.NET Core SignalR 'de hub 'ları kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/20/2018
uid: signalr/hubs
ms.openlocfilehash: 4922d6d780b18727d3ac181b94dbf75458d74fe6
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746520"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a>ASP.NET Core için SignalR içindeki hub 'ları kullanma

, [Rachel Appel](https://twitter.com/rachelappel) ve [Kevin Griffin](https://twitter.com/1kevgriff) tarafından

[Örnek kodu görüntüle veya indir](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(indirme)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-signalr-hub"></a>SignalR hub nedir?

SignalR hub 'Ları API 'SI, bağlı istemcilerdeki yöntemleri sunucudan çağırmanızı sağlar. Sunucu kodunda, istemci tarafından çağrılan yöntemleri tanımlarsınız. İstemci kodunda, sunucudan çağrılan yöntemleri tanımlarsınız. SignalR, gerçek zamanlı istemciden sunucuya ve sunucudan istemciye iletişimleri mümkün kılan arka planda her şeyi üstlenir.

## <a name="configure-signalr-hubs"></a>SignalR hub 'larını yapılandırma

SignalR ara yazılımı, çağırarak `services.AddSignalR`yapılandırılan bazı hizmetler gerektirir.

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

Bir ASP.NET Core uygulamasına SignalR işlevselliği eklerken, `endpoint.MapHub` `Startup.Configure` metodun `app.UseEndpoints` geri çağrısında arayarak SignalR yollarını ayarlayın.

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Bir ASP.NET Core uygulamasına SignalR işlevselliği eklerken, `app.UseSignalR` `Startup.Configure` yöntemini çağırarak SignalR yollarını ayarlayın.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>Hub 'lar oluşturma ve kullanma

Öğesinden `Hub`devralan bir sınıf bildirerek bir hub oluşturun ve ona ortak yöntemler ekleyin. İstemcileri, olarak `public`tanımlanan yöntemleri çağırabilir.

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Herhangi C# bir yöntemde yaptığınız gibi, karmaşık türler ve diziler dahil olmak üzere bir dönüş türü ve parametreleri belirtebilirsiniz. SignalR parametrelerinizin ve dönüş değerlerindeki karmaşık nesne ve dizilerin serileştirilmesi ve serisini kaldırma işlemi gerçekleştirir.

> [!NOTE]
> Hub 'lar geçicidir:
>
> * Durumu hub sınıfının bir özelliğinde depolamayın. Her hub yöntemi çağrısı yeni bir hub örneğinde yürütülür.
> * Hub `await` 'a bağlı olan zaman uyumsuz yöntemleri çağırırken kullanım etkin kalmakta. Örneğin, gibi bir yöntem `Clients.All.SendAsync(...)` , olmadan `await` çağrılırsa ve hub yöntemi bitmeden önce `SendAsync` tamamlandığında başarısız olabilir.

## <a name="the-context-object"></a>Bağlam nesnesi

Sınıfı, bağlantıyla ilgili `Context` bilgiler içeren aşağıdaki özellikleri içeren bir özelliğe sahiptir: `Hub`

| Özellik | Açıklama |
| ------ | ----------- |
| `ConnectionId` | SignalR tarafından atanan bağlantının benzersiz KIMLIĞINI alır. Her bağlantı için bir bağlantı KIMLIĞI vardır.|
| `UserIdentifier` | [Kullanıcı tanımlayıcısını](xref:signalr/groups)alır. Varsayılan olarak, SignalR, `ClaimTypes.NameIdentifier` `ClaimsPrincipal` Kullanıcı tanımlayıcısı ile ilişkilendirilen bağlantıyla ilişkili ' ı kullanır. |
| `User` | Geçerli kullanıcıyla `ClaimsPrincipal` ilişkili öğesini alır. |
| `Items` | Bu bağlantının kapsamındaki verileri paylaşmak için kullanılabilecek bir anahtar/değer koleksiyonu alır. Veriler bu koleksiyonda depolanabilir ve farklı hub yöntemi etkinleştirmeleri arasında bağlantı için kalıcı hale gelir. |
| `Features` | Bağlantıda kullanılabilen özelliklerin koleksiyonunu alır. Şimdilik bu koleksiyon Çoğu senaryoda gerekli değildir, bu nedenle henüz ayrıntılı olarak açıklanmamıştır. |
| `ConnectionAborted` | Bağlantı iptal `CancellationToken` edildiğinde bir bildirim alır. |

`Hub.Context`Aşağıdaki yöntemleri de içerir:

| Yöntem | Açıklama |
| ------ | ----------- |
| `GetHttpContext` | Bağlantı için veya `null` bağlantı bir HTTP isteğiyle ilişkilendirilmediği takdirde. `HttpContext` HTTP bağlantılarında, HTTP üstbilgileri ve sorgu dizeleri gibi bilgileri almak için bu yöntemi kullanabilirsiniz. |
| `Abort` | Bağlantıyı iptal eder. |

## <a name="the-clients-object"></a>Istemciler nesnesi

Sınıfı, sunucu ve `Clients` istemci arasındaki iletişim için aşağıdaki özellikleri içeren bir özelliğe sahiptir: `Hub`

| Özellik | Açıklama |
| ------ | ----------- |
| `All` | Tüm bağlı istemcilerde bir yöntemi çağırır |
| `Caller` | İstemciye, hub yöntemini çağıran bir yöntemi çağırır |
| `Others` | Yöntemi çağıran istemci hariç tüm bağlı istemcilerde bir yöntemi çağırır |

`Hub.Clients`Aşağıdaki yöntemleri de içerir:

| Yöntem | Açıklama |
| ------ | ----------- |
| `AllExcept` | Belirtilen bağlantılar hariç tüm bağlı istemcilerde bir yöntemi çağırır |
| `Client` | Belirli bir bağlı istemcide bir yöntemi çağırır |
| `Clients` | Belirli bağlı istemcilerde bir yöntemi çağırır |
| `Group` | Belirtilen gruptaki tüm bağlantılarda bir yöntemi çağırır  |
| `GroupExcept` | Belirtilen bağlantılar dışında, belirtilen gruptaki tüm bağlantılarda bir yöntemi çağırır |
| `Groups` | Birden çok bağlantı grubunda bir yöntemi çağırır  |
| `OthersInGroup` | Hub yöntemini çağıran istemci hariç bir bağlantı grubu üzerinde bir yöntemi çağırır  |
| `User` | Belirli bir kullanıcıyla ilişkili tüm bağlantılarda bir yöntemi çağırır |
| `Users` | Belirtilen kullanıcılarla ilişkili tüm bağlantılarda bir yöntemi çağırır |

Yukarıdaki tablolardaki her bir özellik veya yöntem bir `SendAsync` yöntemine sahip bir nesne döndürür. `SendAsync` Yöntemi, çağrılacak istemci yönteminin adını ve parametrelerini girmenize olanak sağlar.

## <a name="send-messages-to-clients"></a>İstemcilere ileti gönderme

Belirli istemcilere çağrı yapmak için `Clients` nesnesinin özelliklerini kullanın. Aşağıdaki örnekte, üç hub yöntemi vardır:

* `SendMessage`kullanarak `Clients.All`tüm bağlı istemcilere bir ileti gönderir.
* `SendMessageToCaller`kullanarak `Clients.Caller`, çağırana geri bir ileti gönderir.
* `SendMessageToGroups``SignalR Users` gruptaki tüm istemcilere bir ileti gönderir.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Türü kesin belirlenmiş hub 'lar

Kullanmanın `SendAsync` bir dezavantajı, çağrılacak istemci yöntemini belirtmek için bir sihirli dize kullanır. Bu, yöntem adı yanlış yazıldığında veya istemcide eksikse kodu, çalışma zamanı hatalarına açık bırakır.

Kullanmanın `SendAsync` alternatifi, `Hub` ile <xref:Microsoft.AspNetCore.SignalR.Hub%601>kesin bir şekilde yazmak için kullanılır. Aşağıdaki örnekte, `ChatHub` istemci yöntemleri adlı `IChatClient`bir arabirimde çıkarılmıştır.

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Bu arabirim, önceki `ChatHub` örneği yeniden düzenleme için kullanılabilir.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

Kullanımı `Hub<IChatClient>` , istemci yöntemlerinin derleme zamanı denetimini sunar. Bu, yalnızca arabirimde tanımlanan yöntemlere erişim `Hub<T>` sağlayabileceğinizden, sihirli dizeler kullanılarak oluşan sorunları önler.

Türü kesin belirlenmiş `Hub<T>` kullanılması, kullanma `SendAsync`yeteneğini devre dışı bırakır. Arabirim üzerinde tanımlanan Yöntemler hala zaman uyumsuz olarak tanımlanabilir. Aslında, bu yöntemlerin her biri bir `Task`döndürmelidir. Bir arabirim olduğundan `async` anahtar sözcüğünü kullanmayın. Örneğin:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> `Async` Sonek, yöntem adından çıkarılır. İstemci yönteminiz ile `.on('MyMethodAsync')`tanımlanmadıysa, ad olarak kullanmamalısınız `MyMethodAsync` .

## <a name="change-the-name-of-a-hub-method"></a>Bir hub yönteminin adını değiştirme

Varsayılan olarak, bir sunucu hub 'ı Yöntem adı .NET yönteminin adıdır. Ancak, bu Varsayılanı değiştirmek ve yöntemi için el ile bir ad belirtmek için [Hubmethodname](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) özniteliğini kullanabilirsiniz. İstemci, yöntemi çağırırken .NET Yöntem adı yerine bu adı kullanmalıdır.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Bir bağlantı için olayları işleyin

SignalR hub 'ları API 'si, `OnConnectedAsync` bağlantıları `OnDisconnectedAsync` yönetmek ve izlemek için ve sanal yöntemler sağlar. Bir istemci hub 'a bağlanırken bir gruba ekleme gibi işlemleri gerçekleştirmek için sanalyöntemigeçersizkılın.`OnConnectedAsync`

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Bir istemcinin bağlantısı kesildiğinde işlemleri gerçekleştirmek için sanalyöntemigeçersizkılın.`OnDisconnectedAsync` İstemci kasıtlı olarak bağlantı kesildiğinde (örneğin, `connection.stop()`çağırarak) `exception` , parametresi `null`olur. Ancak, istemcinin bağlantısı bir hata nedeniyle kesildiyse (örneğin, bir ağ arızası), `exception` Bu parametre hatayı açıklayan bir özel durum içerir.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a>Hataları işleme

Hub yöntemleriniz içinde oluşturulan özel durumlar, yöntemi çağıran istemciye gönderilir. JavaScript istemcisinde, `invoke` yöntemi bir [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)döndürür. İstemci, promise `catch`öğesine eklenmiş bir işleyiciyle bir hata aldığında, çağrılır ve bir JavaScript `Error` nesnesi olarak geçirilir.

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Hub 'ınız bir özel durum oluşturursa, bağlantılar kapanmamıştır. Varsayılan olarak, SignalR istemciye genel bir hata iletisi döndürür. Örneğin:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Beklenmeyen özel durumlar genellikle veritabanı bağlantısı başarısız olduğunda tetiklenen bir özel durumda veritabanı sunucusunun adı gibi hassas bilgiler içerir. SignalR, varsayılan olarak güvenlik önlemi olarak bu ayrıntılı hata iletilerini açığa çıkarır. Özel durum ayrıntılarının neden bastırıldığına ilişkin daha fazla bilgi için [güvenlik konuları makalesine](xref:signalr/security#exceptions) bakın.

İstemciye *yaymak istediğiniz olağanüstü* bir koşulunuz varsa, `HubException` sınıfını kullanabilirsiniz. Hub yönteinizden bir `HubException` oluşturursanız, **SignalR tüm** iletiyi istemciye, değiştirilmemiş olarak gönderir.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR yalnızca özel durumun `Message` özelliğini istemciye gönderir. Özel durumun yığın izlemesi ve diğer özellikleri istemci tarafından kullanılamaz.

## <a name="related-resources"></a>İlgili kaynaklar

* [ASP.NET Core SignalR 'ye giriş](xref:signalr/introduction)
* [JavaScript istemcisi](xref:signalr/javascript-client)
* [Azure'a Yayımlama](xref:signalr/publish-to-azure-web-app)
