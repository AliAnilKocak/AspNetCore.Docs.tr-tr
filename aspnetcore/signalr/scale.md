---
title: ASP.NET Core SignalR Üretim barındırma ve ölçeklendirme
author: bradygaster
description: ASP.NET Core kullanan uygulamalardaki performans ve ölçeklendirme sorunlarının nasıl önleneceğini öğrenin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/scale
ms.openlocfilehash: 2bfe05748e6740043be7f1ccc6dbe22ad4b0ca44
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722572"
---
# <a name="aspnet-core-no-locsignalr-hosting-and-scaling"></a><span data-ttu-id="93ef2-103">SignalRBarındırma ve ölçeklendirmeyi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="93ef2-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="93ef2-104">, [Andrew Stanton-nuri](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)ve [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="93ef2-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="93ef2-105">Bu makalede, ASP.NET Core kullanan yüksek trafik uygulamalarına yönelik barındırma ve ölçeklendirme konuları açıklanmaktadır SignalR .</span><span class="sxs-lookup"><span data-stu-id="93ef2-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="93ef2-106">Yapışkan oturumlar</span><span class="sxs-lookup"><span data-stu-id="93ef2-106">Sticky Sessions</span></span>

<span data-ttu-id="93ef2-107">SignalR belirli bir bağlantı için tüm HTTP isteklerinin aynı sunucu işlemi tarafından işlenmesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-107">SignalR requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="93ef2-108">SignalRBir sunucu grubunda (birden çok sunucu) çalışırken, "yapışkan oturumlar" kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="93ef2-109">"Yapışkan oturumlar", bazı yük dengeleyiciler tarafından oturum benzeşimi olarak da adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="93ef2-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="93ef2-110">Azure App Service istekleri yönlendirmek için [uygulama Isteği yönlendirme](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) kullanır.</span><span class="sxs-lookup"><span data-stu-id="93ef2-110">Azure App Service uses [Application Request Routing](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="93ef2-111">Azure App Service "ARR benzeşimi" ayarının etkinleştirilmesi "yapışkan oturumlar" sağlayacaktır.</span><span class="sxs-lookup"><span data-stu-id="93ef2-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="93ef2-112">Yapışkan oturumların gerekmediği tek koşullar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="93ef2-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="93ef2-113">Tek bir sunucuda barındırırken, tek bir işlemde.</span><span class="sxs-lookup"><span data-stu-id="93ef2-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="93ef2-114">Azure SignalR hizmetini kullanırken.</span><span class="sxs-lookup"><span data-stu-id="93ef2-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="93ef2-115">Tüm istemciler **yalnızca** WebSockets kullanacak şekilde yapılandırıldığında **ve** istemci yapılandırmasında [skipanlaşma ayarı](xref:signalr/configuration#configure-additional-options) etkinse.</span><span class="sxs-lookup"><span data-stu-id="93ef2-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="93ef2-116">Tüm diğer koşullarda (Redsıs geri düzlemi kullanıldığında dahil), sunucu ortamının yapışkan oturumlar için yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="93ef2-117">İçin Azure App Service yapılandırma kılavuzu için SignalR , bkz <xref:signalr/publish-to-azure-web-app> ..</span><span class="sxs-lookup"><span data-stu-id="93ef2-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="93ef2-118">TCP bağlantı kaynakları</span><span class="sxs-lookup"><span data-stu-id="93ef2-118">TCP connection resources</span></span>

<span data-ttu-id="93ef2-119">Bir Web sunucusunun destekleyebileceği eşzamanlı TCP bağlantısı sayısı sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="93ef2-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="93ef2-120">Standart HTTP istemcileri, *kısa ömürlü* bağlantıları kullanır.</span><span class="sxs-lookup"><span data-stu-id="93ef2-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="93ef2-121">Bu bağlantılar, istemci boşta kaldığında ve daha sonra yeniden açıldığı zaman kapatılabilir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="93ef2-122">Öte yandan bir SignalR bağlantı *kalıcıdır*.</span><span class="sxs-lookup"><span data-stu-id="93ef2-122">On the other hand, a SignalR connection is *persistent*.</span></span> <span data-ttu-id="93ef2-123">SignalR istemci boşta kaldığında bile bağlantılar açık kalır.</span><span class="sxs-lookup"><span data-stu-id="93ef2-123">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="93ef2-124">Çok sayıda istemciye hizmet veren yüksek trafikli bir uygulamada, bu kalıcı bağlantılar sunucuların en fazla bağlantı sayısına ulaşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="93ef2-125">Kalıcı bağlantılar, her bağlantıyı izlemek için ek bellek de tüketir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="93ef2-126">İle bağlantıyla ilgili kaynakların ağır kullanımı, SignalR aynı sunucuda barındırılan diğer Web uygulamalarını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="93ef2-127">SignalR, Ve en son KULLANILABILIR TCP bağlantılarını tutuyorsa, aynı sunucudaki diğer Web uygulamalarına da daha fazla bağlantı bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="93ef2-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="93ef2-128">Sunucuda bağlantı biterse rastgele yuva hataları ve bağlantı sıfırlama hataları görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="93ef2-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="93ef2-129">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="93ef2-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="93ef2-130">SignalRKaynak kullanımının diğer Web uygulamalarındaki hatalara neden olmasına devam etmek için SignalR diğer Web uygulamalarınızdan farklı sunucular üzerinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="93ef2-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="93ef2-131">SignalRKaynak kullanımını bir uygulamadaki hatalara neden olmasına devam etmek için SignalR , bir sunucunun işlemesi olan bağlantı sayısını sınırlamak üzere ölçeği ölçeklendirin.</span><span class="sxs-lookup"><span data-stu-id="93ef2-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="93ef2-132">Ölçeği genişletme</span><span class="sxs-lookup"><span data-stu-id="93ef2-132">Scale out</span></span>

<span data-ttu-id="93ef2-133">Kullanan bir uygulamanın SignalR , bir sunucu grubu için sorunlar oluşturan tüm bağlantılarını izlemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="93ef2-134">Bir sunucu ekleyin ve diğer sunucuların hakkında bilgi sahibi olmadığı yeni bağlantıları alır.</span><span class="sxs-lookup"><span data-stu-id="93ef2-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="93ef2-135">Örneğin, SignalR aşağıdaki diyagramdaki her bir sunucuda diğer sunuculardaki bağlantılar farkında değildir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="93ef2-136">SignalRSunuculardan birinde tüm istemcilere ileti göndermek istediğinde ileti yalnızca bu sunucuya bağlı olan istemcilere gider.</span><span class="sxs-lookup"><span data-stu-id="93ef2-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![Ölçeklendirme::: No-Loc (SignalR)::: geri düzlemi olmadan](scale/_static/scale-no-backplane.png)

<span data-ttu-id="93ef2-138">Bu sorunu çözmeye yönelik seçenekler [Azure SignalR hizmeti](#azure-signalr-service) ve [Redsıs arkadüzledir](#redis-backplane).</span><span class="sxs-lookup"><span data-stu-id="93ef2-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-no-locsignalr-service"></a><span data-ttu-id="93ef2-139">Azure SignalR hizmeti</span><span class="sxs-lookup"><span data-stu-id="93ef2-139">Azure SignalR Service</span></span>

<span data-ttu-id="93ef2-140">Azure SignalR hizmeti, arka düzlemi yerine bir ara sunucu.</span><span class="sxs-lookup"><span data-stu-id="93ef2-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="93ef2-141">İstemci sunucuya bir bağlantı başlattığında, istemci hizmete bağlanmak için yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="93ef2-142">Bu işlem aşağıdaki diyagramda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="93ef2-142">That process is illustrated in the following diagram:</span></span>

![Azure::: No-Loc (SignalR)::: Service bağlantısı kuruluyor](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="93ef2-144">Sonuç olarak, hizmetin tüm istemci bağlantılarını yönetmesi, her sunucunun aşağıdaki diyagramda gösterildiği gibi yalnızca küçük bir sabit bağlantı sayısına ihtiyacı vardır:</span><span class="sxs-lookup"><span data-stu-id="93ef2-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![Hizmete bağlı istemciler, hizmete bağlı sunucular](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="93ef2-146">Bu genişleme yaklaşımına yönelik bu yaklaşım, Redsıs geri düzlemi 'nin diğer avantajlarından biridir:</span><span class="sxs-lookup"><span data-stu-id="93ef2-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="93ef2-147">[İstemci benzeşimi](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)olarak da bilinen yapışkan oturumlar gerekli değildir, çünkü Istemciler bağlandıklarında Azure hizmetine anında yönlendirilir SignalR .</span><span class="sxs-lookup"><span data-stu-id="93ef2-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="93ef2-148">SignalRAzure SignalR hizmeti herhangi bir sayıda bağlantıyı işleyecek şekilde ölçeklenirken, bir uygulama gönderilen ileti sayısına göre ölçeklendirebilir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service scales to handle any number of connections.</span></span> <span data-ttu-id="93ef2-149">Örneğin, binlerce istemci olabilir, ancak saniye başına yalnızca birkaç ileti gönderiliyorsa, SignalR uygulamanın yalnızca bağlantıları işlemek için birden çok sunucuya ölçeklendirilmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="93ef2-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="93ef2-150">Bir SignalR uygulama, olmadan bir Web uygulamasından çok daha fazla bağlantı kaynağı kullanmaz SignalR .</span><span class="sxs-lookup"><span data-stu-id="93ef2-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="93ef2-151">Bu nedenlerden dolayı, SignalR SignalR App Service, VM 'ler ve kapsayıcılar dahil olmak üzere Azure 'da barındırılan tüm ASP.NET Core uygulamaları için Azure hizmetini öneririz.</span><span class="sxs-lookup"><span data-stu-id="93ef2-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="93ef2-152">Daha fazla bilgi için bkz. [Azure SignalR hizmeti belgeleri](/azure/azure-signalr/signalr-overview).</span><span class="sxs-lookup"><span data-stu-id="93ef2-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="93ef2-153">Redis kartı</span><span class="sxs-lookup"><span data-stu-id="93ef2-153">Redis backplane</span></span>

<span data-ttu-id="93ef2-154">[Redsıs](https://redis.io/) , bir yayımlama/abonelik modeliyle bir mesajlaşma sistemini destekleyen bellek içi anahtar-değer deposudur.</span><span class="sxs-lookup"><span data-stu-id="93ef2-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="93ef2-155">SignalRRedsıs geri düzlemi, iletileri diğer sunuculara iletmek için pub/Sub özelliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="93ef2-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="93ef2-156">İstemci bir bağlantı yaptığında, bağlantı bilgileri geri düzleme geçirilir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="93ef2-157">Bir sunucu tüm istemcilere ileti göndermek istediğinde, bu geri düzlemi gönderir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="93ef2-158">Biriktirme listesi, tüm bağlı istemcileri ve bunların hangi sunuculara olduğunu bilir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="93ef2-159">İletiyi ilgili sunucuları aracılığıyla tüm istemcilere gönderir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="93ef2-160">Bu işlem aşağıdaki diyagramda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="93ef2-160">This process is illustrated in the following diagram:</span></span>

![Redsıs geri düzlemi, bir sunucudan tüm istemcilere gönderilen ileti](scale/_static/redis-backplane.png)

<span data-ttu-id="93ef2-162">Redsıs geri düzlemi, kendi altyapınızda barındırılan uygulamalar için önerilen genişleme yaklaşımıdır.</span><span class="sxs-lookup"><span data-stu-id="93ef2-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="93ef2-163">Veri merkezinizde bir Azure veri merkezi arasında önemli bir bağlantı gecikmesi varsa, Azure SignalR hizmeti düşük gecikme süresi veya yüksek performans gereksinimlerine sahip şirket içi uygulamalar için pratik bir seçenek olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="93ef2-164">SignalRDaha önce bahsedilen Azure hizmeti avantajları redsıs geri düzlemi için dezavantajlardır:</span><span class="sxs-lookup"><span data-stu-id="93ef2-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="93ef2-165">[İstemci benzeşimi](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)olarak da bilinen yapışkan oturumlar, aşağıdakilerin **her ikisi** de doğru olduğunda gereklidir:</span><span class="sxs-lookup"><span data-stu-id="93ef2-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="93ef2-166">Tüm istemciler **yalnızca** WebSockets kullanacak şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="93ef2-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="93ef2-167">[Skipanlaşma ayarı](xref:signalr/configuration#configure-additional-options) istemci yapılandırmasında etkindir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="93ef2-168">Sunucuda bir bağlantı başlatıldıktan sonra bağlantı o sunucuda kalmaya devam etmek zorunda kalır.</span><span class="sxs-lookup"><span data-stu-id="93ef2-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="93ef2-169">SignalRBirkaç ileti gönderilse bile, bir uygulamanın, istemci sayısına göre ölçeği ölçeklendirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="93ef2-170">Bir SignalR uygulama, olmadan bir Web uygulamasından çok daha fazla bağlantı kaynağı kullanır SignalR .</span><span class="sxs-lookup"><span data-stu-id="93ef2-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="93ef2-171">Windows istemci işletim sisteminde IIS sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="93ef2-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="93ef2-172">Windows 10 ve Windows 8. x istemci işletim sistemleridir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="93ef2-173">İstemci işletim sistemlerindeki IIS, 10 eşzamanlı bağlantı sınırına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="93ef2-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> <span data-ttu-id="93ef2-174">SignalRbağlantıları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="93ef2-174">SignalR's connections are:</span></span>

* <span data-ttu-id="93ef2-175">Geçici ve sık yeniden oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="93ef2-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="93ef2-176">Artık **kullanılmadıysa** hemen atılamaz.</span><span class="sxs-lookup"><span data-stu-id="93ef2-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="93ef2-177">Yukarıdaki koşullar, istemci işletim sistemi üzerindeki 10 bağlantı sınırına ulaşmaları olasılığını ortadan alır.</span><span class="sxs-lookup"><span data-stu-id="93ef2-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="93ef2-178">Geliştirme için bir istemci işletim sistemi kullanıldığında şunları yapmanızı öneririz:</span><span class="sxs-lookup"><span data-stu-id="93ef2-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="93ef2-179">IIS kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="93ef2-179">Avoid IIS.</span></span>
* <span data-ttu-id="93ef2-180">Dağıtım hedefleri olarak Kestrel veya IIS Express kullanın.</span><span class="sxs-lookup"><span data-stu-id="93ef2-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="93ef2-181">Nginx ile Linux</span><span class="sxs-lookup"><span data-stu-id="93ef2-181">Linux with Nginx</span></span>

<span data-ttu-id="93ef2-182">Proxy 'nin `Connection` ve `Upgrade` üst bilgilerini WebSockets için aşağıdaki şekilde ayarlayın SignalR :</span><span class="sxs-lookup"><span data-stu-id="93ef2-182">Set the proxy's `Connection` and `Upgrade` headers to the following for SignalR WebSockets:</span></span>

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

<span data-ttu-id="93ef2-183">Daha fazla bilgi için bkz. [WebSocket proxy 'si olarak NGINX](https://www.nginx.com/blog/websocket-nginx/).</span><span class="sxs-lookup"><span data-stu-id="93ef2-183">For more information, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/).</span></span>

## <a name="third-party-no-locsignalr-backplane-providers"></a><span data-ttu-id="93ef2-184">Üçüncü taraf SignalR arka düzlem sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="93ef2-184">Third-party SignalR backplane providers</span></span>

* [<span data-ttu-id="93ef2-185">NCache</span><span class="sxs-lookup"><span data-stu-id="93ef2-185">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="93ef2-186">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span><span class="sxs-lookup"><span data-stu-id="93ef2-186">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span></span>

## <a name="next-steps"></a><span data-ttu-id="93ef2-187">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="93ef2-187">Next steps</span></span>

<span data-ttu-id="93ef2-188">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="93ef2-188">For more information, see the following resources:</span></span>

* [<span data-ttu-id="93ef2-189">Azure SignalR hizmeti belgeleri</span><span class="sxs-lookup"><span data-stu-id="93ef2-189">Azure SignalR Service documentation</span></span>](/azure/azure-signalr/signalr-overview)
* [<span data-ttu-id="93ef2-190">Redsıs geri düzlemi ayarlama</span><span class="sxs-lookup"><span data-stu-id="93ef2-190">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)