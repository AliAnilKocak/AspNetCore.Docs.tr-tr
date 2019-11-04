---
title: ASP.NET Core SignalR desteklenen platformlar
author: bradygaster
description: ASP.NET Core SignalR için desteklenen platformlar hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2019
uid: signalr/supported-platforms
ms.openlocfilehash: 1be7a307710e6e522c0088fd1ca01da11a13eda1
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426975"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="fe8a9-103">ASP.NET Core SignalR desteklenen platformlar</span><span class="sxs-lookup"><span data-stu-id="fe8a9-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="fe8a9-104">Sunucu sistem gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="fe8a9-104">Server system requirements</span></span>

<span data-ttu-id="fe8a9-105">ASP.NET Core için SignalR, ASP.NET Core desteklediği tüm sunucu platformunu destekler.</span><span class="sxs-lookup"><span data-stu-id="fe8a9-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="fe8a9-106">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="fe8a9-106">JavaScript client</span></span>

<span data-ttu-id="fe8a9-107">[JavaScript Istemcisi](https://www.npmjs.com/package/@aspnet/signalr) NodeJS 8 ve sonraki sürümlerde ve aşağıdaki tarayıcılarda çalışır:</span><span class="sxs-lookup"><span data-stu-id="fe8a9-107">The [JavaScript client](https://www.npmjs.com/package/@aspnet/signalr) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="fe8a9-108">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="fe8a9-108">Browser</span></span>                         | <span data-ttu-id="fe8a9-109">Version</span><span class="sxs-lookup"><span data-stu-id="fe8a9-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="fe8a9-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="fe8a9-110">Microsoft Edge</span></span>                  | <span data-ttu-id="fe8a9-111">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="fe8a9-111">Current&dagger;</span></span> |
| <span data-ttu-id="fe8a9-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="fe8a9-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="fe8a9-113">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="fe8a9-113">Current&dagger;</span></span> |
| <span data-ttu-id="fe8a9-114">Google Chrome; Android içerir</span><span class="sxs-lookup"><span data-stu-id="fe8a9-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="fe8a9-115">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="fe8a9-115">Current&dagger;</span></span> |
| <span data-ttu-id="fe8a9-116">Uygulamasını iOS içerir</span><span class="sxs-lookup"><span data-stu-id="fe8a9-116">Safari; includes iOS</span></span>            | <span data-ttu-id="fe8a9-117">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="fe8a9-117">Current&dagger;</span></span> |
| <span data-ttu-id="fe8a9-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="fe8a9-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="fe8a9-119">11</span><span class="sxs-lookup"><span data-stu-id="fe8a9-119">11</span></span>              |

<span data-ttu-id="fe8a9-120">&dagger;*geçerli* , tarayıcının en son sürümünü ifade eder.</span><span class="sxs-lookup"><span data-stu-id="fe8a9-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="fe8a9-121">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="fe8a9-121">.NET client</span></span>

<span data-ttu-id="fe8a9-122">[.Net Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) , ASP.NET Core tarafından desteklenen herhangi bir platformda çalışır.</span><span class="sxs-lookup"><span data-stu-id="fe8a9-122">The [.NET client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="fe8a9-123">Örneğin, Xamarin geliştiricileri, Xamarin. iOS 8.4.0.1 ve üzeri ile iOS uygulamalarını ve Xamarin. iOS 11.14.0.4 ve üstünü kullanarak Android uygulamaları oluşturmak için [SignalR 'yi kullanabilir](https://github.com/aspnet/Announcements/issues/305) .</span><span class="sxs-lookup"><span data-stu-id="fe8a9-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="fe8a9-124">Sunucu IIS çalıştırıyorsa, WebSockets aktarımı Windows Server 2012 veya üzeri sürümlerde IIS 8,0 veya sonraki bir sürümü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="fe8a9-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="fe8a9-125">Diğer aktarımlar tüm platformlarda desteklenir.</span><span class="sxs-lookup"><span data-stu-id="fe8a9-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="fe8a9-126">Java istemcisi</span><span class="sxs-lookup"><span data-stu-id="fe8a9-126">Java client</span></span>

<span data-ttu-id="fe8a9-127">[Java istemcisi](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) , Java 8 ve sonraki sürümlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="fe8a9-127">The [Java client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="fe8a9-128">Desteklenmeyen istemciler</span><span class="sxs-lookup"><span data-stu-id="fe8a9-128">Unsupported clients</span></span>

<span data-ttu-id="fe8a9-129">Aşağıdaki istemciler mevcuttur, ancak deneysel veya resmi olmayan bir.</span><span class="sxs-lookup"><span data-stu-id="fe8a9-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="fe8a9-130">Şu anda desteklenmemektedir ve hiçbir şekilde bulunmayabilir.</span><span class="sxs-lookup"><span data-stu-id="fe8a9-130">They aren't currently supported and may never be.</span></span>

* [<span data-ttu-id="fe8a9-131">C++istemcilerinin</span><span class="sxs-lookup"><span data-stu-id="fe8a9-131">C++ client</span></span>](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [<span data-ttu-id="fe8a9-132">Swift istemcisi</span><span class="sxs-lookup"><span data-stu-id="fe8a9-132">Swift client</span></span>](https://github.com/moozzyk/SignalR-Client-Swift)
