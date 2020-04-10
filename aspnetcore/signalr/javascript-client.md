---
title: ASP.NET SignalR Core JavaScript istemcisi
author: bradygaster
description: Core SignalR JavaScript istemcisi ASP.NET genel bakış.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: a99c1dd2aba6ef6ff925783762a98e2c81ed7225
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994578"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="9ca39-103">ASP.NET SignalR Core JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="9ca39-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="9ca39-104">Yazar: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="9ca39-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="9ca39-105">ASP.NET Core SignalR JavaScript istemci kitaplığı, geliştiricilerin sunucu tarafındaki hub kodunu aramasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="9ca39-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="9ca39-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9ca39-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="9ca39-107">İstemci paketini SignalR yükleme</span><span class="sxs-lookup"><span data-stu-id="9ca39-107">Install the SignalR client package</span></span>

<span data-ttu-id="9ca39-108">SignalR JavaScript istemci kitaplığı [npm](https://www.npmjs.com/) paketi olarak teslim edilir.</span><span class="sxs-lookup"><span data-stu-id="9ca39-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="9ca39-109">Aşağıdaki bölümler, istemci kitaplığını yüklemenin farklı yollarını sıralar.</span><span class="sxs-lookup"><span data-stu-id="9ca39-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="9ca39-110">npm ile yükleyin</span><span class="sxs-lookup"><span data-stu-id="9ca39-110">Install with npm</span></span>

<span data-ttu-id="9ca39-111">Visual Studio kullanıyorsanız, kök klasördeyken **Package Manager Console'dan** aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="9ca39-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="9ca39-112">Visual Studio Code **için, Entegre Terminal'den**aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="9ca39-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="9ca39-113">npm paket içeriğini \*node_modules\\ \* klasörüne yükler.</span><span class="sxs-lookup"><span data-stu-id="9ca39-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="9ca39-114">*wwwroot\\lib* klasörü altında *signalr* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9ca39-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="9ca39-115">*Signalr.js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="9ca39-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="9ca39-116">npm paket içeriğini \*node_modules\\ \* klasörüne yükler.</span><span class="sxs-lookup"><span data-stu-id="9ca39-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="9ca39-117">*wwwroot\\lib* klasörü altında *signalr* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9ca39-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="9ca39-118">*Signalr.js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="9ca39-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="9ca39-119">Öğedeki SignalR JavaScript istemcisi başvurun. `<script>`</span><span class="sxs-lookup"><span data-stu-id="9ca39-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="9ca39-120">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="9ca39-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="9ca39-121">İçerik Dağıtım Ağı (CDN) kullanma</span><span class="sxs-lookup"><span data-stu-id="9ca39-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="9ca39-122">NPM ön koşulu olmadan istemci kitaplığını kullanmak için istemci kitaplığı CDN barındırılan bir kopyasına başvurun.</span><span class="sxs-lookup"><span data-stu-id="9ca39-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="9ca39-123">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="9ca39-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="9ca39-124">İstemci kitaplığı aşağıdaki CDN'lerde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="9ca39-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="9ca39-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="9ca39-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="9ca39-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="9ca39-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="9ca39-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="9ca39-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="9ca39-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="9ca39-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="9ca39-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="9ca39-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="9ca39-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="9ca39-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="9ca39-131">LibMan ile yükleyin</span><span class="sxs-lookup"><span data-stu-id="9ca39-131">Install with LibMan</span></span>

<span data-ttu-id="9ca39-132">[LibMan,](xref:client-side/libman/index) CDN tarafından barındırılan istemci kitaplığından belirli istemci kitaplığı dosyalarını yüklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9ca39-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="9ca39-133">Örneğin, yalnızca minified JavaScript dosyasını projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9ca39-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="9ca39-134">Bu yaklaşımla ilgili ayrıntılar [ SignalR için](xref:tutorials/signalr#add-the-signalr-client-library)bkz.</span><span class="sxs-lookup"><span data-stu-id="9ca39-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="9ca39-135">Hub'a bağlanma</span><span class="sxs-lookup"><span data-stu-id="9ca39-135">Connect to a hub</span></span>

<span data-ttu-id="9ca39-136">Aşağıdaki kod bir bağlantı oluşturur ve başlatır.</span><span class="sxs-lookup"><span data-stu-id="9ca39-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="9ca39-137">Hub'ın adı büyük/küçük harf duyarsız.</span><span class="sxs-lookup"><span data-stu-id="9ca39-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="9ca39-138">Orijinler arası bağlantılar</span><span class="sxs-lookup"><span data-stu-id="9ca39-138">Cross-origin connections</span></span>

<span data-ttu-id="9ca39-139">Genellikle, tarayıcılar bağlantıları istenen sayfayla aynı etki alanından yükler.</span><span class="sxs-lookup"><span data-stu-id="9ca39-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="9ca39-140">Ancak, başka bir etki alanına bağlantı gerektiği durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="9ca39-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="9ca39-141">Kötü amaçlı bir sitenin başka bir siteden gelen hassas verileri okumasını önlemek [için, orijinler arası bağlantılar](xref:security/cors) varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="9ca39-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="9ca39-142">Bir çapraz kaynak isteğine izin vermek `Startup` için, bunu sınıfta etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="9ca39-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="9ca39-143">İstemciden hub yöntemlerini arama</span><span class="sxs-lookup"><span data-stu-id="9ca39-143">Call hub methods from client</span></span>

<span data-ttu-id="9ca39-144">JavaScript istemcileri [HubConnection'ın](/javascript/api/%40aspnet/signalr/hubconnection) [çağırma](/javascript/api/%40aspnet/signalr/hubconnection#invoke) yöntemi yle hub'larda genel yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="9ca39-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="9ca39-145">Yöntem `invoke` iki bağımsız değişkeni kabul eder:</span><span class="sxs-lookup"><span data-stu-id="9ca39-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="9ca39-146">Hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="9ca39-146">The name of the hub method.</span></span> <span data-ttu-id="9ca39-147">Aşağıdaki örnekte, hub'daki yöntem `SendMessage`adı.</span><span class="sxs-lookup"><span data-stu-id="9ca39-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="9ca39-148">Hub yönteminde tanımlanan bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="9ca39-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="9ca39-149">Aşağıdaki örnekte, bağımsız değişken `message`adı .</span><span class="sxs-lookup"><span data-stu-id="9ca39-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="9ca39-150">Örnek kod, Internet Explorer dışındaki tüm büyük tarayıcıların geçerli sürümlerinde desteklenen ok işlevi sözdizimini kullanır.</span><span class="sxs-lookup"><span data-stu-id="9ca39-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="9ca39-151">Azure SignalR Hizmetini *Serverless modunda*kullanıyorsanız, istemciden hub yöntemlerini çağıramazsınız.</span><span class="sxs-lookup"><span data-stu-id="9ca39-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="9ca39-152">Daha fazla bilgi için [ SignalR Hizmet belgelerine](/azure/azure-signalr/signalr-concept-serverless-development-config)bakın.</span><span class="sxs-lookup"><span data-stu-id="9ca39-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="9ca39-153">Yöntem `invoke` bir JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)döndürür.</span><span class="sxs-lookup"><span data-stu-id="9ca39-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="9ca39-154">Sunucudaki `Promise` yöntem döndüğünde iade değeri (varsa) ile çözülür.</span><span class="sxs-lookup"><span data-stu-id="9ca39-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="9ca39-155">Sunucudaki yöntem bir hata atarsa, `Promise` hata iletisi ile reddedilir.</span><span class="sxs-lookup"><span data-stu-id="9ca39-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="9ca39-156">Bu `then` servis `catch` taleplerini `Promise` (veya `await` sözdizimi) işlemek için kendi üzerindeki yöntemleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="9ca39-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="9ca39-157">Yöntem `send` bir JavaScript `Promise`döndürür.</span><span class="sxs-lookup"><span data-stu-id="9ca39-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="9ca39-158">İleti `Promise` sunucuya gönderildiğinde çözülür.</span><span class="sxs-lookup"><span data-stu-id="9ca39-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="9ca39-159">İletiyi gönderen bir hata varsa, hata iletisi ile `Promise` reddedilir.</span><span class="sxs-lookup"><span data-stu-id="9ca39-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="9ca39-160">Bu `then` servis `catch` taleplerini `Promise` (veya `await` sözdizimi) işlemek için kendi üzerindeki yöntemleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="9ca39-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="9ca39-161">Sunucu `send` iletiyi alana kadar kullanma beklemez.</span><span class="sxs-lookup"><span data-stu-id="9ca39-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="9ca39-162">Sonuç olarak, sunucudan veri veya hata döndürmek mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9ca39-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="9ca39-163">İstemci yöntemlerini hub'dan arama</span><span class="sxs-lookup"><span data-stu-id="9ca39-163">Call client methods from hub</span></span>

<span data-ttu-id="9ca39-164">Hub'dan ileti almak için, ['nin açık](/javascript/api/%40aspnet/signalr/hubconnection#on) yöntemini kullanarak bir yöntem `HubConnection`tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="9ca39-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="9ca39-165">JavaScript istemci yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="9ca39-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="9ca39-166">Aşağıdaki örnekte, yöntem adı `ReceiveMessage`.</span><span class="sxs-lookup"><span data-stu-id="9ca39-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="9ca39-167">Bağımsız değişkenler hub yönteme geçer.</span><span class="sxs-lookup"><span data-stu-id="9ca39-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="9ca39-168">Aşağıdaki örnekte, bağımsız değişken `message`değeri .</span><span class="sxs-lookup"><span data-stu-id="9ca39-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="9ca39-169">Sunucu tarafındaki kod `connection.on` [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) yöntemini kullanarak aradığında, önceki kod çalışır.</span><span class="sxs-lookup"><span data-stu-id="9ca39-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="9ca39-170">tanımlanan yöntem adı ve bağımsız değişkenleri eşleştirerek `SendAsync` `connection.on`hangi istemci yöntemini arayacağını belirler.</span><span class="sxs-lookup"><span data-stu-id="9ca39-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="9ca39-171">En iyi uygulama olarak, `HubConnection` sonra `on` [başlangıç](/javascript/api/%40aspnet/signalr/hubconnection#start) yöntemini arayın.</span><span class="sxs-lookup"><span data-stu-id="9ca39-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="9ca39-172">Bunu yapmak, işleyicilerinizin herhangi bir ileti alınmadan önce kaydedilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="9ca39-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="9ca39-173">Hata işleme ve günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="9ca39-173">Error handling and logging</span></span>

<span data-ttu-id="9ca39-174">İstemci tarafı hatalarını `start` işlemek için yöntemin sonuna bir `catch` yöntem zincirleyin.</span><span class="sxs-lookup"><span data-stu-id="9ca39-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="9ca39-175">Tarayıcının konsolundaki hataları çıkarmak için kullanın. `console.error`</span><span class="sxs-lookup"><span data-stu-id="9ca39-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="9ca39-176">Bağlantı yapıldığında günlüğe kaydetmek için bir logger ve olay türünü geçirerek istemci tarafı günlük izlemesini kurulum.</span><span class="sxs-lookup"><span data-stu-id="9ca39-176">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="9ca39-177">İletiler belirtilen günlük düzeyi ve daha yüksek günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9ca39-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="9ca39-178">Kullanılabilir günlük düzeyleri aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="9ca39-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="9ca39-179">`signalR.LogLevel.Error`&ndash; Hata iletileri.</span><span class="sxs-lookup"><span data-stu-id="9ca39-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="9ca39-180">Yalnızca `Error` iletileri günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="9ca39-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="9ca39-181">`signalR.LogLevel.Warning`&ndash; Olası hatalar hakkında uyarı iletileri.</span><span class="sxs-lookup"><span data-stu-id="9ca39-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="9ca39-182">Günlükler `Warning`ve `Error` mesajlar.</span><span class="sxs-lookup"><span data-stu-id="9ca39-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="9ca39-183">`signalR.LogLevel.Information`&ndash; Durum iletileri hatasız.</span><span class="sxs-lookup"><span data-stu-id="9ca39-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="9ca39-184">Günlükler `Information` `Warning`ve `Error` mesajlar.</span><span class="sxs-lookup"><span data-stu-id="9ca39-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="9ca39-185">`signalR.LogLevel.Trace`&ndash; İletileri takip et.</span><span class="sxs-lookup"><span data-stu-id="9ca39-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="9ca39-186">Hub ve istemci arasında taşınan veriler de dahil olmak üzere her şeyi günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="9ca39-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="9ca39-187">Günlük düzeyini [yapılandırmak](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) için [HubConnectionBuilder'da](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) yapılandırma Günlüğü yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9ca39-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="9ca39-188">İletiler tarayıcı konsoluna kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9ca39-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="9ca39-189">İstemcileri yeniden bağlama</span><span class="sxs-lookup"><span data-stu-id="9ca39-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="9ca39-190">Otomatik olarak yeniden bağlanma</span><span class="sxs-lookup"><span data-stu-id="9ca39-190">Automatically reconnect</span></span>

<span data-ttu-id="9ca39-191">Için JavaScript SignalR istemcisi `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)yöntemini kullanarak otomatik olarak yeniden bağlanmak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9ca39-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="9ca39-192">Varsayılan olarak otomatik olarak yeniden bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="9ca39-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="9ca39-193">Herhangi bir `withAutomaticReconnect()` parametre olmadan, istemciyi her yeniden bağlama denemesini denemeden önce sırasıyla 0, 2, 10 ve 30 saniye bekleyecek şekilde yapılandırArak dört başarısız denemeden sonra durur.</span><span class="sxs-lookup"><span data-stu-id="9ca39-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="9ca39-194">Herhangi bir yeniden bağlanma `HubConnection` girişimine başlamadan `HubConnectionState.Reconnecting` önce, `onreconnecting` `Disconnected` duruma geçiş yapacak ve duruma geçiş yapmak `onclose` yerine geri aramaları `HubConnection` ateşleyecek ve otomatik yeniden bağlanma olmadan geri aramaları tetikleyecek.</span><span class="sxs-lookup"><span data-stu-id="9ca39-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="9ca39-195">Bu, kullanıcıları bağlantının kaybolduğu konusunda uyarmak ve Kullanıcı Arabirimi öğelerini devre dışı etmek için bir fırsat sağlar.</span><span class="sxs-lookup"><span data-stu-id="9ca39-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="9ca39-196">İstemci ilk dört denemesinde başarılı bir `HubConnection` şekilde yeniden bağlanırsa, `Connected` `onreconnected` duruma geri döner ve geri aramalarını ateşler.</span><span class="sxs-lookup"><span data-stu-id="9ca39-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="9ca39-197">Bu, kullanıcılara bağlantının yeniden kurulduğunu bildirme fırsatı sağlar.</span><span class="sxs-lookup"><span data-stu-id="9ca39-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="9ca39-198">Bağlantı sunucuya tamamen yeni göründüğünden, `connectionId` `onreconnected` geri arama için yeni bir bağlantı sağlanacaktır.</span><span class="sxs-lookup"><span data-stu-id="9ca39-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="9ca39-199">`onreconnected` Arama nın `connectionId` parametresi, anlaşma [zatını atlamak](xref:signalr/configuration#configure-client-options)için yapılandırıldıysa `HubConnection` tanımsız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="9ca39-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="9ca39-200">`withAutomaticReconnect()`ilk başlatma hatalarını `HubConnection` yeniden denemek için yapılandırmaz, bu nedenle başlangıç hatalarının el ile ele alınması gerekir:</span><span class="sxs-lookup"><span data-stu-id="9ca39-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="9ca39-201">İstemci ilk dört denemesinde başarılı bir şekilde `HubConnection` yeniden bağlanmazsa, `Disconnected` duruma geçiş ve [yakın](/javascript/api/%40aspnet/signalr/hubconnection#onclose) geri aramaları ateş.</span><span class="sxs-lookup"><span data-stu-id="9ca39-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="9ca39-202">Bu, kullanıcılara bağlantının kalıcı olarak kaybolduğunu bildirmek ve sayfanın yenilenerek tavsiye de bulunmanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="9ca39-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="9ca39-203">Bağlantıyı kesmeden veya yeniden bağlanma zamanlamasını değiştirmeden önce özel bir `withAutomaticReconnect` yeniden bağlanma denemesi sayısını yapılandırmak için, her yeniden bağlantı girişimini başlatmadan önce beklemesi için milisaniye cinsinden gecikmeyi temsil eden bir dizi sayı kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9ca39-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="9ca39-204">Önceki örnek, bağlantı `HubConnection` kaybolduktan hemen sonra yeniden bağlanmayı denemeye başlamayı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="9ca39-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="9ca39-205">Bu, varsayılan yapılandırma için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9ca39-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="9ca39-206">İlk yeniden bağlanma denemesi başarısız olursa, varsayılan yapılandırmada olduğu gibi 2 saniye beklemek yerine ikinci yeniden bağlanma denemesi de hemen başlar.</span><span class="sxs-lookup"><span data-stu-id="9ca39-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="9ca39-207">İkinci yeniden bağlanma denemesi başarısız olursa, üçüncü yeniden bağlanma denemesi 10 saniye içinde başlar ve bu da varsayılan yapılandırma gibi olur.</span><span class="sxs-lookup"><span data-stu-id="9ca39-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="9ca39-208">Özel davranış daha sonra varsayılan yapılandırmada olduğu gibi başka bir 30 saniye içinde bir yeniden bağlanma girişimi denemek yerine üçüncü yeniden bağlanma girişimi hatası ndan sonra durdurarak varsayılan davranıştan yeniden uzaklaşır.</span><span class="sxs-lookup"><span data-stu-id="9ca39-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="9ca39-209">Otomatik yeniden bağlanma denemelerinin zamanlaması ve sayısı üzerinde `withAutomaticReconnect` daha fazla denetim istiyorsanız, '' adlı `IRetryPolicy` `nextRetryDelayInMilliseconds`tek bir yöntem ekibe uygulayan bir nesne kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9ca39-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="9ca39-210">`nextRetryDelayInMilliseconds`türü `RetryContext`ile tek bir bağımsız değişken alır.</span><span class="sxs-lookup"><span data-stu-id="9ca39-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="9ca39-211">Üç `RetryContext` `previousRetryCount`özelliği vardır: `elapsedMilliseconds` `retryReason` , ve `number`hangi `number` a `Error` , a ve bir sırasıyla.</span><span class="sxs-lookup"><span data-stu-id="9ca39-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="9ca39-212">İlk yeniden bağlanma denemesinden `previousRetryCount` `elapsedMilliseconds` önce, her ikisi `retryReason` de ve sıfır olacak ve bağlantının kaybolmasına neden olan Hata olacaktır.</span><span class="sxs-lookup"><span data-stu-id="9ca39-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="9ca39-213">Her başarısız yeniden deneme `previousRetryCount` denemesinden sonra, bir `elapsedMilliseconds` ile artımlı olacak, milisaniye cinsinden yeniden bağlanma için harcanan süreyi yansıtacak şekilde güncellenecektir ve son yeniden bağlanma denemesinin başarısız olması için hata `retryReason` olacaktır.</span><span class="sxs-lookup"><span data-stu-id="9ca39-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="9ca39-214">`nextRetryDelayInMilliseconds`bir sonraki yeniden bağlanma girişiminden önce beklemek için milisaniye sayısını `null` temsil `HubConnection` eden bir sayı döndürmesi veya yeniden bağlanmayı durdurması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9ca39-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="9ca39-215">Alternatif olarak, istemcinizi [el ile yeniden bağlayacak](#manually-reconnect)kod yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9ca39-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="9ca39-216">El ile yeniden bağlanma</span><span class="sxs-lookup"><span data-stu-id="9ca39-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="9ca39-217">3.0'dan önce, javascript SignalR istemcisi otomatik olarak yeniden bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="9ca39-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="9ca39-218">İstemcinizi el ile yeniden bağlayacak kod yazmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="9ca39-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="9ca39-219">Aşağıdaki kod tipik bir el ile yeniden bağlantı yaklaşımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="9ca39-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="9ca39-220">Bağlantıyı başlatmak için bir `start` işlev (bu durumda, işlev) oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9ca39-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="9ca39-221">Bağlantının `start` `onclose` olay işleyicisindeki işlevi çağırın.</span><span class="sxs-lookup"><span data-stu-id="9ca39-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="9ca39-222">Gerçek bir uygulama, vazgeçmeden önce üstel bir geri çekilme kullanır veya belirli sayıda yeniden dener.</span><span class="sxs-lookup"><span data-stu-id="9ca39-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9ca39-223">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9ca39-223">Additional resources</span></span>

* [<span data-ttu-id="9ca39-224">JavaScript API'si başvurusu</span><span class="sxs-lookup"><span data-stu-id="9ca39-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="9ca39-225">JavaScript öğretici</span><span class="sxs-lookup"><span data-stu-id="9ca39-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="9ca39-226">WebPack ve TypeScript öğretici</span><span class="sxs-lookup"><span data-stu-id="9ca39-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="9ca39-227">Hub'lar</span><span class="sxs-lookup"><span data-stu-id="9ca39-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="9ca39-228">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="9ca39-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="9ca39-229">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="9ca39-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="9ca39-230">Çapraz Orijin İstekleri (CORS)</span><span class="sxs-lookup"><span data-stu-id="9ca39-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="9ca39-231">[Azure SignalR Hizmeti sunucusuz belgeler](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="9ca39-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
