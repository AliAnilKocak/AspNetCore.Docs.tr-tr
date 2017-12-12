---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-2
title: Modelleri ve denetleyicileri ekleyin | Microsoft Docs
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/16/2014
ms.topic: article
ms.assetid: 88908ff8-51a9-40eb-931c-a8139128b680
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-2
msc.type: authoredcontent
ms.openlocfilehash: b75eae11fd99b60864256f79d4770a3007487964
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="add-models-and-controllers"></a><span data-ttu-id="80211-102">Modelleri ve denetleyicileri ekleyin</span><span class="sxs-lookup"><span data-stu-id="80211-102">Add Models and Controllers</span></span>
====================
<span data-ttu-id="80211-103">tarafından [CAN Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="80211-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

[<span data-ttu-id="80211-104">Tamamlanan projenizi indirin</span><span class="sxs-lookup"><span data-stu-id="80211-104">Download Completed Project</span></span>](https://github.com/MikeWasson/BookService)

<span data-ttu-id="80211-105">Bu bölümde, veritabanı varlıklarını tanımlama modeli sınıfları ekleyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="80211-105">In this section, you will add model classes that define the database entities.</span></span> <span data-ttu-id="80211-106">Ardından bu varlıkların CRUD işlemleri Web API denetleyicilerinin ekleyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="80211-106">Then you will add Web API controllers that perform CRUD operations on those entities.</span></span>

## <a name="add-model-classes"></a><span data-ttu-id="80211-107">Model sınıfları ekleme</span><span class="sxs-lookup"><span data-stu-id="80211-107">Add Model Classes</span></span>

<span data-ttu-id="80211-108">Bu öğreticide, veritabanı "Code First" yaklaşımı Entity Framework (EF) kullanarak oluşturacağız.</span><span class="sxs-lookup"><span data-stu-id="80211-108">In this tutorial, we'll create the database by using the "Code First" approach to Entity Framework (EF).</span></span> <span data-ttu-id="80211-109">Code First ile veritabanı tablolarında karşılık gelen C# sınıfları yazma ve veritabanı EF oluşturur.</span><span class="sxs-lookup"><span data-stu-id="80211-109">With Code First, you write C# classes that correspond to database tables, and EF creates the database.</span></span> <span data-ttu-id="80211-110">(Daha fazla bilgi için bkz: [Entity Framework Geliştirme yaklaşımları](https://msdn.microsoft.com/en-us/library/ms178359%28v=vs.110%29.aspx#dbfmfcf).)</span><span class="sxs-lookup"><span data-stu-id="80211-110">(For more information, see [Entity Framework Development Approaches](https://msdn.microsoft.com/en-us/library/ms178359%28v=vs.110%29.aspx#dbfmfcf).)</span></span>

<span data-ttu-id="80211-111">Bizim etki alanı nesnelerini POCOs (düz eski CLR nesneler) tanımlayarak başlatın.</span><span class="sxs-lookup"><span data-stu-id="80211-111">We start by defining our domain objects as POCOs (plain-old CLR objects).</span></span> <span data-ttu-id="80211-112">Aşağıdaki POCOs oluşturacağız:</span><span class="sxs-lookup"><span data-stu-id="80211-112">We will create the following POCOs:</span></span>

- <span data-ttu-id="80211-113">Yazar</span><span class="sxs-lookup"><span data-stu-id="80211-113">Author</span></span>
- <span data-ttu-id="80211-114">Rehberi</span><span class="sxs-lookup"><span data-stu-id="80211-114">Book</span></span>

<span data-ttu-id="80211-115">Çözüm Gezgini'nde modeller klasörü sağ tıklatın.</span><span class="sxs-lookup"><span data-stu-id="80211-115">In Solution Explorer, right click the Models folder.</span></span> <span data-ttu-id="80211-116">Seçin **Ekle**seçeneğini belirleyip **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="80211-116">Select **Add**, then select **Class**.</span></span> <span data-ttu-id="80211-117">Sınıf adını `Author`.</span><span class="sxs-lookup"><span data-stu-id="80211-117">Name the class `Author`.</span></span>

![](part-2/_static/image1.png)

<span data-ttu-id="80211-118">Tüm Author.cs Demirbaş kodu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="80211-118">Replace all of the boilerplate code in Author.cs with the following code.</span></span>

[!code-csharp[Main](part-2/samples/sample1.cs)]

<span data-ttu-id="80211-119">Adlı başka bir sınıf ekleyin `Book`, aşağıdaki kod ile.</span><span class="sxs-lookup"><span data-stu-id="80211-119">Add another class named `Book`, with the following code.</span></span>

[!code-csharp[Main](part-2/samples/sample2.cs)]

<span data-ttu-id="80211-120">Entity Framework veritabanı tabloları oluşturmak için bu modeller kullanır.</span><span class="sxs-lookup"><span data-stu-id="80211-120">Entity Framework will use these models to create database tables.</span></span> <span data-ttu-id="80211-121">Her model için `Id` özelliği, veritabanı tablosunun birincil anahtar sütunu hale gelir.</span><span class="sxs-lookup"><span data-stu-id="80211-121">For each model, the `Id` property will become the primary key column of the database table.</span></span>

<span data-ttu-id="80211-122">Kitap sınıfında `AuthorId` içine yabancı anahtar tanımlayan `Author` tablo.</span><span class="sxs-lookup"><span data-stu-id="80211-122">In the Book class, the `AuthorId` defines a foreign key into the `Author` table.</span></span> <span data-ttu-id="80211-123">(Kolaylık sağlamak için t her kitap tek Yazar olduğunu varsayarak.) Ayrıca Gezinti özelliğine ilgili kitap sınıf içerir `Author`.</span><span class="sxs-lookup"><span data-stu-id="80211-123">(For simplicity, I'm assuming that each book has a single author.) The book class also contains a navigation property to the related `Author`.</span></span> <span data-ttu-id="80211-124">Gezinti özelliği ilgili erişmek için kullanabileceğiniz `Author` kod.</span><span class="sxs-lookup"><span data-stu-id="80211-124">You can use the navigation property to access the related `Author` in code.</span></span> <span data-ttu-id="80211-125">Bölüm 4, gezinti özellikleri hakkında daha fazla söyleyin [işleme varlık ilişkileriyle](part-4.md).</span><span class="sxs-lookup"><span data-stu-id="80211-125">I say more about navigation properties in part 4, [Handling Entity Relations](part-4.md).</span></span>

## <a name="add-web-api-controllers"></a><span data-ttu-id="80211-126">Web API denetleyicilerinin ekleme</span><span class="sxs-lookup"><span data-stu-id="80211-126">Add Web API Controllers</span></span>

<span data-ttu-id="80211-127">Bu bölümde, CRUD işlemleri destekleyen Web API denetleyicilerinin ekleyeceğiz (oluşturma, okuma, güncelleştirme ve silme).</span><span class="sxs-lookup"><span data-stu-id="80211-127">In this section, we'll add Web API controllers that support CRUD operations (create, read, update, and delete).</span></span> <span data-ttu-id="80211-128">Denetleyicileri Entity Framework veritabanı katmanı ile iletişim kurmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="80211-128">The controllers will use Entity Framework to communicate with the database layer.</span></span>

<span data-ttu-id="80211-129">İlk olarak, dosya Controllers/ValuesController.cs silebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="80211-129">First, you can delete the file Controllers/ValuesController.cs.</span></span> <span data-ttu-id="80211-130">Bu dosya bir örnek Web API denetleyicisi içeriyor, ancak bu öğretici için gerekmez.</span><span class="sxs-lookup"><span data-stu-id="80211-130">This file contains an example Web API controller, but you don't need it for this tutorial.</span></span>

![](part-2/_static/image2.png)

<span data-ttu-id="80211-131">Ardından, projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="80211-131">Next, build the project.</span></span> <span data-ttu-id="80211-132">Web API yapı iskelesi yansıma derlenmiş derleme gereken şekilde modeli sınıfları bulmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="80211-132">The Web API scaffolding uses reflection to find the model classes, so it needs the compiled assembly.</span></span>

<span data-ttu-id="80211-133">Çözüm Gezgini'nde denetleyicileri klasörü sağ tıklatın.</span><span class="sxs-lookup"><span data-stu-id="80211-133">In Solution Explorer, right-click the Controllers folder.</span></span> <span data-ttu-id="80211-134">Seçin **Ekle**seçeneğini belirleyip **denetleyicisi**.</span><span class="sxs-lookup"><span data-stu-id="80211-134">Select **Add**, then select **Controller**.</span></span>

![](part-2/_static/image3.png)

<span data-ttu-id="80211-135">İçinde **İskele Ekle** iletişim kutusunda "Web API 2 denetleyici Entity Framework kullanarak Eylemler ile".</span><span class="sxs-lookup"><span data-stu-id="80211-135">In the **Add Scaffold** dialog, select "Web API 2 Controller with actions, using Entity Framework".</span></span> <span data-ttu-id="80211-136">**Ekle**'yi tıklatın.</span><span class="sxs-lookup"><span data-stu-id="80211-136">Click **Add**.</span></span>

![](part-2/_static/image4.png)

<span data-ttu-id="80211-137">İçinde **denetleyici Ekle** iletişim kutusunda, aşağıdakileri yapın:</span><span class="sxs-lookup"><span data-stu-id="80211-137">In the **Add Controller** dialog, do the following:</span></span>

1. <span data-ttu-id="80211-138">İçinde **Model sınıfı** açılan listesinde, select `Author` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="80211-138">In the **Model class** dropdown, select the `Author` class.</span></span> <span data-ttu-id="80211-139">(Bunu açılır listede görmüyorsanız, proje yerleşik emin olun.)</span><span class="sxs-lookup"><span data-stu-id="80211-139">(If you don't see it listed in the dropdown, make sure that you built the project.)</span></span>
2. <span data-ttu-id="80211-140">"Kullanım zaman uyumsuz denetleyici eylemlerini" denetleyin.</span><span class="sxs-lookup"><span data-stu-id="80211-140">Check "Use async controller actions".</span></span>
3. <span data-ttu-id="80211-141">Denetleyici adı olarak bırakın &quot;AuthorsController&quot;.</span><span class="sxs-lookup"><span data-stu-id="80211-141">Leave the controller name as &quot;AuthorsController&quot;.</span></span>
4. <span data-ttu-id="80211-142">Tıklatın artı (+) düğmesini yanına **veri bağlamı sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="80211-142">Click plus (+) button next to **Data Context Class**.</span></span>

![](part-2/_static/image5.png)

<span data-ttu-id="80211-143">İçinde **yeni veri bağlamı** iletişim kutusunda, varsayılan adı bırakın ve tıklayın **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="80211-143">In the **New Data Context** dialog, leave the default name and click **Add**.</span></span>

![](part-2/_static/image6.png)

<span data-ttu-id="80211-144">Tıklatın **Ekle** tamamlamak için **denetleyici Ekle** iletişim.</span><span class="sxs-lookup"><span data-stu-id="80211-144">Click **Add** to complete the **Add Controller** dialog.</span></span> <span data-ttu-id="80211-145">İletişim kutusu iki sınıf projenize ekler:</span><span class="sxs-lookup"><span data-stu-id="80211-145">The dialog adds two classes to your project:</span></span>

- <span data-ttu-id="80211-146">`AuthorsController`Web API denetleyicisi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="80211-146">`AuthorsController` defines a Web API controller.</span></span> <span data-ttu-id="80211-147">İstemcilerin yazarlar listesinde CRUD işlemleri gerçekleştirmek için kullandığı REST API denetleyicisi uygular.</span><span class="sxs-lookup"><span data-stu-id="80211-147">The controller implements the REST API that clients use to perform CRUD operations on the list of authors.</span></span>
- <span data-ttu-id="80211-148">`BookServiceContext`Veritabanı, değişiklik izleme ve kalıcı veri verilerini veritabanına nesnelerle doldurmak içeren çalışma zamanı sırasında varlık nesneleri yönetir.</span><span class="sxs-lookup"><span data-stu-id="80211-148">`BookServiceContext` manages entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span> <span data-ttu-id="80211-149">Öğesinden devralınan `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="80211-149">It inherits from `DbContext`.</span></span>

![](part-2/_static/image7.png)

<span data-ttu-id="80211-150">Bu noktada, projeyi yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="80211-150">At this point, build the project again.</span></span> <span data-ttu-id="80211-151">Şimdi bir API denetleyicisi için eklemek için aynı adımlarını `Book` varlıklar.</span><span class="sxs-lookup"><span data-stu-id="80211-151">Now go through the same steps to add an API controller for `Book` entities.</span></span> <span data-ttu-id="80211-152">Bu süre, select `Book` model sınıfı ve varolan seçin `BookServiceContext` veri bağlamı sınıfı için sınıf.</span><span class="sxs-lookup"><span data-stu-id="80211-152">This time, select `Book` for the model class, and select the existing `BookServiceContext` class for the data context class.</span></span> <span data-ttu-id="80211-153">(Yeni bir veri bağlamı oluşturmayın.) Tıklatın **Ekle** denetleyicisi eklemek için.</span><span class="sxs-lookup"><span data-stu-id="80211-153">(Don't create a new data context.) Click **Add** to add the controller.</span></span>

![](part-2/_static/image8.png)

>[!div class="step-by-step"]
<span data-ttu-id="80211-154">[Önceki](part-1.md)
[sonraki](part-3.md)</span><span class="sxs-lookup"><span data-stu-id="80211-154">[Previous](part-1.md)
[Next](part-3.md)</span></span>
