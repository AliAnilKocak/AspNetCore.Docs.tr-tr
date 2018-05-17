---
title: ASP.NET ve ASP.NET Core arasında seçim yapma
author: rick-anderson
description: ASP.NET ve ASP.NET Core arasında seçim yapma hakkında bilgi edinin.
manager: wpickett
ms.author: riande
ms.date: 05/11/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 0c6924d40b7327d2032a0278c56a0b4fa41d15a1
ms.sourcegitcommit: 9bc34b8269d2a150b844c3b8646dcb30278a95ea
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/12/2018
---
# <a name="choose-between-aspnet-and-aspnet-core"></a><span data-ttu-id="3ed9c-103">ASP.NET ve ASP.NET Core arasında seçim yapma</span><span class="sxs-lookup"><span data-stu-id="3ed9c-103">Choose between ASP.NET and ASP.NET Core</span></span>

<span data-ttu-id="3ed9c-104">ASP.NET web uygulaması olsun, oluşturduğunuz bir çözümü sizin için vardır: Windows Server hedefleme Kurumsal web uygulamaları ' küçük mikro Linux kapsayıcıları ve her şeyi arasında hedefleme.</span><span class="sxs-lookup"><span data-stu-id="3ed9c-104">No matter the web app you're creating, ASP.NET has a solution for you: from enterprise web apps targeting Windows Server, to small microservices targeting Linux containers, and everything in between.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="3ed9c-105">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ed9c-105">ASP.NET Core</span></span>

<span data-ttu-id="3ed9c-106">ASP.NET Core Windows, macOS ya da Linux modern, bulut tabanlı web uygulamaları oluşturmak için açık kaynak, platformlar arası bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="3ed9c-106">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

## <a name="aspnet"></a><span data-ttu-id="3ed9c-107">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="3ed9c-107">ASP.NET</span></span>

<span data-ttu-id="3ed9c-108">ASP.NET, kurumsal düzeyde oluşturmak için gereken tüm hizmetleri Windows server tabanlı web uygulamalarında sağlayan olgun bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="3ed9c-108">ASP.NET is a mature framework that provides all the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="3ed9c-109">Framework seçimi</span><span class="sxs-lookup"><span data-stu-id="3ed9c-109">Framework selection</span></span>

<span data-ttu-id="3ed9c-110">Hangi framework gereksinimleriniz için en uygun olduğunu belirlemek için aşağıdaki tabloyu gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="3ed9c-110">Review the table below to determine which framework is most appropriate for your needs.</span></span>

| <span data-ttu-id="3ed9c-111">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ed9c-111">ASP.NET Core</span></span> | <span data-ttu-id="3ed9c-112">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="3ed9c-112">ASP.NET</span></span> |
|---|---|
|<span data-ttu-id="3ed9c-113">Windows, macOS ya da Linux derleme</span><span class="sxs-lookup"><span data-stu-id="3ed9c-113">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="3ed9c-114">Windows için derleme</span><span class="sxs-lookup"><span data-stu-id="3ed9c-114">Build for Windows</span></span>|
|<span data-ttu-id="3ed9c-115">[Razor sayfalarının](xref:mvc/razor-pages/index) ASP.NET Core itibariyle bir Web kullanıcı Arabirimi oluşturmak için önerilen yaklaşımdır 2.x.</span><span class="sxs-lookup"><span data-stu-id="3ed9c-115">[Razor Pages](xref:mvc/razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="3ed9c-116">Ayrıca bkz. [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), ve [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="3ed9c-116">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="3ed9c-117">Kullanım [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [Kancalarını](/aspnet/webhooks/), veya [Web sayfaları](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="3ed9c-117">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="3ed9c-118">Makine başına birden çok sürüm</span><span class="sxs-lookup"><span data-stu-id="3ed9c-118">Multiple versions per machine</span></span>|<span data-ttu-id="3ed9c-119">Makine başına bir sürüm</span><span class="sxs-lookup"><span data-stu-id="3ed9c-119">One version per machine</span></span>|
|<span data-ttu-id="3ed9c-120">Visual Studio ile geliştirme [Mac için Visual Studio](https://www.visualstudio.com/vs/visual-studio-mac/), veya [Visual Studio Code](https://code.visualstudio.com/) C# veya F # kullanma</span><span class="sxs-lookup"><span data-stu-id="3ed9c-120">Develop with Visual Studio, [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="3ed9c-121">C#, VB ve F # kullanarak Visual Studio ile geliştirme</span><span class="sxs-lookup"><span data-stu-id="3ed9c-121">Develop with Visual Studio using C#, VB, or F#</span></span>|
|<span data-ttu-id="3ed9c-122">ASP.NET daha yüksek performans</span><span class="sxs-lookup"><span data-stu-id="3ed9c-122">Higher performance than ASP.NET</span></span>|<span data-ttu-id="3ed9c-123">İyi bir performans</span><span class="sxs-lookup"><span data-stu-id="3ed9c-123">Good performance</span></span>|
|[<span data-ttu-id="3ed9c-124">.NET Framework veya .NET çekirdeği çalışma zamanı seçin</span><span class="sxs-lookup"><span data-stu-id="3ed9c-124">Choose .NET Framework or .NET Core runtime</span></span>](/dotnet/articles/standard/choosing-core-framework-server)|<span data-ttu-id="3ed9c-125">.NET Framework çalışma zamanı kullanın</span><span class="sxs-lookup"><span data-stu-id="3ed9c-125">Use .NET Framework runtime</span></span>|

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="3ed9c-126">ASP.NET Core senaryoları</span><span class="sxs-lookup"><span data-stu-id="3ed9c-126">ASP.NET Core scenarios</span></span>

* <span data-ttu-id="3ed9c-127">[Razor sayfalarının](xref:mvc/razor-pages/index) ASP.NET Core itibariyle bir Web kullanıcı Arabirimi oluşturmak için önerilen yaklaşımdır 2.x.</span><span class="sxs-lookup"><span data-stu-id="3ed9c-127">[Razor Pages](xref:mvc/razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span>
* [<span data-ttu-id="3ed9c-128">Web siteleri</span><span class="sxs-lookup"><span data-stu-id="3ed9c-128">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="3ed9c-129">API'leri</span><span class="sxs-lookup"><span data-stu-id="3ed9c-129">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="3ed9c-130">Gerçek zamanlı</span><span class="sxs-lookup"><span data-stu-id="3ed9c-130">Real-time</span></span>](xref:signalr/index)

## <a name="aspnet-scenarios"></a><span data-ttu-id="3ed9c-131">ASP.NET senaryoları</span><span class="sxs-lookup"><span data-stu-id="3ed9c-131">ASP.NET scenarios</span></span>

* [<span data-ttu-id="3ed9c-132">Web siteleri</span><span class="sxs-lookup"><span data-stu-id="3ed9c-132">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="3ed9c-133">API'leri</span><span class="sxs-lookup"><span data-stu-id="3ed9c-133">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="3ed9c-134">Gerçek zamanlı</span><span class="sxs-lookup"><span data-stu-id="3ed9c-134">Real-time</span></span>](/aspnet/signalr)

## <a name="resources"></a><span data-ttu-id="3ed9c-135">Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3ed9c-135">Resources</span></span>

* [<span data-ttu-id="3ed9c-136">ASP.NET giriş</span><span class="sxs-lookup"><span data-stu-id="3ed9c-136">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="3ed9c-137">ASP.NET Core giriş</span><span class="sxs-lookup"><span data-stu-id="3ed9c-137">Introduction to ASP.NET Core</span></span>](xref:index)
