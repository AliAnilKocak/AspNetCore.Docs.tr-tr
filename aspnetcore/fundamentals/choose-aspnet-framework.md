---
title: Seçin arasında ASP.NET 4.x ve ASP.NET Core
author: rick-anderson
description: ASP.NET Core vs açıklar. ASP.NET 4.x ve bunlar arasında seçim yapma.
ms.author: riande
ms.date: 09/11/2018
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 79e56e10b756677431ceff289300c251e54bf632
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861621"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="bf97f-103">Seçin arasında ASP.NET 4.x ve ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf97f-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="bf97f-104">ASP.NET Core, ASP.NET'in yeniden 4.x.</span><span class="sxs-lookup"><span data-stu-id="bf97f-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="bf97f-105">Bu makalede, aralarındaki farklılıklar listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="bf97f-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="bf97f-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf97f-106">ASP.NET Core</span></span>

<span data-ttu-id="bf97f-107">ASP.NET Core Windows, macOS veya Linux'ta modern, bulut tabanlı web uygulamaları oluşturmaya yönelik açık kaynaklı, platformlar arası bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="bf97f-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="bf97f-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="bf97f-108">ASP.NET 4.x</span></span>

<span data-ttu-id="bf97f-109">ASP.NET 4.x, kurumsal sınıf, oluşturmak için gereken hizmetleri Windows server tabanlı web apps'de sağlayan olgun bir altyapısıdır.</span><span class="sxs-lookup"><span data-stu-id="bf97f-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="bf97f-110">Framework seçimi</span><span class="sxs-lookup"><span data-stu-id="bf97f-110">Framework selection</span></span>

<span data-ttu-id="bf97f-111">Aşağıdaki tabloda karşılaştırılmıştır ASP.NET Core, ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="bf97f-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="bf97f-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf97f-112">ASP.NET Core</span></span> | <span data-ttu-id="bf97f-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="bf97f-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="bf97f-114">Windows, macOS veya Linux için derleme</span><span class="sxs-lookup"><span data-stu-id="bf97f-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="bf97f-115">Windows için derleme</span><span class="sxs-lookup"><span data-stu-id="bf97f-115">Build for Windows</span></span>|
|<span data-ttu-id="bf97f-116">[Razor sayfaları](xref:razor-pages/index) itibarıyla ASP.NET Core Web kullanıcı Arabirimi oluşturmak için önerilen yaklaşımdır 2.x.</span><span class="sxs-lookup"><span data-stu-id="bf97f-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="bf97f-117">Ayrıca bkz: [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), ve [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="bf97f-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="bf97f-118">Kullanım [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [Web kancaları](/aspnet/webhooks/), veya [Web sayfaları](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="bf97f-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="bf97f-119">Makine başına birden çok sürümü</span><span class="sxs-lookup"><span data-stu-id="bf97f-119">Multiple versions per machine</span></span>|<span data-ttu-id="bf97f-120">Makine başına bir sürümü</span><span class="sxs-lookup"><span data-stu-id="bf97f-120">One version per machine</span></span>|
|<span data-ttu-id="bf97f-121">Visual Studio ile geliştirin [Mac için Visual Studio](https://www.visualstudio.com/vs/visual-studio-mac/), veya [Visual Studio Code](https://code.visualstudio.com/) kullanarak C# veyaF#</span><span class="sxs-lookup"><span data-stu-id="bf97f-121">Develop with Visual Studio, [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="bf97f-122">Visual Studio kullanarak ile geliştirme C#, VB, veyaF#</span><span class="sxs-lookup"><span data-stu-id="bf97f-122">Develop with Visual Studio using C#, VB, or F#</span></span>|
|<span data-ttu-id="bf97f-123">Daha yüksek performansa ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="bf97f-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="bf97f-124">İyi bir performans</span><span class="sxs-lookup"><span data-stu-id="bf97f-124">Good performance</span></span>|
|[<span data-ttu-id="bf97f-125">.NET Framework veya .NET Core çalışma zamanı seçin</span><span class="sxs-lookup"><span data-stu-id="bf97f-125">Choose .NET Framework or .NET Core runtime</span></span>](/dotnet/articles/standard/choosing-core-framework-server)|<span data-ttu-id="bf97f-126">.NET Framework çalışma zamanını kullanma</span><span class="sxs-lookup"><span data-stu-id="bf97f-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="bf97f-127">Bkz: [.NET Framework'ü hedefleyen ASP.NET Core](xref:index#target-framework) .NET Framework üzerinde ASP.NET Core 2.x desteği hakkında daha fazla bilgi için.</span><span class="sxs-lookup"><span data-stu-id="bf97f-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="bf97f-128">ASP.NET Core senaryoları</span><span class="sxs-lookup"><span data-stu-id="bf97f-128">ASP.NET Core scenarios</span></span>

* <span data-ttu-id="bf97f-129">[Razor sayfaları](xref:razor-pages/index) itibarıyla ASP.NET Core Web kullanıcı Arabirimi oluşturmak için önerilen yaklaşımdır 2.x.</span><span class="sxs-lookup"><span data-stu-id="bf97f-129">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span>
* [<span data-ttu-id="bf97f-130">Web siteleri</span><span class="sxs-lookup"><span data-stu-id="bf97f-130">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="bf97f-131">API'leri</span><span class="sxs-lookup"><span data-stu-id="bf97f-131">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="bf97f-132">Gerçek zamanlı</span><span class="sxs-lookup"><span data-stu-id="bf97f-132">Real-time</span></span>](xref:signalr/index)
* [<span data-ttu-id="bf97f-133">Azure'da ASP.NET Core uygulaması dağıtma</span><span class="sxs-lookup"><span data-stu-id="bf97f-133">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="bf97f-134">ASP.NET 4.x senaryoları</span><span class="sxs-lookup"><span data-stu-id="bf97f-134">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="bf97f-135">Web siteleri</span><span class="sxs-lookup"><span data-stu-id="bf97f-135">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="bf97f-136">API'leri</span><span class="sxs-lookup"><span data-stu-id="bf97f-136">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="bf97f-137">Gerçek zamanlı</span><span class="sxs-lookup"><span data-stu-id="bf97f-137">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="bf97f-138">Azure'da ASP.NET 4.x web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="bf97f-138">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="bf97f-139">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="bf97f-139">Additional resources</span></span>

* [<span data-ttu-id="bf97f-140">ASP.NET'e giriş</span><span class="sxs-lookup"><span data-stu-id="bf97f-140">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="bf97f-141">ASP.NET Core'a giriş</span><span class="sxs-lookup"><span data-stu-id="bf97f-141">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>

> [!NOTE]
> <span data-ttu-id="bf97f-142">ASP.NET Core içindekiler tablosuna yönelik önerilmiş olan yeni bir yapının kullanılabilirliğini test ediyoruz.</span><span class="sxs-lookup"><span data-stu-id="bf97f-142">We’re testing the usability of a proposed new structure for the ASP.NET Core table of contents.</span></span>  <span data-ttu-id="bf97f-143">Geçerli veya önerilen içindekiler tablosunda 7 farklı konuyu bulmaya ilişkin alıştırmayı denemek için vaktiniz varsa lütfen [çalışmaya katılmak için buraya tıklayın](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).</span><span class="sxs-lookup"><span data-stu-id="bf97f-143">If you have a few minutes to try an exercise of finding 7 different topics in the current or proposed table of contents, please [click here to participate in the study](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).</span></span>