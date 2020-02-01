---
title: ASP.NET Core için SignalR içinde MessagePack hub protokolünü kullanın
author: bradygaster
description: ASP.NET Core SignalRMessagePack hub protokolünü ekleyin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 3c2a4285945d3fdc6bba195e3160da8b9dcbba44
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928184"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="31653-103">ASP.NET Core için SignalR içinde MessagePack hub protokolünü kullanın</span><span class="sxs-lookup"><span data-stu-id="31653-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="31653-104">[Brennan Conroy](https://github.com/BrennanConroy) tarafından</span><span class="sxs-lookup"><span data-stu-id="31653-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="31653-105">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="31653-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="31653-106">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="31653-106">What is MessagePack?</span></span>

<span data-ttu-id="31653-107">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt bir ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="31653-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="31653-108">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="31653-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="31653-109">İkili bir biçim olduğundan, baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde ağ izlemeleri ve günlükleri aranırken iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="31653-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="31653-110"> MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="31653-110"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="31653-111">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="31653-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="31653-112">Sunucuda MessagePack hub protokolünü etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini uygulamanıza yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="31653-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="31653-113">Startup.cs dosyasında, sunucuda MessagePack desteğini etkinleştirmek için `AddSignalR` çağrısına `AddMessagePackProtocol` ekleyin.</span><span class="sxs-lookup"><span data-stu-id="31653-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="31653-114">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="31653-114">JSON is enabled by default.</span></span> <span data-ttu-id="31653-115">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="31653-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="31653-116">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="31653-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="31653-117">Bu temsilcisinde, `FormatterResolvers` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="31653-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="31653-118">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="31653-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="31653-119">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="31653-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

> [!WARNING]
> <span data-ttu-id="31653-120">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="31653-120">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="31653-121">Örneğin, `MessagePackSecurity.Active` static özelliği `MessagePackSecurity.UntrustedData`olarak ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="31653-121">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="31653-122">`MessagePackSecurity.Active` ayarlamak için [MessagePack 'in bir 1.9. x sürümünün](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="31653-122">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="31653-123">Yükleme `MessagePack` 1.9. x yükseltmeleri, SignalR sürümünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="31653-123">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="31653-124">`MessagePackSecurity.Active` `MessagePackSecurity.UntrustedData`olarak ayarlanmamışsa kötü amaçlı bir istemci hizmet reddine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="31653-124">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="31653-125">Aşağıdaki kodda gösterildiği gibi `Program.Main``MessagePackSecurity.Active` ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="31653-125">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="31653-126">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="31653-126">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="31653-127">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="31653-127">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="31653-128">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="31653-128">Clients can only support a single protocol.</span></span> <span data-ttu-id="31653-129">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="31653-129">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="31653-130">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="31653-130">.NET client</span></span>

<span data-ttu-id="31653-131">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip `HubConnectionBuilder``AddMessagePackProtocol` çağırın.</span><span class="sxs-lookup"><span data-stu-id="31653-131">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="31653-132">Bu `AddMessagePackProtocol` çağrısı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="31653-132">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="31653-133">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="31653-133">JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="31653-134">JavaScript istemcisi için MessagePack desteği `@microsoft/signalr-protocol-msgpack` NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="31653-134">MessagePack support for the JavaScript client is provided by the `@microsoft/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="31653-135">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="31653-135">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="31653-136">JavaScript istemcisi için MessagePack desteği `@aspnet/signalr-protocol-msgpack` NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="31653-136">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="31653-137">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="31653-137">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

<span data-ttu-id="31653-138">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="31653-138">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="31653-139">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="31653-139">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="31653-140">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="31653-140">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

<span data-ttu-id="31653-141">Bir tarayıcıda `msgpack5` kitaplığına da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="31653-141">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="31653-142">Başvuru oluşturmak için bir `<script>` etiketi kullanın.</span><span class="sxs-lookup"><span data-stu-id="31653-142">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="31653-143">Kitaplık, *node_modules \msgpack5\dist\msgpack5,JS*konumunda bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="31653-143">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="31653-144">`<script>` öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="31653-144">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="31653-145">*SignalR-Protocol-msgpack. js* ' *msgpack5. js*' den önce başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="31653-145">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="31653-146">*SignalR. js* , *SignalR-Protocol-msgpack. js*' den önce de gereklidir.</span><span class="sxs-lookup"><span data-stu-id="31653-146">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="31653-147">`HubConnectionBuilder` `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` eklemek, istemciyi bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="31653-147">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="31653-148">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="31653-148">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="31653-149">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="31653-149">MessagePack quirks</span></span>

<span data-ttu-id="31653-150">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="31653-150">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="31653-151">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="31653-151">MessagePack is case-sensitive</span></span>

<span data-ttu-id="31653-152">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="31653-152">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="31653-153">Örneğin, aşağıdaki C# sınıfı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="31653-153">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="31653-154">JavaScript istemcisinden gönderirken, büyük/küçük harf C# sınıfıyla tam olarak eşleşmesi gerektiğinden `PascalCased` özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="31653-154">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="31653-155">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="31653-155">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="31653-156">`camelCased` adları kullanmak C# sınıfa düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="31653-156">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="31653-157">MessagePack özelliği için farklı bir ad belirtmek üzere `Key` özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="31653-157">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="31653-158">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="31653-158">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="31653-159">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="31653-159">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="31653-160">MessagePack protokolü, bir `DateTime``Kind` değerini kodlamak için bir yol sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="31653-160">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="31653-161">Sonuç olarak, bir tarih serisi kaldırılırken, MessagePack hub protokolü gelen tarihin UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="31653-161">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="31653-162">Yerel saat içinde `DateTime` değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="31653-162">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="31653-163">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="31653-163">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="31653-164">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="31653-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="31653-165">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="31653-165">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="31653-166">SignalR JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) kitaplığı MessagePack içindeki `timestamp96` türünü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="31653-166">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="31653-167">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="31653-167">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="31653-168">`DateTime.MinValue` değeri, bir `timestamp96` değerinde kodlanması gereken `January 1, 0001`.</span><span class="sxs-lookup"><span data-stu-id="31653-168">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="31653-169">Bu nedenle, JavaScript istemcisine `DateTime.MinValue` gönderilmesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="31653-169">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="31653-170">JavaScript istemcisi tarafından `DateTime.MinValue` alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="31653-170">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="31653-171">Genellikle, `DateTime.MinValue` bir "eksik" veya `null` değeri kodlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="31653-171">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="31653-172">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir `DateTime` değeri (`DateTime?`) kullanın veya tarihin mevcut olup olmadığını belirten ayrı bir `bool` değeri kodlayın.</span><span class="sxs-lookup"><span data-stu-id="31653-172">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="31653-173">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="31653-173">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="31653-174">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="31653-174">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="31653-175">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="31653-175">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="31653-176">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="31653-176">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="31653-177">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="31653-177">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="31653-178">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="31653-178">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="31653-179">Serileştiriciler önceden oluşturulduktan sonra, `AddMessagePackProtocol`geçirilen yapılandırma temsilcisini kullanarak kaydedebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="31653-179">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="31653-180">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="31653-180">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="31653-181">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="31653-181">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="31653-182">Örneğin, gelen nesnenin bir sayı olan (`{ foo: 42 }`) bir özellik değeri varsa, ancak .NET sınıfındaki özelliği `string`türündedir, değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="31653-182">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="31653-183">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="31653-183">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="31653-184">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="31653-184">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="31653-185">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="31653-185">Related resources</span></span>

* [<span data-ttu-id="31653-186">Başlarken</span><span class="sxs-lookup"><span data-stu-id="31653-186">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="31653-187">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="31653-187">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="31653-188">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="31653-188">JavaScript client</span></span>](xref:signalr/javascript-client)
