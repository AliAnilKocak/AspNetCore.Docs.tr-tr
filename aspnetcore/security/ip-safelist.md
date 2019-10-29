---
title: ASP.NET Core için istemci IP SafeList
author: damienbod
description: Uzak IP adreslerini onaylanan IP adresleri listesine göre doğrulamak için ara yazılım veya eylem filtreleri yazmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: ca5b0f8088773027f7403120247cbeca8900bcf5
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034340"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="35aab-103">ASP.NET Core için istemci IP SafeList</span><span class="sxs-lookup"><span data-stu-id="35aab-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="35aab-104">By [Davmıen Bowden](https://twitter.com/damien_bod) ve [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="35aab-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="35aab-105">Bu makalede bir ASP.NET Core uygulamasında bir IP SafeList (beyaz liste olarak da bilinir) uygulamanın üç yolu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="35aab-105">This article shows three ways to implement an IP safelist (also known as a whitelist) in an ASP.NET Core app.</span></span> <span data-ttu-id="35aab-106">Şunu kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="35aab-106">You can use:</span></span>

* <span data-ttu-id="35aab-107">Her isteğin uzak IP adresini denetlemek için ara yazılım.</span><span class="sxs-lookup"><span data-stu-id="35aab-107">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="35aab-108">Belirli denetleyiciler veya eylem yöntemlerine yönelik isteklerin uzak IP adresini denetlemek için eylem filtreleri.</span><span class="sxs-lookup"><span data-stu-id="35aab-108">Action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="35aab-109">Razor sayfalarına yönelik isteklerin uzak IP adresini denetlemek için filtreler Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="35aab-109">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="35aab-110">Her durumda, onaylanan istemci IP adreslerini içeren bir dize bir uygulama ayarında saklanır.</span><span class="sxs-lookup"><span data-stu-id="35aab-110">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="35aab-111">Ara yazılım veya filtre, dizeyi bir liste olarak ayrıştırır ve uzak IP 'nin listede olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="35aab-111">The middleware or filter parses the string into a list and checks if the remote IP is in the list.</span></span> <span data-ttu-id="35aab-112">Aksi takdirde, HTTP 403 yasaklanmış durum kodu döndürülür.</span><span class="sxs-lookup"><span data-stu-id="35aab-112">If not, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="35aab-113">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="35aab-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-safelist"></a><span data-ttu-id="35aab-114">SafeList</span><span class="sxs-lookup"><span data-stu-id="35aab-114">The safelist</span></span>

<span data-ttu-id="35aab-115">Liste *appSettings. JSON* dosyasında yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="35aab-115">The list is configured in the *appsettings.json* file.</span></span> <span data-ttu-id="35aab-116">Bu, noktalı virgülle ayrılmış bir liste ve IPv4 ve IPv6 adresleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="35aab-116">It's a semicolon-delimited list and can contain IPv4 and IPv6 addresses.</span></span>

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a><span data-ttu-id="35aab-117">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="35aab-117">Middleware</span></span>

<span data-ttu-id="35aab-118">`Configure` yöntemi, ara yazılımı ekler ve bir Oluşturucu parametresinde SafeList dizesini buna geçirir.</span><span class="sxs-lookup"><span data-stu-id="35aab-118">The `Configure` method adds the middleware and passes the safelist string to it in a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="35aab-119">Ara yazılım, dizeyi bir dizi olarak ayrıştırır ve dizideki uzak IP adresini arar.</span><span class="sxs-lookup"><span data-stu-id="35aab-119">The middleware parses the string into an array and looks for the remote IP address in the array.</span></span> <span data-ttu-id="35aab-120">Uzak IP adresi bulunamazsa, ara yazılım HTTP 401 yasak değerini döndürür.</span><span class="sxs-lookup"><span data-stu-id="35aab-120">If the remote IP address is not found, the middleware returns HTTP 401 Forbidden.</span></span> <span data-ttu-id="35aab-121">Bu doğrulama işlemi HTTP GET istekleri için atlanır.</span><span class="sxs-lookup"><span data-stu-id="35aab-121">This validation process is bypassed for HTTP Get requests.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="35aab-122">Eylem filtresi</span><span class="sxs-lookup"><span data-stu-id="35aab-122">Action filter</span></span>

<span data-ttu-id="35aab-123">Yalnızca belirli denetleyiciler veya eylem yöntemleri için bir SafeList istiyorsanız, bir eylem filtresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="35aab-123">If you want a safelist only for specific controllers or action methods, use an action filter.</span></span> <span data-ttu-id="35aab-124">Örnek buradadır:</span><span class="sxs-lookup"><span data-stu-id="35aab-124">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckFilter.cs)]

<span data-ttu-id="35aab-125">Eylem filtresi, hizmetler kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="35aab-125">The action filter is added to the services container.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="35aab-126">Filtre daha sonra bir denetleyici veya eylem yönteminde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="35aab-126">The filter can then be used on a controller or action method.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

<span data-ttu-id="35aab-127">Örnek uygulamada, filtre `Get` yöntemine uygulanır.</span><span class="sxs-lookup"><span data-stu-id="35aab-127">In the sample app, the filter is applied to the `Get` method.</span></span> <span data-ttu-id="35aab-128">Bu nedenle, uygulamayı bir `Get` API isteği göndererek test ettiğinizde, öznitelik istemci IP adresini doğruluyor.</span><span class="sxs-lookup"><span data-stu-id="35aab-128">So when you test the app by sending a `Get` API request, the attribute is validating the client IP address.</span></span> <span data-ttu-id="35aab-129">API 'YI başka bir HTTP yöntemiyle çağırarak test ettiğinizde, ara yazılım istemci IP 'sini doğruluyor.</span><span class="sxs-lookup"><span data-stu-id="35aab-129">When you test by calling the API with any other HTTP method, the middleware is validating the client IP.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="35aab-130">Razor Pages filtresi</span><span class="sxs-lookup"><span data-stu-id="35aab-130">Razor Pages filter</span></span> 

<span data-ttu-id="35aab-131">Razor Pages bir uygulama için bir SafeList istiyorsanız, bir Razor Pages filtresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="35aab-131">If you want a safelist for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="35aab-132">Örnek buradadır:</span><span class="sxs-lookup"><span data-stu-id="35aab-132">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckPageFilter.cs)]

<span data-ttu-id="35aab-133">Bu filtre, MVC filtreleri koleksiyonuna eklenerek etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="35aab-133">This filter is enabled by adding it to the MVC Filters collection.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

<span data-ttu-id="35aab-134">Uygulamayı çalıştırıp bir Razor sayfası istediğinizde Razor Pages filtresi istemci IP 'sini doğruluyor.</span><span class="sxs-lookup"><span data-stu-id="35aab-134">When you run the app and request a Razor page, the Razor Pages filter is validating the client IP.</span></span>

## <a name="next-steps"></a><span data-ttu-id="35aab-135">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="35aab-135">Next steps</span></span>

<span data-ttu-id="35aab-136">[ASP.NET Core ara yazılım hakkında daha fazla bilgi edinin](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="35aab-136">[Learn more about ASP.NET Core Middleware](xref:fundamentals/middleware/index).</span></span>
