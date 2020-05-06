---
title: Desteklenen SignalR platformları ASP.NET Core
author: bradygaster
description: ASP.NET Core SignalRiçin desteklenen platformlar hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 70a05dabb95aaf561aa78d5c8b24b430c51bd973
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772611"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="4a5d6-103">ASP.NET Core SignalR desteklenen platformlar</span><span class="sxs-lookup"><span data-stu-id="4a5d6-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="4a5d6-104">Sunucu sistem gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="4a5d6-104">Server system requirements</span></span>

<span data-ttu-id="4a5d6-105">ASP.NET Core için SignalR, ASP.NET Core desteklediği tüm sunucu platformunu destekler.</span><span class="sxs-lookup"><span data-stu-id="4a5d6-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="4a5d6-106">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="4a5d6-106">JavaScript client</span></span>

<span data-ttu-id="4a5d6-107">[JavaScript Istemcisi](xref:signalr/javascript-client) NodeJS 8 ve sonraki sürümlerde ve aşağıdaki tarayıcılarda çalışır:</span><span class="sxs-lookup"><span data-stu-id="4a5d6-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="4a5d6-108">Tarayıcı</span><span class="sxs-lookup"><span data-stu-id="4a5d6-108">Browser</span></span>                         | <span data-ttu-id="4a5d6-109">Sürüm</span><span class="sxs-lookup"><span data-stu-id="4a5d6-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="4a5d6-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="4a5d6-110">Microsoft Edge</span></span>                  | <span data-ttu-id="4a5d6-111">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="4a5d6-111">Current&dagger;</span></span> |
| <span data-ttu-id="4a5d6-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="4a5d6-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="4a5d6-113">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="4a5d6-113">Current&dagger;</span></span> |
| <span data-ttu-id="4a5d6-114">Google Chrome; Android içerir</span><span class="sxs-lookup"><span data-stu-id="4a5d6-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="4a5d6-115">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="4a5d6-115">Current&dagger;</span></span> |
| <span data-ttu-id="4a5d6-116">Uygulamasını iOS içerir</span><span class="sxs-lookup"><span data-stu-id="4a5d6-116">Safari; includes iOS</span></span>            | <span data-ttu-id="4a5d6-117">Geçerli&dagger;</span><span class="sxs-lookup"><span data-stu-id="4a5d6-117">Current&dagger;</span></span> |
| <span data-ttu-id="4a5d6-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="4a5d6-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="4a5d6-119">11</span><span class="sxs-lookup"><span data-stu-id="4a5d6-119">11</span></span>              |

<span data-ttu-id="4a5d6-120">&dagger;*Geçerli* , tarayıcının en son sürümünü ifade eder.</span><span class="sxs-lookup"><span data-stu-id="4a5d6-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="4a5d6-121">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="4a5d6-121">.NET client</span></span>

<span data-ttu-id="4a5d6-122">[.Net Client](xref:signalr/dotnet-client) , ASP.NET Core tarafından desteklenen herhangi bir platformda çalışır.</span><span class="sxs-lookup"><span data-stu-id="4a5d6-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="4a5d6-123">Örneğin, [Xamarin geliştiricileri SignalR ](https://github.com/aspnet/Announcements/issues/305) Xamarin. iOS 8.4.0.1 ve üzeri ve Android uygulamalarını kullanarak Xamarin. iOS 11.14.0.4 ve üstünü kullanarak Android uygulamaları oluşturmak için kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="4a5d6-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="4a5d6-124">Sunucu IIS çalıştırıyorsa, WebSockets aktarımı Windows Server 2012 veya üzeri sürümlerde IIS 8,0 veya sonraki bir sürümü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4a5d6-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="4a5d6-125">Diğer aktarımlar tüm platformlarda desteklenir.</span><span class="sxs-lookup"><span data-stu-id="4a5d6-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="4a5d6-126">Java istemcisi</span><span class="sxs-lookup"><span data-stu-id="4a5d6-126">Java client</span></span>

<span data-ttu-id="4a5d6-127">[Java istemcisi](xref:signalr/java-client) , Java 8 ve sonraki sürümlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="4a5d6-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="4a5d6-128">Desteklenmeyen istemciler</span><span class="sxs-lookup"><span data-stu-id="4a5d6-128">Unsupported clients</span></span>

<span data-ttu-id="4a5d6-129">Aşağıdaki istemciler mevcuttur, ancak deneysel veya resmi olmayan bir.</span><span class="sxs-lookup"><span data-stu-id="4a5d6-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="4a5d6-130">Şu anda desteklenmemektedir ve hiçbir şekilde bulunmayabilir.</span><span class="sxs-lookup"><span data-stu-id="4a5d6-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="4a5d6-131">[C++ istemcisi](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="4a5d6-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="4a5d6-132">[Swift istemcisi](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="4a5d6-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
