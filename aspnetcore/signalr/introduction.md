---
title: ASP.NET Core SignalR giriş
author: bradygaster
description: ASP.NET Core SignalR kitaplığının uygulamalara gerçek zamanlı işlevsellik eklemeyi nasıl basitleştireceğinizi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- SignalR
uid: signalr/introduction
ms.openlocfilehash: e84dd0d086cbfc80a80bc10baa33979da9b5d137
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717240"
---
# <a name="introduction-to-aspnet-core-opno-locsignalr"></a><span data-ttu-id="a13f3-103">ASP.NET Core SignalR giriş</span><span class="sxs-lookup"><span data-stu-id="a13f3-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-opno-locsignalr"></a><span data-ttu-id="a13f3-104">SignalRnedir?</span><span class="sxs-lookup"><span data-stu-id="a13f3-104">What is SignalR?</span></span>

<span data-ttu-id="a13f3-105">ASP.NET Core SignalR, uygulamalara gerçek zamanlı Web işlevselliği eklemeyi kolaylaştıran açık kaynaklı bir kitaplıktır.</span><span class="sxs-lookup"><span data-stu-id="a13f3-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="a13f3-106">Gerçek zamanlı Web işlevselliği, sunucu tarafı kodun anında istemcilere içerik gönderebilmesine olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="a13f3-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="a13f3-107">SignalRiçin iyi adaylar:</span><span class="sxs-lookup"><span data-stu-id="a13f3-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="a13f3-108">Sunucudan yüksek frekanslı güncelleştirmeler gerektiren uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="a13f3-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="a13f3-109">Oyun, sosyal ağlar, oylama, açık eksiltme, haritalar ve GPS uygulamaları örnekleri verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="a13f3-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="a13f3-110">Panolar ve izleme uygulamaları.</span><span class="sxs-lookup"><span data-stu-id="a13f3-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="a13f3-111">Şirket panoları, anlık satış güncelleştirmeleri veya seyahat uyarıları örnekleri bulunur.</span><span class="sxs-lookup"><span data-stu-id="a13f3-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="a13f3-112">İşbirliğine dayalı uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="a13f3-112">Collaborative apps.</span></span> <span data-ttu-id="a13f3-113">Beyaz tahta uygulamaları ve takım toplantısı yazılımı, işbirliğine dayalı uygulamalara örnek olarak verilebilir.</span><span class="sxs-lookup"><span data-stu-id="a13f3-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="a13f3-114">Bildirimleri gerektiren uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="a13f3-114">Apps that require notifications.</span></span> <span data-ttu-id="a13f3-115">Sosyal ağlar, e-posta, sohbet, Oyunlar, seyahat uyarıları ve diğer birçok uygulama kullanım bildirimleri.</span><span class="sxs-lookup"><span data-stu-id="a13f3-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

SignalR<span data-ttu-id="a13f3-116">, sunucudan istemciye [uzak yordam çağrıları (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)oluşturmak IÇIN bir API sağlar.</span><span class="sxs-lookup"><span data-stu-id="a13f3-116"> provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="a13f3-117">RPC 'ler, sunucu tarafı .NET Core kodundan gelen istemcilerdeki JavaScript işlevlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="a13f3-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="a13f3-118">ASP.NET Core için SignalR bazı özellikler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="a13f3-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="a13f3-119">Bağlantı yönetimini otomatik olarak işler.</span><span class="sxs-lookup"><span data-stu-id="a13f3-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="a13f3-120">Tüm bağlı istemcilere aynı anda iletiler gönderir.</span><span class="sxs-lookup"><span data-stu-id="a13f3-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="a13f3-121">Örneğin, bir sohbet odası.</span><span class="sxs-lookup"><span data-stu-id="a13f3-121">For example, a chat room.</span></span>
* <span data-ttu-id="a13f3-122">Belirli istemcilere veya istemci gruplarına iletiler gönderir.</span><span class="sxs-lookup"><span data-stu-id="a13f3-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="a13f3-123">Artan trafiği işleyecek şekilde ölçeklendirilir.</span><span class="sxs-lookup"><span data-stu-id="a13f3-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="a13f3-124">Kaynak, [GitHub 'daki birSignalR deposunda](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR)barındırılır.</span><span class="sxs-lookup"><span data-stu-id="a13f3-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="a13f3-125">Taşımalar</span><span class="sxs-lookup"><span data-stu-id="a13f3-125">Transports</span></span>

SignalR<span data-ttu-id="a13f3-126"> gerçek zamanlı iletişimi (düzgün geri dönüş sırasıyla) işlemek için aşağıdaki teknikleri destekler:</span><span class="sxs-lookup"><span data-stu-id="a13f3-126"> supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="a13f3-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="a13f3-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="a13f3-128">Sunucu tarafından gönderilen olaylar</span><span class="sxs-lookup"><span data-stu-id="a13f3-128">Server-Sent Events</span></span>
* <span data-ttu-id="a13f3-129">Uzun yoklama</span><span class="sxs-lookup"><span data-stu-id="a13f3-129">Long Polling</span></span>

SignalR<span data-ttu-id="a13f3-130">, sunucu ve istemci özellikleri içinde en iyi taşıma yöntemini otomatik olarak seçer.</span><span class="sxs-lookup"><span data-stu-id="a13f3-130"> automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="a13f3-131">Merkezler</span><span class="sxs-lookup"><span data-stu-id="a13f3-131">Hubs</span></span>

SignalR<span data-ttu-id="a13f3-132">, istemciler ve sunucular arasında iletişim kurmak için *hub 'lar* kullanır.</span><span class="sxs-lookup"><span data-stu-id="a13f3-132"> uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="a13f3-133">Hub, bir istemcinin ve sunucunun birbirlerine Yöntemler çağırmasını sağlayan üst düzey bir işlem hattdır.</span><span class="sxs-lookup"><span data-stu-id="a13f3-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> SignalR<span data-ttu-id="a13f3-134">, makinenin sunucu sınırları genelinde dağıtımını otomatik olarak işler ve istemcilerin sunucuda Yöntemler çağırmasını sağlar ve tam tersi de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="a13f3-134"> handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="a13f3-135">Model bağlamayı sağlayan yöntemlere kesin olarak yazılmış parametreler geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a13f3-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> SignalR<span data-ttu-id="a13f3-136"> iki yerleşik hub Protokolü sağlar: JSON tabanlı bir metin Protokolü ve [MessagePack](https://msgpack.org/)temelli bir ikili protokol.</span><span class="sxs-lookup"><span data-stu-id="a13f3-136"> provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="a13f3-137">MessagePack genellikle JSON ile karşılaştırıldığında daha küçük iletiler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a13f3-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="a13f3-138">Daha eski tarayıcıların, MessagePack protokolü desteği sağlamak için [XHR düzey 2](https://caniuse.com/#feat=xhr2) ' i desteklemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="a13f3-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="a13f3-139">Hub 'lar, istemci tarafı yönteminin adını ve parametrelerini içeren iletiler göndererek istemci tarafı kodu çağırır.</span><span class="sxs-lookup"><span data-stu-id="a13f3-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="a13f3-140">Yöntem parametreleri olarak gönderilen nesneler, yapılandırılan protokol kullanılarak seri durumdan çıkarılacak.</span><span class="sxs-lookup"><span data-stu-id="a13f3-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="a13f3-141">İstemci, adı istemci tarafı koddaki bir yöntemle eşleştirmeye çalışır.</span><span class="sxs-lookup"><span data-stu-id="a13f3-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="a13f3-142">İstemci bir eşleşme bulduğunda, yöntemini çağırır ve seri durumdan çıkarılan parametre verilerine geçirir.</span><span class="sxs-lookup"><span data-stu-id="a13f3-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a13f3-143">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a13f3-143">Additional resources</span></span>

* <span data-ttu-id="a13f3-144">[ASP.NET Core için SignalR kullanmaya başlama](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="a13f3-144">[Get started with SignalR for ASP.NET Core](xref:tutorials/signalr)</span></span>
* [<span data-ttu-id="a13f3-145">Desteklenen Platformlar</span><span class="sxs-lookup"><span data-stu-id="a13f3-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="a13f3-146">Merkezler</span><span class="sxs-lookup"><span data-stu-id="a13f3-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="a13f3-147">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="a13f3-147">JavaScript client</span></span>](xref:signalr/javascript-client)
