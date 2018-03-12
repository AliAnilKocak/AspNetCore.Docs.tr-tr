---
title: "ASP.NET Core üzerinde SignalR giriş"
author: rachelappel
description: "Uygulamalar için gerçek zamanlı web işlevselliği ekleme ASP.NET Core SignalR kitaplığı nasıl basitleştirir öğrenin."
manager: wpickett
ms.author: rachelap
ms.custom: mvc
ms.date: 03/07/2018
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: signalr/introduction-signalr-core
ms.openlocfilehash: d4ad9bb1910a3339ac8d0d8ff740417f4e7262b7
ms.sourcegitcommit: 9622bdc6326c28c3322c70000468a80ef21ad376
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/12/2018
---
# <a name="introduction-to-signalr"></a><span data-ttu-id="6efac-103">SignalR giriş</span><span class="sxs-lookup"><span data-stu-id="6efac-103">Introduction to SignalR</span></span>

<span data-ttu-id="6efac-104">Tarafından [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="6efac-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

## <a name="what-is-signalr"></a><span data-ttu-id="6efac-105">SignalR nedir?</span><span class="sxs-lookup"><span data-stu-id="6efac-105">What is SignalR?</span></span>

<span data-ttu-id="6efac-106">ASP.NET Core SignalR uygulamalarını ekleme gerçek zamanlı web işlevselliği basitleştiren bir kitaplıktır.</span><span class="sxs-lookup"><span data-stu-id="6efac-106">ASP.NET Core SignalR is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="6efac-107">Gerçek zamanlı web işlevselliği sunucu tarafı kodu anında içeriği istemcilere hemen sağlar.</span><span class="sxs-lookup"><span data-stu-id="6efac-107">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="6efac-108">SignalR için iyi adaylar:</span><span class="sxs-lookup"><span data-stu-id="6efac-108">Good candidates for SignalR:</span></span>

* <span data-ttu-id="6efac-109">Yüksek yoğunlukta güncelleştirmeleri sunucudan gerektiren uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="6efac-109">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="6efac-110">Örnek oyunlara, sosyal ağlara, oylama, artırma, eşlemeleri ve GPS uygulamaları verilebilir.</span><span class="sxs-lookup"><span data-stu-id="6efac-110">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="6efac-111">Panolar ve izleme uygulamaları.</span><span class="sxs-lookup"><span data-stu-id="6efac-111">Dashboards and monitoring apps.</span></span> <span data-ttu-id="6efac-112">Örnek şirket panoları, anlık satış güncelleştirmeleri dahil veya uyarıları seyahat.</span><span class="sxs-lookup"><span data-stu-id="6efac-112">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="6efac-113">İşbirlikçi uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="6efac-113">Collaborative apps.</span></span> <span data-ttu-id="6efac-114">Beyaz Tahta uygulamaları ve yazılım toplantı takım işbirliği uygulamalara örnektir.</span><span class="sxs-lookup"><span data-stu-id="6efac-114">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="6efac-115">Bildirimleri gerektiren uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="6efac-115">Apps that require notifications.</span></span> <span data-ttu-id="6efac-116">Sosyal ağlar, e-posta, sohbet, oyunlar, seyahat uyarıları ve diğer birçok uygulama bildirimleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="6efac-116">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="6efac-117">SignalR server istemcisi oluşturmak için bir API sağlar [uzaktan yordam çağrısı (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span><span class="sxs-lookup"><span data-stu-id="6efac-117">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="6efac-118">RPC'ler JavaScript işlevlerini sunucu tarafı .NET Core koddan istemcilerde çağırın.</span><span class="sxs-lookup"><span data-stu-id="6efac-118">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="6efac-119">SignalR ASP.NET Core için:</span><span class="sxs-lookup"><span data-stu-id="6efac-119">SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="6efac-120">Bağlantı Yönetimi otomatik olarak yönetir.</span><span class="sxs-lookup"><span data-stu-id="6efac-120">Handles connection management automatically.</span></span>
* <span data-ttu-id="6efac-121">İletileri aynı anda bağlanan tüm istemciler için yayın etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="6efac-121">Enables broadcasting messages to all connected clients simultaneously.</span></span> <span data-ttu-id="6efac-122">Sohbet yer.</span><span class="sxs-lookup"><span data-stu-id="6efac-122">For example, a chat room.</span></span>
* <span data-ttu-id="6efac-123">Belirli istemciler veya istemci gruplarının ileti gönderilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6efac-123">Enables sending messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="6efac-124">Açık kaynaklıdır konumunda olduğundan [GitHub](https://github.com/aspnet/signalr).</span><span class="sxs-lookup"><span data-stu-id="6efac-124">Is open-sourced at [GitHub](https://github.com/aspnet/signalr).</span></span>
* <span data-ttu-id="6efac-125">Sorunsuz şekilde ölçeklendirir.</span><span class="sxs-lookup"><span data-stu-id="6efac-125">Scales nicely.</span></span>

<span data-ttu-id="6efac-126">Bir HTTP bağlantısı farklı olarak istemci ve sunucu arasındaki bağlantının kalıcıdır.</span><span class="sxs-lookup"><span data-stu-id="6efac-126">The connection between the client and server is persistent, unlike an HTTP connection.</span></span>

## <a name="transports"></a><span data-ttu-id="6efac-127">Taşımalar</span><span class="sxs-lookup"><span data-stu-id="6efac-127">Transports</span></span>

<span data-ttu-id="6efac-128">Gerçek zamanlı web uygulamaları oluşturmak için teknikleri sayısı üzerinden SignalR özetleri.</span><span class="sxs-lookup"><span data-stu-id="6efac-128">SignalR abstracts over a number of techniques for building real-time web applications.</span></span> <span data-ttu-id="6efac-129">[WebSockets](https://tools.ietf.org/html/rfc7118) en iyi Aktarım, ancak bunlar kullanılamaz Server-Sent olayları ve uzun yoklama gibi başka teknikler kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6efac-129">[WebSockets](https://tools.ietf.org/html/rfc7118) is the optimal transport, but other techniques like Server-Sent Events and Long Polling can be used when those aren't available.</span></span> <span data-ttu-id="6efac-130">SignalR otomatik olarak algılar ve sunucu ve istemci üzerinde desteklenen özelliklere bağlı olarak uygun bir taşıma başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="6efac-130">SignalR will automatically detect and initialize the appropriate transport based on features supported on the server and client.</span></span>

## <a name="hubs-and-endpoints"></a><span data-ttu-id="6efac-131">Hub ve uç noktaları</span><span class="sxs-lookup"><span data-stu-id="6efac-131">Hubs and Endpoints</span></span>

<span data-ttu-id="6efac-132">SignalR hub'ları ve uç noktaları istemciler ve sunucular arasında iletişim kurmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="6efac-132">SignalR uses Hubs and Endpoints to communicate between clients and servers.</span></span> <span data-ttu-id="6efac-133">Hub API çoğu senaryolar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="6efac-133">The Hubs API covers the most scenarios.</span></span>

<span data-ttu-id="6efac-134">Bir hub istemci ve sunucu birbirine yöntemlerini çağırmaya veren bir uç nokta API üzerine inşa üst düzey bir işlem hattı ' dir.</span><span class="sxs-lookup"><span data-stu-id="6efac-134">A hub is a high-level pipeline built upon the Endpoint API that allows your client and server to call methods on each other.</span></span> <span data-ttu-id="6efac-135">SignalR makine sınırları içinde otomatik olarak göndermeyi kolayca yerel yöntemleri ve tersi olarak sunucuda yöntemlerini çağırmaya istemcilere izin verme işler.</span><span class="sxs-lookup"><span data-stu-id="6efac-135">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server as easily as local methods, and vice versa.</span></span> <span data-ttu-id="6efac-136">Hub yöntemleri için kesin tür belirtilmiş parametreleri geçirme model bağlama sağlayan izin verir.</span><span class="sxs-lookup"><span data-stu-id="6efac-136">Hubs allow passing strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="6efac-137">SignalR iki yerleşik hub protokol sağlar: bir metin protokolü temel JSON ve temel bir ikili protokol [MessagePack](https://msgpack.org/).</span><span class="sxs-lookup"><span data-stu-id="6efac-137">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="6efac-138">MessagePack genellikle JSON kullanırken daha küçük ileti oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6efac-138">MessagePack generally creates smaller messages than when using JSON.</span></span> <span data-ttu-id="6efac-139">Eski tarayıcılar desteklemelidir [XHR Düzey 2](https://caniuse.com/#feat=xhr2) MessagePack protokolü desteği sağlamak için.</span><span class="sxs-lookup"><span data-stu-id="6efac-139">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="6efac-140">Hub etkin taşımanın kullanarak iletiler göndererek istemci-tarafı kodu çağırma.</span><span class="sxs-lookup"><span data-stu-id="6efac-140">Hubs call client-side code by sending messages using the active transport.</span></span> <span data-ttu-id="6efac-141">İletileri adını ve istemci tarafı yönteminin parametrelerini içerir.</span><span class="sxs-lookup"><span data-stu-id="6efac-141">The messages contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="6efac-142">Nesneleri yöntem parametreleri, yapılandırılmış protokolü kullanılarak seri durumdan olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6efac-142">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="6efac-143">İstemci, istemci tarafı kodlar yönteminde adına eşleştirmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="6efac-143">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="6efac-144">Bir eşleşme gerçekleştiğinde, istemci yöntemi seri durumdan çıkarılmış parametre veri kullanarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="6efac-144">When a match happens, the client method runs using the deserialized parameter data.</span></span>

<span data-ttu-id="6efac-145">Uç noktaları istemciden okumasına ve yazmasına etkinleştirmeden bir ham yuva benzeri API sağlar.</span><span class="sxs-lookup"><span data-stu-id="6efac-145">Endpoints provide a raw socket-like API, enabling them to read and write from the client.</span></span> <span data-ttu-id="6efac-146">Bu gruplandırma, yayın ve diğer işlevleri işlemek için geliştiriciler için hazır.</span><span class="sxs-lookup"><span data-stu-id="6efac-146">It's up to the developer to handle grouping, broadcasting, and other functions.</span></span> <span data-ttu-id="6efac-147">Hub API uç noktaları katman üzerinde oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="6efac-147">The Hubs API is built on top of the Endpoints layer.</span></span>

<span data-ttu-id="6efac-148">Aşağıdaki diyagramda hub, uç noktaları ve istemciler arasındaki ilişkiyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="6efac-148">The following diagram shows the relationship between hubs, endpoints, and clients.</span></span>

![SignalR eşleme](introduction-signalr-core/_static/signalr-core-architecture.png)

## <a name="related-resources"></a><span data-ttu-id="6efac-150">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6efac-150">Related resources</span></span>

[<span data-ttu-id="6efac-151">ASP.NET Core için SignalR ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="6efac-151">Get started with SignalR for ASP.NET Core</span></span>](xref:signalr/get-started-signalr-core)
