---
uid: mvc/overview/older-versions-1/controllers-and-routing/creating-a-route-constraint-cs
title: Bir rota kısıtlaması (C#) oluşturma | Microsoft Docs
author: StephenWalther
description: Bu öğreticide, Normal ifadelerle rota kısıtlamalarını oluşturarak tarayıcı eşleşen yolların nasıl istekleri nasıl kontrol edebilir Stephen Walther gösterir.
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/16/2009
ms.topic: article
ms.assetid: 0bfd06b1-12d3-4fbb-9779-a82e5eb7fe7d
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/creating-a-route-constraint-cs
msc.type: authoredcontent
ms.openlocfilehash: 3159feb6538e3048f4f235f7d549e692604ca4e7
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
ms.locfileid: "30871213"
---
<a name="creating-a-route-constraint-c"></a><span data-ttu-id="c811b-103">Bir rota kısıtlaması (C#) oluşturma</span><span class="sxs-lookup"><span data-stu-id="c811b-103">Creating a Route Constraint (C#)</span></span>
====================
<span data-ttu-id="c811b-104">tarafından [Stephen Walther](https://github.com/StephenWalther)</span><span class="sxs-lookup"><span data-stu-id="c811b-104">by [Stephen Walther](https://github.com/StephenWalther)</span></span>

> <span data-ttu-id="c811b-105">Bu öğreticide, Normal ifadelerle rota kısıtlamalarını oluşturarak tarayıcı eşleşen yolların nasıl istekleri nasıl kontrol edebilir Stephen Walther gösterir.</span><span class="sxs-lookup"><span data-stu-id="c811b-105">In this tutorial, Stephen Walther demonstrates how you can control how browser requests match routes by creating route constraints with regular expressions.</span></span>


<span data-ttu-id="c811b-106">Rota kısıtlamalarını belirli bir rota ile eşleşmekte tarayıcı isteklerini sınırlamak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="c811b-106">You use route constraints to restrict the browser requests that match a particular route.</span></span> <span data-ttu-id="c811b-107">Bir rota kısıtlamasını belirtmek amacıyla bir normal ifade kullanın.</span><span class="sxs-lookup"><span data-stu-id="c811b-107">You can use a regular expression to specify a route constraint.</span></span>

<span data-ttu-id="c811b-108">Örneğin, rota, Global.asax dosyasında listeleniyor 1'de tanımladığınız düşünün.</span><span class="sxs-lookup"><span data-stu-id="c811b-108">For example, imagine that you have defined the route in Listing 1 in your Global.asax file.</span></span>

<span data-ttu-id="c811b-109">**1 - Global.asax.cs listeleme**</span><span class="sxs-lookup"><span data-stu-id="c811b-109">**Listing 1 - Global.asax.cs**</span></span>

[!code-csharp[Main](creating-a-route-constraint-cs/samples/sample1.cs)]

<span data-ttu-id="c811b-110">1 listeleme ürün adında bir yol içeriyor.</span><span class="sxs-lookup"><span data-stu-id="c811b-110">Listing 1 contains a route named Product.</span></span> <span data-ttu-id="c811b-111">Tarayıcı isteklerini listeleme 2'de bulunan ProductController eşlemek için ürün rota kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c811b-111">You can use the Product route to map browser requests to the ProductController contained in Listing 2.</span></span>

<span data-ttu-id="c811b-112">**2 - Controllers\ProductController.cs listeleme**</span><span class="sxs-lookup"><span data-stu-id="c811b-112">**Listing 2 - Controllers\ProductController.cs**</span></span>

[!code-csharp[Main](creating-a-route-constraint-cs/samples/sample2.cs)]

<span data-ttu-id="c811b-113">Ürün denetleyicisi tarafından kullanıma sunulan Details() eylem ProductID adlı tek bir parametre kabul eden dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="c811b-113">Notice that the Details() action exposed by the Product controller accepts a single parameter named productId.</span></span> <span data-ttu-id="c811b-114">Bu parametre bir tamsayı parametredir.</span><span class="sxs-lookup"><span data-stu-id="c811b-114">This parameter is an integer parameter.</span></span>

<span data-ttu-id="c811b-115">Listeleme 1'de tanımlanan yol aşağıdaki URL'lerden birini eşleşir:</span><span class="sxs-lookup"><span data-stu-id="c811b-115">The route defined in Listing 1 will match any of the following URLs:</span></span>

- <span data-ttu-id="c811b-116">/ Ürün/23</span><span class="sxs-lookup"><span data-stu-id="c811b-116">/Product/23</span></span>
- <span data-ttu-id="c811b-117">/ Ürün/7</span><span class="sxs-lookup"><span data-stu-id="c811b-117">/Product/7</span></span>

<span data-ttu-id="c811b-118">Ne yazık ki, yol aşağıdaki URL'lerle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="c811b-118">Unfortunately, the route will also match the following URLs:</span></span>

- <span data-ttu-id="c811b-119">/ Ürün/filan</span><span class="sxs-lookup"><span data-stu-id="c811b-119">/Product/blah</span></span>
- <span data-ttu-id="c811b-120">/ Ürün/apple</span><span class="sxs-lookup"><span data-stu-id="c811b-120">/Product/apple</span></span>

<span data-ttu-id="c811b-121">Details() eylemi bir tamsayı parametresi beklediği dışında bir tamsayı değeri içeren bir isteği yapan bir hataya neden olur.</span><span class="sxs-lookup"><span data-stu-id="c811b-121">Because the Details() action expects an integer parameter, making a request that contains something other than an integer value will cause an error.</span></span> <span data-ttu-id="c811b-122">URL /Product/apple tarayıcınıza yazın, örneğin, ardından hata sayfası Şekil 1'de alırsınız.</span><span class="sxs-lookup"><span data-stu-id="c811b-122">For example, if you type the URL /Product/apple into your browser then you will get the error page in Figure 1.</span></span>


<span data-ttu-id="c811b-123">[![Yeni Proje iletişim kutusu](creating-a-route-constraint-cs/_static/image1.jpg)](creating-a-route-constraint-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="c811b-123">[![The New Project dialog box](creating-a-route-constraint-cs/_static/image1.jpg)](creating-a-route-constraint-cs/_static/image1.png)</span></span>

<span data-ttu-id="c811b-124">**Şekil 01**: açılımı bir sayfayı görmenizin ([tam boyutlu görüntüyü görüntülemek için tıklatın](creating-a-route-constraint-cs/_static/image2.png))</span><span class="sxs-lookup"><span data-stu-id="c811b-124">**Figure 01**: Seeing a page explode ([Click to view full-size image](creating-a-route-constraint-cs/_static/image2.png))</span></span>


<span data-ttu-id="c811b-125">Gerçekten yapmak istediğinize uygun tamsayı ProductID içeren URL'ler yalnızca eşleşen olur.</span><span class="sxs-lookup"><span data-stu-id="c811b-125">What you really want to do is only match URLs that contain a proper integer productId.</span></span> <span data-ttu-id="c811b-126">Rota ile eşleşmekte URL'leri kısıtlamak için bir yol tanımlarken bir kısıtlama kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c811b-126">You can use a constraint when defining a route to restrict the URLs that match the route.</span></span> <span data-ttu-id="c811b-127">Değiştirilen ürün rota listeleme 3'te yalnızca tamsayı eşleşen bir normal ifade kısıtlaması içerir.</span><span class="sxs-lookup"><span data-stu-id="c811b-127">The modified Product route in Listing 3 contains a regular expression constraint that only matches integers.</span></span>

<span data-ttu-id="c811b-128">**3 - Global.asax.cs listeleme**</span><span class="sxs-lookup"><span data-stu-id="c811b-128">**Listing 3 - Global.asax.cs**</span></span>

[!code-csharp[Main](creating-a-route-constraint-cs/samples/sample3.cs)]

<span data-ttu-id="c811b-129">Normal ifade \d+ bir veya daha fazla tamsayılar eşleşir.</span><span class="sxs-lookup"><span data-stu-id="c811b-129">The regular expression \d+ matches one or more integers.</span></span> <span data-ttu-id="c811b-130">Bu sınırlama aşağıdaki URL'ler eşleştirmek ürün yol neden olur:</span><span class="sxs-lookup"><span data-stu-id="c811b-130">This constraint causes the Product route to match the following URLs:</span></span>

- <span data-ttu-id="c811b-131">/Product/3</span><span class="sxs-lookup"><span data-stu-id="c811b-131">/Product/3</span></span>
- <span data-ttu-id="c811b-132">/ Ürün/8999</span><span class="sxs-lookup"><span data-stu-id="c811b-132">/Product/8999</span></span>

<span data-ttu-id="c811b-133">Ancak aşağıdaki URL'ler:</span><span class="sxs-lookup"><span data-stu-id="c811b-133">But not the following URLs:</span></span>

- <span data-ttu-id="c811b-134">/ Ürün/apple</span><span class="sxs-lookup"><span data-stu-id="c811b-134">/Product/apple</span></span>
- <span data-ttu-id="c811b-135">/ Ürün</span><span class="sxs-lookup"><span data-stu-id="c811b-135">/Product</span></span>

- <span data-ttu-id="c811b-136">Bu tarayıcı istekleri başka bir rota tarafından işlenecek veya eşleşen hiçbir yol varsa bir *kaynak bulunamadı* hata döndürülecek.</span><span class="sxs-lookup"><span data-stu-id="c811b-136">These browser requests will be handled by another route or, if there are no matching routes, a *The resource could not be found* error will be returned.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c811b-137">[Önceki](creating-custom-routes-cs.md)
> [sonraki](creating-a-custom-route-constraint-cs.md)</span><span class="sxs-lookup"><span data-stu-id="c811b-137">[Previous](creating-custom-routes-cs.md)
[Next](creating-a-custom-route-constraint-cs.md)</span></span>
