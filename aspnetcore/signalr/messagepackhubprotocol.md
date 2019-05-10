---
title: MessagePack Hub protokol SignalR öğesinde ASP.NET Core için kullanın.
author: bradygaster
description: MessagePack Hub protokolü için ASP.NET Core SignalR ekleyin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/27/2019
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7742f6f8bb53fb3c299ff98ae52a0da519ff396c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902594"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="b2883-103">MessagePack Hub protokol SignalR öğesinde ASP.NET Core için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b2883-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="b2883-104">Tarafından [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="b2883-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="b2883-105">Bu makalede, okuyucu, konu başlıkları hakkında bilgi sahibi olduğunu varsayar [Başlarken](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="b2883-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="b2883-106">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="b2883-106">What is MessagePack?</span></span>

<span data-ttu-id="b2883-107">[MessagePack](https://msgpack.org/index.html) hızlı ve sıkıştırılmış bir ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="b2883-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="b2883-108">İle karşılaştırıldığında daha küçük ileti oluşturduğundan performans ve bant genişliği bir sorun olduğunda kullanışlıdır [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="b2883-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="b2883-109">Bir ikili biçimi olduğundan, iletileri bayt MessagePack inceleyicisi üzerinden geçirilir sürece ağ izlemelerini ve günlüklerini ararken okunamaz.</span><span class="sxs-lookup"><span data-stu-id="b2883-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="b2883-110">SignalR MessagePack biçimi için yerleşik destek içerir ve istemci ve sunucu kullanmak için API'ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="b2883-110">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="b2883-111">MessagePack yapılandırın</span><span class="sxs-lookup"><span data-stu-id="b2883-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="b2883-112">Sunucuda MessagePack Hub Protokolü etkinleştirmek için yükleme `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` uygulamanızı bir pakette.</span><span class="sxs-lookup"><span data-stu-id="b2883-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="b2883-113">Ekleme Startup.cs dosyasındaki `AddMessagePackProtocol` için `AddSignalR` sunucuda MessagePack desteğini etkinleştirmek için çağrı.</span><span class="sxs-lookup"><span data-stu-id="b2883-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="b2883-114">JSON, varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="b2883-114">JSON is enabled by default.</span></span> <span data-ttu-id="b2883-115">MessagePack ekleme, hem JSON hem de MessagePack istemciler için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="b2883-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="b2883-116">MessagePack verilerinizi, nasıl biçimlendirecek özelleştirmek için `AddMessagePackProtocol` seçeneklerini yapılandırmak için bir temsilciyi alır.</span><span class="sxs-lookup"><span data-stu-id="b2883-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="b2883-117">Bu temsilci `FormatterResolvers` özelliği, MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b2883-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="b2883-118">MessagePack kitaplık Çözümleyicileri nasıl çalıştığı hakkında daha fazla bilgi için ziyaret [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="b2883-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="b2883-119">Öznitelikleri nasıl ele tanımlamak için seri hale getirmek istediğiniz nesneler üzerinde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b2883-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="b2883-120">İstemcide MessagePack yapılandırın</span><span class="sxs-lookup"><span data-stu-id="b2883-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="b2883-121">JSON desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="b2883-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="b2883-122">İstemcileri yalnızca tek bir protokolü destekler.</span><span class="sxs-lookup"><span data-stu-id="b2883-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="b2883-123">MessagePack destek herhangi daha önce değiştirecek ekleme protokolleri yapılandırılmış.</span><span class="sxs-lookup"><span data-stu-id="b2883-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="b2883-124">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="b2883-124">.NET client</span></span>

<span data-ttu-id="b2883-125">.NET istemci MessagePack etkinleştirmek için yükleme `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paket ve çağrı `AddMessagePackProtocol` üzerinde `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b2883-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="b2883-126">Bu `AddMessagePackProtocol` çağrı sunucu gibi seçenekleri yapılandırmak için bir temsilciyi alır.</span><span class="sxs-lookup"><span data-stu-id="b2883-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="b2883-127">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="b2883-127">JavaScript client</span></span>

<span data-ttu-id="b2883-128">JavaScript istemci MessagePack desteği tarafından sağlanır `@aspnet/signalr-protocol-msgpack` npm paketi.</span><span class="sxs-lookup"><span data-stu-id="b2883-128">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="b2883-129">Npm paket yüklendikten sonra modülü doğrudan bir JavaScript Modülü Yükleyicisi kullanılabilir veya başvurarak tarayıcıya içe *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* dosya.</span><span class="sxs-lookup"><span data-stu-id="b2883-129">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* file.</span></span> <span data-ttu-id="b2883-130">Bir tarayıcıda `msgpack5` kitaplığı da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b2883-130">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="b2883-131">Kullanım bir `<script>` başvuru oluşturmak için etiket.</span><span class="sxs-lookup"><span data-stu-id="b2883-131">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="b2883-132">Kitaplık şu yolda bulunabilir: *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="b2883-132">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="b2883-133">Kullanırken `<script>` öğesi sırası önemlidir.</span><span class="sxs-lookup"><span data-stu-id="b2883-133">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="b2883-134">Varsa *signalr protokolünü msgpack.js* önce başvurulan *msgpack5.js*, MessagePack ile bağlanmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="b2883-134">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="b2883-135">*signalr.js* daha önce de gereklidir *signalr protokolünü msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="b2883-135">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="b2883-136">Ekleme `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` için `HubConnectionBuilder` MessagePack protokolü bir sunucuya bağlanırken kullanmak üzere istemciyi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="b2883-136">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="b2883-137">Şu anda hiçbir JavaScript istemci MessagePack protokolü için yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="b2883-137">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="b2883-138">MessagePack quirks</span><span class="sxs-lookup"><span data-stu-id="b2883-138">MessagePack quirks</span></span>

<span data-ttu-id="b2883-139">MessagePack Hub protokolünü kullanırken dikkat edilmesi gereken birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="b2883-139">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="b2883-140">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="b2883-140">MessagePack is case-sensitive</span></span>

<span data-ttu-id="b2883-141">MessagePack protokolü, büyük/küçük harf duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="b2883-141">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="b2883-142">Örneğin, aşağıdakileri dikkate alın C# sınıfı:</span><span class="sxs-lookup"><span data-stu-id="b2883-142">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="b2883-143">JavaScript istemciden gönderirken kullanmalısınız `PascalCased` özellik adları büyük/küçük harf eşleşmesi gerekir bu yana C# tam olarak sınıf.</span><span class="sxs-lookup"><span data-stu-id="b2883-143">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="b2883-144">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b2883-144">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="b2883-145">Kullanarak `camelCased` adlar olmaz düzgün bir şekilde bağlanma C# sınıfı.</span><span class="sxs-lookup"><span data-stu-id="b2883-145">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="b2883-146">Bu geçici bir çözüm kullanarak geçici `Key` MessagePack özelliği için farklı bir ad belirtmek için özniteliği.</span><span class="sxs-lookup"><span data-stu-id="b2883-146">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="b2883-147">Daha fazla bilgi için [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="b2883-147">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="b2883-148">Serileştirme/seri durumdan çıkarılırken zaman DateTime.Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="b2883-148">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="b2883-149">MessagePack Protokolü kodlamak için bir yol sağlamaz `Kind` değerini bir `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="b2883-149">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="b2883-150">Sonuç olarak, bir tarih işlenirken MessagePack Hub protokol gelen tarihi UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="b2883-150">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="b2883-151">İle çalışıyorsanız `DateTime` değerlerini yerel saat öneririz göndermeden önce UTC'ye dönüştürme.</span><span class="sxs-lookup"><span data-stu-id="b2883-151">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="b2883-152">Bunları aldığınızda bunları UTC'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="b2883-152">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="b2883-153">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorunu [aspnet/SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="b2883-153">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="b2883-154">Küçük JavaScript'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="b2883-154">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="b2883-155">[Msgpack5](https://github.com/mcollina/msgpack5) SignalR JavaScript istemci tarafından kullanılan kitaplığı desteklemiyor `timestamp96` MessagePack yazın.</span><span class="sxs-lookup"><span data-stu-id="b2883-155">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="b2883-156">Bu tür, çok büyük tarih değerleri (veya çok erken bir aşamasında geçmiş kadar çok ileride) kodlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b2883-156">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="b2883-157">Değerini `DateTime.MinValue` olduğu `January 1, 0001` hangi gerekir kodlanmış bir `timestamp96` değeri.</span><span class="sxs-lookup"><span data-stu-id="b2883-157">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="b2883-158">Bu, gönderme nedeniyle `DateTime.MinValue` JavaScript istemci desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="b2883-158">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="b2883-159">Zaman `DateTime.MinValue` alınan aşağıdaki hata JavaScript istemci tarafından oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="b2883-159">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="b2883-160">Genellikle, `DateTime.MinValue` kodlamak için kullanılan bir "eksik" veya `null` değeri.</span><span class="sxs-lookup"><span data-stu-id="b2883-160">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="b2883-161">MessagePack değeri kodlamak gerekiyorsa, boş değer atanabilir bir kullanın `DateTime` değeri (`DateTime?`) veya ayrı bir kodla `bool` tarih mevcut olup olmadığını gösteren değer.</span><span class="sxs-lookup"><span data-stu-id="b2883-161">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="b2883-162">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorunu [aspnet/SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="b2883-162">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="b2883-163">"Önceden-ın-time" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="b2883-163">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="b2883-164">[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) .NET istemcisi ve sunucusu tarafından kullanılan kitaplığı serileştirme iyileştirmek için kod oluşturma kullanır.</span><span class="sxs-lookup"><span data-stu-id="b2883-164">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="b2883-165">Sonuç olarak, varsayılan olarak "üretim-ın-time" derleme (örneğin, Xamarin iOS veya Unity) kullanan ortamlar desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b2883-165">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="b2883-166">"Önceden, seri hale getirici/seri durumdan çıkarıcının kod oluşturarak" Bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="b2883-166">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="b2883-167">Daha fazla bilgi için [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="b2883-167">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="b2883-168">Seri hale getiricileri genişletme önceden oluşturduktan sonra geçirilen yapılandırma temsilci kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="b2883-168">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="b2883-169">MessagePack içinde daha katı tür denetimleri</span><span class="sxs-lookup"><span data-stu-id="b2883-169">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="b2883-170">JSON Hub protokol seri durumundan çıkarma sırasında tür dönüştürmeleri gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="b2883-170">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="b2883-171">Gelen nesne bir özellik değeri gibi bir sayı ise (`{ foo: 42 }`) ancak özelliği .NET sınıf türünü `string`, değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="b2883-171">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="b2883-172">Ancak, MessagePack bu dönüştürme gerçekleştirmez ve sunucu tarafı günlüklerini (ve konsolundaki) görülebilir bir özel durum atar:</span><span class="sxs-lookup"><span data-stu-id="b2883-172">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="b2883-173">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorunu [aspnet/SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="b2883-173">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="b2883-174">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b2883-174">Related resources</span></span>

* [<span data-ttu-id="b2883-175">Başlarken</span><span class="sxs-lookup"><span data-stu-id="b2883-175">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b2883-176">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="b2883-176">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b2883-177">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="b2883-177">JavaScript client</span></span>](xref:signalr/javascript-client)
