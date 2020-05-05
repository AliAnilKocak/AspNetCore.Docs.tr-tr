---
title: ASP.NET Core güvenlik konularıSignalR
author: bradygaster
description: ASP.NET Core SignalR'da kimlik doğrulama ve yetkilendirmeyi nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/security
ms.openlocfilehash: 2b049d9d8131c6c95b2f768620c984d0f67f92cc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775329"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a><span data-ttu-id="68a19-103">ASP.NET Core güvenlik konularıSignalR</span><span class="sxs-lookup"><span data-stu-id="68a19-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="68a19-104">, [Andrew Stanton-nurte](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="68a19-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="68a19-105">Bu makalede, güvenliğini sağlama SignalRhakkında bilgi sağlanır.</span><span class="sxs-lookup"><span data-stu-id="68a19-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="68a19-106">Çıkış noktaları arası kaynak paylaşma</span><span class="sxs-lookup"><span data-stu-id="68a19-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="68a19-107">[Çapraz kaynak kaynak paylaşımı (CORS)](https://www.w3.org/TR/cors/) , tarayıcıda çapraz kaynak SignalR bağlantılarına izin vermek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="68a19-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="68a19-108">JavaScript kodu SignalR uygulamadan farklı bir etki alanında barındırılıyorsa, JavaScript 'in SignalR uygulamaya bağlanmasına izin vermek için [CORS ara yazılımı](xref:security/cors) etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="68a19-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="68a19-109">Yalnızca güvendiğiniz veya denetlediğiniz etki alanlarından çıkış noktaları arası isteklere izin verin.</span><span class="sxs-lookup"><span data-stu-id="68a19-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="68a19-110">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="68a19-110">For example:</span></span>

* <span data-ttu-id="68a19-111">Siteniz üzerinde barındırılıyor`http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="68a19-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="68a19-112">SignalR Uygulamanız üzerinde barındırılıyor`http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="68a19-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="68a19-113">CORS, SignalR uygulamada yalnızca kaynağa `www.example.com`izin verecek şekilde yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="68a19-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="68a19-114">CORS 'yi yapılandırma hakkında daha fazla bilgi için bkz. [çıkış noktaları arası istekleri (CORS) etkinleştirme](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="68a19-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> SignalR<span data-ttu-id="68a19-115">Aşağıdaki CORS ilkelerini **gerektirir** :</span><span class="sxs-lookup"><span data-stu-id="68a19-115"> **requires** the following CORS policies:</span></span>

* <span data-ttu-id="68a19-116">Beklenen belirli kaynaklardan izin verin.</span><span class="sxs-lookup"><span data-stu-id="68a19-116">Allow the specific expected origins.</span></span> <span data-ttu-id="68a19-117">Herhangi bir kaynağa izin verilmesi mümkün olsa **da güvenli veya** önerilmez.</span><span class="sxs-lookup"><span data-stu-id="68a19-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="68a19-118">HTTP yöntemlerine `GET` ve `POST` izin verilmelidir.</span><span class="sxs-lookup"><span data-stu-id="68a19-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="68a19-119">Tanımlama bilgisi tabanlı yapışkan oturumların düzgün çalışması için kimlik bilgilerine izin verilmelidir.</span><span class="sxs-lookup"><span data-stu-id="68a19-119">Credentials must be allowed in order for cookie-based sticky sessions to work correctly.</span></span> <span data-ttu-id="68a19-120">Kimlik doğrulaması kullanılmasa bile etkinleştirilmeleri gerekir.</span><span class="sxs-lookup"><span data-stu-id="68a19-120">They must be enabled even when authentication isn't used.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="68a19-121">Ancak, 5,0 ' de, TypeScript istemcisinde kimlik bilgilerini kullanmayan bir seçenek sağladık.</span><span class="sxs-lookup"><span data-stu-id="68a19-121">However, in 5.0 we have provided an option in the TypeScript client to not use credentials.</span></span>
<span data-ttu-id="68a19-122">Kimlik bilgilerini kullanma seçeneği yalnızca %100 kimlik bilgileri gibi kimlik bilgilerinin uygulamanızda gerekli olmadığını (örneğin, yapışkan oturumlar için birden çok sunucu kullanılırken Azure App Service tarafından kullanılır) bilmeniz durumunda kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="68a19-122">The option to not use credentials should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers for sticky sessions).</span></span>

::: moniker-end

<span data-ttu-id="68a19-123">Örneğin, SignalR aşağıdaki CORS ilkesi üzerinde barındırılan bir tarayıcı istemcisinin `https://example.com` üzerinde barındırılan SignalR uygulamaya erişmesine izin verir: `https://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="68a19-123">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a><span data-ttu-id="68a19-124">WebSocket kaynak kısıtlaması</span><span class="sxs-lookup"><span data-stu-id="68a19-124">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="68a19-125">CORS tarafından sunulan korumalar WebSockets için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="68a19-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="68a19-126">WebSockets üzerindeki kaynak kısıtlaması için [WebSockets kaynak kısıtlaması](xref:fundamentals/websockets#websocket-origin-restriction)' nı okuyun.</span><span class="sxs-lookup"><span data-stu-id="68a19-126">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="68a19-127">CORS tarafından sunulan korumalar WebSockets için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="68a19-127">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="68a19-128">Tarayıcılar şunları **desteklemez**:</span><span class="sxs-lookup"><span data-stu-id="68a19-128">Browsers do **not**:</span></span>

* <span data-ttu-id="68a19-129">CORS ön uçuş istekleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="68a19-129">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="68a19-130">WebSocket istekleri yapılırken `Access-Control` üst bilgilerde belirtilen kısıtlamalara saygı.</span><span class="sxs-lookup"><span data-stu-id="68a19-130">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="68a19-131">Ancak, tarayıcılar WebSocket istekleri verirken `Origin` üstbilgiyi gönderir.</span><span class="sxs-lookup"><span data-stu-id="68a19-131">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="68a19-132">Yalnızca beklenen kaynaklardan gelen WebSockets izin verildiğinden emin olmak için uygulamalar bu üstbilgileri doğrulamak üzere yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="68a19-132">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="68a19-133">ASP.NET Core 2,1 ve üzeri sürümlerde, daha önce `Configure` \*\* `UseSignalR`\*\* yerleştirilmiş özel bir ara yazılım ve içindeki kimlik doğrulama ara yazılımı kullanılarak üst bilgi doğrulaması elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="68a19-133">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="68a19-134">`Origin` Üst bilgi istemci tarafından denetlenir ve `Referer` üst bilgi gibi erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="68a19-134">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="68a19-135">Bu üst bilgiler bir kimlik doğrulama mekanizması **olarak kullanılmamalıdır.**</span><span class="sxs-lookup"><span data-stu-id="68a19-135">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="68a19-136">ConnectionID</span><span class="sxs-lookup"><span data-stu-id="68a19-136">ConnectionId</span></span>

<span data-ttu-id="68a19-137">Sunucu veya istemci ASP.NET Core sürümü 2,2 veya daha önceki bir sürümdeyse, kullanıma sunma `ConnectionId` kötü amaçlı kimliğe bürünmeye yol açabilir. SignalR</span><span class="sxs-lookup"><span data-stu-id="68a19-137">Exposing `ConnectionId` can lead to malicious impersonation if the SignalR server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="68a19-138">SignalR Sunucu ve istemci sürümü ASP.NET Core 3,0 veya üzeri ise, `ConnectionToken` bunun yerine gizli tutulması `ConnectionId` gerekir.</span><span class="sxs-lookup"><span data-stu-id="68a19-138">If the SignalR server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="68a19-139">, `ConnectionToken` Özellikle HERHANGI bir API 'de gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="68a19-139">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="68a19-140">Eski SignalR istemcilerin sunucuya bağlanmadığından emin olmak zor olabilir, bu nedenle SignalR sunucu sürümünüz ASP.NET Core 3,0 veya sonraki bir sürümse bile `ConnectionId` gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="68a19-140">It can be difficult to ensure that older SignalR clients aren't connecting to the server, so even if your SignalR server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="68a19-141">Erişim belirteci günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="68a19-141">Access token logging</span></span>

<span data-ttu-id="68a19-142">WebSockets veya sunucu tarafından gönderilen olaylar kullanılırken, tarayıcı istemcisi erişim belirtecini sorgu dizesinde gönderir.</span><span class="sxs-lookup"><span data-stu-id="68a19-142">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="68a19-143">Sorgu dizesi aracılığıyla erişim belirtecinin alınması genellikle standart `Authorization` üst bilgi kullanılarak güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="68a19-143">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="68a19-144">İstemci ve sunucu arasında güvenli bir uçtan uca bağlantı sağlamak için her zaman HTTPS kullanın.</span><span class="sxs-lookup"><span data-stu-id="68a19-144">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="68a19-145">Birçok Web sunucusu, sorgu dizesi dahil olmak üzere her bir isteğin URL 'sini günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="68a19-145">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="68a19-146">URL 'Leri günlüğe kaydetme erişim belirtecini günlüğe alabilir.</span><span class="sxs-lookup"><span data-stu-id="68a19-146">Logging the URLs may log the access token.</span></span> <span data-ttu-id="68a19-147">ASP.NET Core, her isteğin URL 'sini varsayılan olarak günlüğe kaydeder ve bu sorgu dizesini içerir.</span><span class="sxs-lookup"><span data-stu-id="68a19-147">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="68a19-148">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="68a19-148">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="68a19-149">Bu verilerin sunucu günlüklerinizi günlüğe kaydetmekle ilgili endişeleriniz varsa, `Microsoft.AspNetCore.Hosting` günlükçü 'yi `Warning` düzeye veya yukarıya yapılandırarak (Bu iletiler `Info` düzeyinde yazılır) bu günlüğü tamamen devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="68a19-149">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="68a19-150">Daha fazla bilgi için bkz. [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) .</span><span class="sxs-lookup"><span data-stu-id="68a19-150">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="68a19-151">Hala belirli istek bilgilerini günlüğe kaydetmek istiyorsanız, ihtiyacınız olan verileri günlüğe kaydetmek için [bir ara yazılım yazabilir](xref:fundamentals/middleware/write) ve `access_token` sorgu dizesi değerini (varsa) filtreleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="68a19-151">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="68a19-152">Özel durumlar</span><span class="sxs-lookup"><span data-stu-id="68a19-152">Exceptions</span></span>

<span data-ttu-id="68a19-153">Özel durum iletileri genellikle bir istemciye görüntülenmemelidir gizli veriler olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="68a19-153">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="68a19-154">Varsayılan olarak, SignalR bir hub yöntemi tarafından istemciye oluşturulan bir özel durumun ayrıntılarını istemciye göndermez.</span><span class="sxs-lookup"><span data-stu-id="68a19-154">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="68a19-155">Bunun yerine, istemci bir hata oluştuğunu belirten genel bir ileti alır.</span><span class="sxs-lookup"><span data-stu-id="68a19-155">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="68a19-156">İstemciye özel durum iletisi teslimi, [Enabledetailederrors](xref:signalr/configuration#configure-server-options)ile geçersiz kılınabilir (örneğin, geliştirme veya test).</span><span class="sxs-lookup"><span data-stu-id="68a19-156">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="68a19-157">Özel durum iletileri, üretim uygulamalarındaki istemciye gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="68a19-157">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="68a19-158">Arabellek Yönetimi</span><span class="sxs-lookup"><span data-stu-id="68a19-158">Buffer management</span></span>

SignalR<span data-ttu-id="68a19-159">gelen ve giden iletileri yönetmek için bağlantı başına arabellekleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="68a19-159"> uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="68a19-160">Varsayılan olarak, SignalR bu ARABELLEKLERI 32 KB ile sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="68a19-160">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="68a19-161">Bir istemcinin veya sunucunun gönderecan en büyük ileti 32 KB 'tır.</span><span class="sxs-lookup"><span data-stu-id="68a19-161">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="68a19-162">İleti bağlantısı tarafından tüketilen maksimum bellek 32 KB 'tır.</span><span class="sxs-lookup"><span data-stu-id="68a19-162">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="68a19-163">İletileriniz her zaman 32 KB 'den küçükse, sınırı azaltabilirsiniz ve şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="68a19-163">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="68a19-164">Bir istemcinin daha büyük bir ileti gönderebilmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="68a19-164">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="68a19-165">Sunucu, iletileri kabul etmek için hiçbir şekilde büyük arabellekler ayırmayı gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="68a19-165">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="68a19-166">İletileriniz 32 KB 'tan büyükse, limiti artırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="68a19-166">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="68a19-167">Bu sınırı artırmak şu anlama gelir:</span><span class="sxs-lookup"><span data-stu-id="68a19-167">Increasing this limit means:</span></span>

* <span data-ttu-id="68a19-168">İstemci, sunucunun büyük bellek arabellekleri ayırmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="68a19-168">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="68a19-169">Büyük arabelleklerin sunucu ayırması, eşzamanlı bağlantı sayısını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="68a19-169">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="68a19-170">Gelen ve giden iletiler için, her ikisi de yapılandırılan [Httpconnectiondispatcheroptions](xref:signalr/configuration#configure-server-options) nesnesinde yapılandırılabilir `MapHub`:</span><span class="sxs-lookup"><span data-stu-id="68a19-170">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="68a19-171">`ApplicationMaxBufferSize`istemciden sunucunun arabelleğe aldığı en fazla bayt sayısını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="68a19-171">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="68a19-172">İstemci bu sınırdan daha büyük bir ileti göndermemeyi denerse bağlantı kapatılabilir.</span><span class="sxs-lookup"><span data-stu-id="68a19-172">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="68a19-173">`TransportMaxBufferSize`sunucunun gönderemediği en fazla bayt sayısını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="68a19-173">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="68a19-174">Sunucu, bu sınırdan daha büyük bir ileti (hub metotlarından dönüş değerleri dahil) gönderilmeye çalışırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="68a19-174">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="68a19-175">Limit ayarı sınırı `0` devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="68a19-175">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="68a19-176">Sınırı kaldırmak, bir istemcinin herhangi bir boyutta ileti göndermesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="68a19-176">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="68a19-177">Büyük iletiler gönderen kötü amaçlı istemciler fazla belleğin ayrılmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="68a19-177">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="68a19-178">Aşırı bellek kullanımı, eşzamanlı bağlantı sayısını önemli ölçüde azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="68a19-178">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
