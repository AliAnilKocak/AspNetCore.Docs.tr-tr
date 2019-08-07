---
title: ASP.NET Core için istemci IP SafeList
author: damienbod
description: Uzak IP adreslerini onaylanan IP adresleri listesine göre doğrulamak için ara yazılım veya eylem filtreleri yazmayı öğrenin.
ms.author: tdykstra
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: ca05989efabea3a71c6912e98055a6746e0f5966
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223930"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="f7ef9-103">ASP.NET Core için istemci IP SafeList</span><span class="sxs-lookup"><span data-stu-id="f7ef9-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="f7ef9-104">By [Davmıen Bowden](https://twitter.com/damien_bod) ve [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f7ef9-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="f7ef9-105">Bu makalede bir ASP.NET Core uygulamasında bir IP SafeList (beyaz liste olarak da bilinir) uygulamanın üç yolu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-105">This article shows three ways to implement an IP safelist (also known as a whitelist) in an ASP.NET Core app.</span></span> <span data-ttu-id="f7ef9-106">Şunu kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f7ef9-106">You can use:</span></span>

* <span data-ttu-id="f7ef9-107">Her isteğin uzak IP adresini denetlemek için ara yazılım.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-107">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="f7ef9-108">Belirli denetleyiciler veya eylem yöntemlerine yönelik isteklerin uzak IP adresini denetlemek için eylem filtreleri.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-108">Action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="f7ef9-109">Razor sayfalarına yönelik isteklerin uzak IP adresini denetlemek için filtreler Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-109">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="f7ef9-110">Her durumda, onaylanan istemci IP adreslerini içeren bir dize bir uygulama ayarında saklanır.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-110">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="f7ef9-111">Ara yazılım veya filtre, dizeyi bir liste olarak ayrıştırır ve uzak IP 'nin listede olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-111">The middleware or filter parses the string into a list and checks if the remote IP is in the list.</span></span> <span data-ttu-id="f7ef9-112">Aksi takdirde, HTTP 403 yasaklanmış durum kodu döndürülür.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-112">If not, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="f7ef9-113">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7ef9-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-safelist"></a><span data-ttu-id="f7ef9-114">SafeList</span><span class="sxs-lookup"><span data-stu-id="f7ef9-114">The safelist</span></span>

<span data-ttu-id="f7ef9-115">Liste *appSettings. JSON* dosyasında yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-115">The list is configured in the *appsettings.json* file.</span></span> <span data-ttu-id="f7ef9-116">Bu, noktalı virgülle ayrılmış bir liste ve IPv4 ve IPv6 adresleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-116">It's a semicolon-delimited list and can contain IPv4 and IPv6 addresses.</span></span>

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a><span data-ttu-id="f7ef9-117">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="f7ef9-117">Middleware</span></span>

<span data-ttu-id="f7ef9-118">`Configure` Yöntemi, ara yazılımı ekler ve bir Oluşturucu parametresinde SafeList dizesini buna geçirir.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-118">The `Configure` method adds the middleware and passes the safelist string to it in a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="f7ef9-119">Ara yazılım, dizeyi bir dizi olarak ayrıştırır ve dizideki uzak IP adresini arar.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-119">The middleware parses the string into an array and looks for the remote IP address in the array.</span></span> <span data-ttu-id="f7ef9-120">Uzak IP adresi bulunamazsa, ara yazılım HTTP 401 yasak değerini döndürür.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-120">If the remote IP address is not found, the middleware returns HTTP 401 Forbidden.</span></span> <span data-ttu-id="f7ef9-121">Bu doğrulama işlemi HTTP GET istekleri için atlanır.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-121">This validation process is bypassed for HTTP Get requests.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="f7ef9-122">Eylem filtresi</span><span class="sxs-lookup"><span data-stu-id="f7ef9-122">Action filter</span></span>

<span data-ttu-id="f7ef9-123">Yalnızca belirli denetleyiciler veya eylem yöntemleri için bir SafeList istiyorsanız, bir eylem filtresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-123">If you want a safelist only for specific controllers or action methods, use an action filter.</span></span> <span data-ttu-id="f7ef9-124">Örnek buradadır:</span><span class="sxs-lookup"><span data-stu-id="f7ef9-124">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

<span data-ttu-id="f7ef9-125">Eylem filtresi, hizmetler kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-125">The action filter is added to the services container.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="f7ef9-126">Filtre daha sonra bir denetleyici veya eylem yönteminde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-126">The filter can then be used on a controller or action method.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

<span data-ttu-id="f7ef9-127">Örnek uygulamada, filtre `Get` yöntemine uygulanır.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-127">In the sample app, the filter is applied to the `Get` method.</span></span> <span data-ttu-id="f7ef9-128">Bu nedenle, bir `Get` API isteği göndererek uygulamayı test ettiğinizde, öznitelik istemci IP adresini doğruluyor.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-128">So when you test the app by sending a `Get` API request, the attribute is validating the client IP address.</span></span> <span data-ttu-id="f7ef9-129">API 'YI başka bir HTTP yöntemiyle çağırarak test ettiğinizde, ara yazılım istemci IP 'sini doğruluyor.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-129">When you test by calling the API with any other HTTP method, the middleware is validating the client IP.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="f7ef9-130">Razor Pages filtresi</span><span class="sxs-lookup"><span data-stu-id="f7ef9-130">Razor Pages filter</span></span> 

<span data-ttu-id="f7ef9-131">Razor Pages bir uygulama için bir SafeList istiyorsanız, bir Razor Pages filtresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-131">If you want a safelist for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="f7ef9-132">Örnek buradadır:</span><span class="sxs-lookup"><span data-stu-id="f7ef9-132">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

<span data-ttu-id="f7ef9-133">Bu filtre, MVC filtreleri koleksiyonuna eklenerek etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-133">This filter is enabled by adding it to the MVC Filters collection.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

<span data-ttu-id="f7ef9-134">Uygulamayı çalıştırıp bir Razor sayfası istediğinizde Razor Pages filtresi istemci IP 'sini doğruluyor.</span><span class="sxs-lookup"><span data-stu-id="f7ef9-134">When you run the app and request a Razor page, the Razor Pages filter is validating the client IP.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f7ef9-135">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="f7ef9-135">Next steps</span></span>

<span data-ttu-id="f7ef9-136">[ASP.NET Core ara yazılım hakkında daha fazla bilgi edinin](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="f7ef9-136">[Learn more about ASP.NET Core Middleware](xref:fundamentals/middleware/index).</span></span>
