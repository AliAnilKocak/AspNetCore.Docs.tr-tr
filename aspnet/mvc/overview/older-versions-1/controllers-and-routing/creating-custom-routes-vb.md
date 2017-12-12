---
uid: mvc/overview/older-versions-1/controllers-and-routing/creating-custom-routes-vb
title: "Özel yollar (VB) oluşturma | Microsoft Docs"
author: microsoft
description: "Bir ASP.NET MVC uygulaması için özel yollar eklemeyi öğrenin. Bu öğreticide, Global.asax dosyasında varsayılan rota tablosu değiştirme öğrenin."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/16/2009
ms.topic: article
ms.assetid: 6ac5758b-6199-42af-adcb-21954b864951
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/creating-custom-routes-vb
msc.type: authoredcontent
ms.openlocfilehash: 3d3161bd1bf74df425d3c53873875a1abcfbfa05
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="creating-custom-routes-vb"></a><span data-ttu-id="81385-104">Özel yollar (VB) oluşturma</span><span class="sxs-lookup"><span data-stu-id="81385-104">Creating Custom Routes (VB)</span></span>
====================
<span data-ttu-id="81385-105">tarafından [Microsoft](https://github.com/microsoft)</span><span class="sxs-lookup"><span data-stu-id="81385-105">by [Microsoft](https://github.com/microsoft)</span></span>

> <span data-ttu-id="81385-106">Bir ASP.NET MVC uygulaması için özel yollar eklemeyi öğrenin.</span><span class="sxs-lookup"><span data-stu-id="81385-106">Learn how to add custom routes to an ASP.NET MVC application.</span></span> <span data-ttu-id="81385-107">Bu öğreticide, Global.asax dosyasında varsayılan rota tablosu değiştirme öğrenin.</span><span class="sxs-lookup"><span data-stu-id="81385-107">In this tutorial, you learn how to modify the default route table in the Global.asax file.</span></span>


<span data-ttu-id="81385-108">Bu öğreticide, bir ASP.NET MVC uygulaması için özel bir yol ekleme konusunda bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="81385-108">In this tutorial, you learn how to add a custom route to an ASP.NET MVC application.</span></span> <span data-ttu-id="81385-109">Varsayılan rota tablosu ile özel bir rota Global.asax dosyasında değişiklik öğrenin.</span><span class="sxs-lookup"><span data-stu-id="81385-109">You learn how to modify the default route table in the Global.asax file with a custom route.</span></span>

<span data-ttu-id="81385-110">ASP.NET MVC uygulamalarında varsayılan rota tablosu düzgün çalışır.</span><span class="sxs-lookup"><span data-stu-id="81385-110">In ASP.NET MVC applications, the default route table will work just fine.</span></span> <span data-ttu-id="81385-111">Ancak, yönlendirme gereksinimlerini özelleştirilmiş fark edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="81385-111">However, you might discover that you have specialized routing needs.</span></span> <span data-ttu-id="81385-112">Bu durumda, özel bir yol oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="81385-112">In that case, you can create a custom route.</span></span>

<span data-ttu-id="81385-113">Örneğin, bir blog uygulaması oluşturduğunuzu düşünün.</span><span class="sxs-lookup"><span data-stu-id="81385-113">Imagine, for example, that you are building a blog application.</span></span> <span data-ttu-id="81385-114">Şuna gelen istekleri işleyen isteyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="81385-114">You might want to handle incoming requests that look like this:</span></span>

<span data-ttu-id="81385-115">/ Arşiv/12-25-2009</span><span class="sxs-lookup"><span data-stu-id="81385-115">/Archive/12-25-2009</span></span>

<span data-ttu-id="81385-116">Bir kullanıcı bu isteği girdiğinde, karşılık gelen blog girişine tarihe döndürmek istediğiniz 25/12/2009.</span><span class="sxs-lookup"><span data-stu-id="81385-116">When a user enters this request, you want to return the blog entry that corresponds to the date 12/25/2009.</span></span> <span data-ttu-id="81385-117">Bu tür bir isteği işlemek için özel bir yol oluşturmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="81385-117">In order to handle this type of request, you need to create a custom route.</span></span>

<span data-ttu-id="81385-118">Blog, /Archive/ gibi görünen hangi istekleri işleme adlı yeni özel bir rota Global.asax dosyasında listeleniyor 1 içeren*giriş tarihi*.</span><span class="sxs-lookup"><span data-stu-id="81385-118">The Global.asax file in Listing 1 contains a new custom route, named Blog, which handles requests that look like /Archive/*entry date*.</span></span>

<span data-ttu-id="81385-119">**1 - Global.asax (özel rota ile) listeleme**</span><span class="sxs-lookup"><span data-stu-id="81385-119">**Listing 1 - Global.asax (with custom route)**</span></span>

[!code-vb[Main](creating-custom-routes-vb/samples/sample1.vb)]

<span data-ttu-id="81385-120">Yol tablosu ekleme yolları sıralama önemlidir.</span><span class="sxs-lookup"><span data-stu-id="81385-120">The order of the routes that you add to the route table is important.</span></span> <span data-ttu-id="81385-121">Bizim yeni özel Blog rota önce var olan varsayılan yol eklenir.</span><span class="sxs-lookup"><span data-stu-id="81385-121">Our new custom Blog route is added before the existing Default route.</span></span> <span data-ttu-id="81385-122">Sırasını tersine, varsayılan yolu her zaman yerine özel rota çağrılmadığı.</span><span class="sxs-lookup"><span data-stu-id="81385-122">If you reversed the order, then the Default route always will get called instead of the custom route.</span></span>

<span data-ttu-id="81385-123">Özel Blog rota başlatır/arşiv/herhangi bir istek eşleşir.</span><span class="sxs-lookup"><span data-stu-id="81385-123">The custom Blog route matches any request that starts with /Archive/.</span></span> <span data-ttu-id="81385-124">Bu nedenle, aşağıdaki URL'ler tümünün eşleşecek:</span><span class="sxs-lookup"><span data-stu-id="81385-124">So, it matches all of the following URLs:</span></span>

- <span data-ttu-id="81385-125">/ Arşiv/12-25-2009</span><span class="sxs-lookup"><span data-stu-id="81385-125">/Archive/12-25-2009</span></span>

- <span data-ttu-id="81385-126">/ Arşiv/10-6-2004</span><span class="sxs-lookup"><span data-stu-id="81385-126">/Archive/10-6-2004</span></span>

- <span data-ttu-id="81385-127">/ Arşiv/apple</span><span class="sxs-lookup"><span data-stu-id="81385-127">/Archive/apple</span></span>

<span data-ttu-id="81385-128">Özel rota gelen istek arşiv adlı bir denetleyiciye eşler ve Entry() eylemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="81385-128">The custom route maps the incoming request to a controller named Archive and invokes the Entry() action.</span></span> <span data-ttu-id="81385-129">Giriş tarihi Entry() yöntemi çağrıldığında entryDate adlı bir parametre geçirilir.</span><span class="sxs-lookup"><span data-stu-id="81385-129">When the Entry() method is called, the entry date is passed as a parameter named entryDate.</span></span>

<span data-ttu-id="81385-130">2. listeleme denetleyicisiyle Blog özel rota kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="81385-130">You can use the Blog custom route with the controller in Listing 2.</span></span>

<span data-ttu-id="81385-131">**2 - ArchiveController.vb listeleme**</span><span class="sxs-lookup"><span data-stu-id="81385-131">**Listing 2 - ArchiveController.vb**</span></span>

[!code-vb[Main](creating-custom-routes-vb/samples/sample2.vb)]

<span data-ttu-id="81385-132">Listeleme 2 Entry() yönteminde DateTime türünde bir parametre kabul eden dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="81385-132">Notice that the Entry() method in Listing 2 accepts a parameter of type DateTime.</span></span> <span data-ttu-id="81385-133">MVC çerçevesi bir DateTime değerine URL'den giriş tarihi otomatik olarak dönüştürmek akıllıca olur.</span><span class="sxs-lookup"><span data-stu-id="81385-133">The MVC framework is smart enough to convert the entry date from the URL into a DateTime value automatically.</span></span> <span data-ttu-id="81385-134">URL giriş tarihi parametresinden DateTime olarak dönüştürülemiyorsa bir hata oluşturulur (bkz: Şekil 1).</span><span class="sxs-lookup"><span data-stu-id="81385-134">If the entry date parameter from the URL cannot be converted to a DateTime, an error is raised (see Figure 1).</span></span>

<span data-ttu-id="81385-135">**Şekil 1 - parametre dönüştürme gelen hata**</span><span class="sxs-lookup"><span data-stu-id="81385-135">**Figure 1 - Error from converting parameter**</span></span>


<span data-ttu-id="81385-136">[![Yeni Proje iletişim kutusu](creating-custom-routes-vb/_static/image1.jpg)](creating-custom-routes-vb/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="81385-136">[![The New Project dialog box](creating-custom-routes-vb/_static/image1.jpg)](creating-custom-routes-vb/_static/image1.png)</span></span>

<span data-ttu-id="81385-137">**Şekil 01**: parametre dönüştürme gelen hata ([tam boyutlu görüntüyü görüntülemek için tıklatın](creating-custom-routes-vb/_static/image2.png))</span><span class="sxs-lookup"><span data-stu-id="81385-137">**Figure 01**: Error from converting parameter ([Click to view full-size image](creating-custom-routes-vb/_static/image2.png))</span></span>


## <a name="summary"></a><span data-ttu-id="81385-138">Özet</span><span class="sxs-lookup"><span data-stu-id="81385-138">Summary</span></span>

<span data-ttu-id="81385-139">Özel rota nasıl oluşturabileceğinizi göstermek için bu öğreticinin amacı oluştu.</span><span class="sxs-lookup"><span data-stu-id="81385-139">The goal of this tutorial was to demonstrate how you can create a custom route.</span></span> <span data-ttu-id="81385-140">Yol tablosu blog girdileri temsil eder Global.asax dosyasında özel bir rota eklemek öğrendiniz.</span><span class="sxs-lookup"><span data-stu-id="81385-140">You learned how to add a custom route to the route table in the Global.asax file that represents blog entries.</span></span> <span data-ttu-id="81385-141">Biz ArchiveController adlı bir denetleyici ve Entry() adlı bir denetleyici eylemi için blog girdileri isteklerinde eşlemek açıklanır.</span><span class="sxs-lookup"><span data-stu-id="81385-141">We discussed how to map requests for blog entries to a controller named ArchiveController and a controller action named Entry().</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="81385-142">[Önceki](asp-net-mvc-controller-overview-vb.md)
[sonraki](creating-a-route-constraint-vb.md)</span><span class="sxs-lookup"><span data-stu-id="81385-142">[Previous](asp-net-mvc-controller-overview-vb.md)
[Next](creating-a-route-constraint-vb.md)</span></span>
