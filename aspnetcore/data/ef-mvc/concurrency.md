---
title: "Öğretici: EF Core eşzamanlılık-ASP.NET MVC 'yi işle"
description: Bu öğreticide, birden çok kullanıcı aynı anda aynı varlık güncelleştirdiğinizde çakışmalarına gösterilmektedir.
author: tdykstra
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: d4b83bd269b067ab6fa9e8666109bacc602589bd
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583245"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="2e7e2-103">Öğretici: EF Core eşzamanlılık-ASP.NET MVC 'yi işle</span><span class="sxs-lookup"><span data-stu-id="2e7e2-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="2e7e2-104">Önceki öğreticilerde, verileri güncelleştirme hakkında daha fazla öğrendiniz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="2e7e2-105">Bu öğreticide, birden çok kullanıcı aynı anda aynı varlık güncelleştirdiğinizde çakışmalarına gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="2e7e2-106">Departman varlığıyla çalışan ve eşzamanlılık hatalarını işleyecek Web sayfaları oluşturacaksınız.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="2e7e2-107">Aşağıdaki çizimler, bir eşzamanlılık çakışması oluşursa görüntülenen bazı iletiler dahil olmak üzere, düzenleme ve silme sayfalarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Bölüm düzenleme sayfası](concurrency/_static/edit-error.png)

![Departmanı silme sayfası](concurrency/_static/delete-error.png)

<span data-ttu-id="2e7e2-110">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2e7e2-111">Eşzamanlılık çakışmaları hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="2e7e2-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="2e7e2-112">İzleme özelliği Ekle</span><span class="sxs-lookup"><span data-stu-id="2e7e2-112">Add a tracking property</span></span>
> * <span data-ttu-id="2e7e2-113">Departmanlar denetleyicisi ve görünümleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="2e7e2-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="2e7e2-114">Dizin görünümünü Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="2e7e2-114">Update Index view</span></span>
> * <span data-ttu-id="2e7e2-115">Düzenleme yöntemlerini Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="2e7e2-115">Update Edit methods</span></span>
> * <span data-ttu-id="2e7e2-116">Güncelleştirme düzenleme görünümü</span><span class="sxs-lookup"><span data-stu-id="2e7e2-116">Update Edit view</span></span>
> * <span data-ttu-id="2e7e2-117">Eşzamanlılık çakışmalarını test et</span><span class="sxs-lookup"><span data-stu-id="2e7e2-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="2e7e2-118">Silme sayfası</span><span class="sxs-lookup"><span data-stu-id="2e7e2-118">Update the Delete page</span></span>
> * <span data-ttu-id="2e7e2-119">Güncelleştirme ayrıntıları ve görünüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="2e7e2-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2e7e2-120">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="2e7e2-120">Prerequisites</span></span>

* [<span data-ttu-id="2e7e2-121">İlgili verileri güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="2e7e2-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="2e7e2-122">Eşzamanlılık çakışmaları</span><span class="sxs-lookup"><span data-stu-id="2e7e2-122">Concurrency conflicts</span></span>

<span data-ttu-id="2e7e2-123">Bir Kullanıcı bir varlığın verilerini düzenlemek için bir varlık verileri görüntülediğinde bir eşzamanlılık çakışması oluşur ve sonra, ilk kullanıcının değişikliği veritabanına yazılmadan önce diğer Kullanıcı aynı varlığın verilerini günceller.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="2e7e2-124">Bu tür çakışmaların algılanmasını etkinleştirmezseniz, veritabanını güncelleştirme son olarak diğer kullanıcının değişikliklerinin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="2e7e2-125">Birçok uygulamada, bu risk kabul edilebilir: birkaç Kullanıcı veya birkaç güncelleştirme varsa veya bazı değişikliklerin üzerine yazılırsa gerçekten önemli değilse, eşzamanlılık için programlama maliyeti avantajdan yararlanabilir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="2e7e2-126">Bu durumda, uygulamayı eşzamanlılık çakışmalarını işleyecek şekilde yapılandırmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="2e7e2-127">Kötümser eşzamanlılık (kilitleme)</span><span class="sxs-lookup"><span data-stu-id="2e7e2-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="2e7e2-128">Uygulamanızın eşzamanlılık senaryolarında yanlışlıkla veri kaybını önlemesi gerekiyorsa, bunu yapmanın bir yolu veritabanı kilitlerini kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="2e7e2-129">Bu, Kötümser eşzamanlılık olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="2e7e2-130">Örneğin, bir veritabanından bir satırı okuyabilmeniz için, salt okunurdur veya güncelleştirme erişimi için bir kilit isteyin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="2e7e2-131">Bir satırı güncelleştirme erişimi için kilitlerseniz, başka hiçbir kullanıcının satırı değiştirme sürecinde olan verilerin bir kopyasını alması için salt okunurdur veya güncelleştirme erişimi için bu satırı kilitlemesine izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="2e7e2-132">Bir satırı salt okuma erişimi için kilitlerseniz, diğerleri dosyayı salt okuma erişimi için de kilitleyip güncelleştirme için de kilitleyebilirler.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="2e7e2-133">Kilitleri yönetmek dezavantajlara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="2e7e2-134">Program, karmaşık olabilir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-134">It can be complex to program.</span></span> <span data-ttu-id="2e7e2-135">Önemli veritabanı yönetim kaynakları gerektirir ve bir uygulamanın kullanıcı sayısı arttıkça performans sorunlarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="2e7e2-136">Bu nedenlerden dolayı, tüm veritabanı yönetim sistemleri Kötümser eşzamanlılık 'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="2e7e2-137">Entity Framework Core, BT için yerleşik destek sağlamaz ve bu öğretici bunu nasıl uygulayacağınızı göstermez.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="2e7e2-138">İyimser Eşzamanlılık</span><span class="sxs-lookup"><span data-stu-id="2e7e2-138">Optimistic Concurrency</span></span>

<span data-ttu-id="2e7e2-139">Kötümser eşzamanlılık yerine iyimser eşzamanlılık yapılır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="2e7e2-140">İyimser eşzamanlılık, eşzamanlılık çakışmalarının gerçekleşmesine ve sonra uygun şekilde yeniden davranmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="2e7e2-141">Örneğin, Gamze departman düzenleme sayfasını ziyaret ettiğinde, Ingilizce departmanı $350.000,00 olan bütçe tutarını $0,00 olarak değiştirir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Bütçe 0 olarak değiştirme](concurrency/_static/change-budget.png)

<span data-ttu-id="2e7e2-143">Jane tıkladığında önce **Kaydet**, John aynı sayfayı ziyaret eder ve alanın başlangıç tarihi 1/9/2013 1/9/2007'deki değiştirir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-143">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![2013'e başlangıç tarihini değiştirme](concurrency/_static/change-date.png)

<span data-ttu-id="2e7e2-145">Gamze önce **Kaydet** ' i tıklatır ve tarayıcı dizin sayfasına döndüğünde değişikliği görür.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Bütçe sıfır olarak değiştirildi](concurrency/_static/budget-zero.png)

<span data-ttu-id="2e7e2-147">Ardından John, hala $350.000,00 bütçesini gösteren bir düzenleme sayfasında **Kaydet** ' e tıklamakta.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="2e7e2-148">Sonraki işlemin ne eşzamanlılık çakışmalarını nasıl ele tarafından belirlenir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="2e7e2-149">Bazı seçenekler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-149">Some of the options include the following:</span></span>

* <span data-ttu-id="2e7e2-150">Bir kullanıcının hangi özelliği değiştirdiği ve yalnızca ilgili sütunları veritabanında güncelleştirdiğinden haberdar olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="2e7e2-151">Örnek senaryoda, iki kullanıcı tarafından farklı özellikler güncelleştirildiğinden hiçbir veri kaybolmaz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="2e7e2-152">Ingilizce bölüme bir dahaki sefer ilk kez gözattığında, hem gamze 'nin hem de John 'un değişikliklerini görür; başlangıç tarihi 9/1/2013 ve sıfır dolar bir bütçe olur.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="2e7e2-153">Bu güncelleştirme yöntemi, veri kaybına neden olabilecek çakışmaların sayısını azaltabilir, ancak bir varlığın aynı özelliğinde rekabet değişiklikleri yapılırsa veri kaybını önleyebilir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="2e7e2-154">Entity Framework bu şekilde çalışıp çalışmadığını, güncelleştirme kodunuzu nasıl uygulayadığınıza bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="2e7e2-155">Genellikle bir Web uygulamasında pratik değildir, çünkü bir varlığın tüm özgün özellik değerlerini ve yeni değerleri izlemek için büyük miktarlarda durum tutmanızı gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="2e7e2-156">Büyük miktarlarda durum bulundurma, uygulama performansını etkileyebilir çünkü sunucu kaynakları gerektirir ya da Web sayfasının kendisine (örneğin, gizli alanlarda) veya bir tanımlama bilgisinde yer almalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="2e7e2-157">Gamze'nin değişikliğinin üzerine Can'ın değişiklik sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="2e7e2-158">Ingilizce bölüme bir dahaki sefer gözattığında, 9/1/2013 ve geri yüklenen $350.000,00 değerini görür.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="2e7e2-159">Bu, *Istemci WINS* veya *son WINS* senaryosu olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="2e7e2-160">(Tüm istemci değerlerinden veri deposunda nedir üzerinde önceliklidir.) Bu bölümün giriş bölümünde belirtildiği gibi, eşzamanlılık işleme için herhangi bir kodlama yapmazsanız, bu otomatik olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="2e7e2-161">John 'un değişikliğini veritabanında güncelleştirilmesini engelleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="2e7e2-162">Genellikle bir hata iletisi görüntüler, verilerin geçerli durumunu gösterir ve yine de bunu yapmak istiyorsa, yaptığı değişiklikleri yeniden uygular.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="2e7e2-163">Bu adlı bir *Store WINS* senaryo.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="2e7e2-164">(Veri deposu değerlerini istemci tarafından gönderilen değerler önceliklidir.) Bu öğreticide mağaza WINS senaryosunu uygulayacaksınız.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="2e7e2-165">Bu yöntem, bir kullanıcının neler olduğunu bildirmeden önce hiçbir değişikliğin üzerine yazılmamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="2e7e2-166">Eşzamanlılık çakışmalarını algılama</span><span class="sxs-lookup"><span data-stu-id="2e7e2-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="2e7e2-167">Entity Framework oluşturduğu özel durumları işleyerek `DbConcurrencyException` çakışmaları çözebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="2e7e2-168">Bu özel durumların ne zaman throw hakkında bilgi edinmek için Entity Framework çakışmaları algılayabilmelidir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="2e7e2-169">Bu nedenle, veritabanını ve veri modelini uygun şekilde yapılandırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="2e7e2-170">Çakışma algılamayı etkinleştirmeye yönelik bazı seçenekler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="2e7e2-171">Veritabanı tablosunda, bir satırın ne zaman değiştirildiğini belirlemede kullanılabilecek bir izleme sütunu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="2e7e2-172">Daha sonra Entity Framework SQL Update veya delete komutlarının WHERE yan tümcesinde bu sütunu içerecek şekilde yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="2e7e2-173">İzleme sütununun veri türü genellikle `rowversion`olur.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="2e7e2-174">`rowversion` Değer, satır her güncelleştirildiği zaman artılan sıralı bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="2e7e2-175">Bir Update veya delete komutunda WHERE yan tümcesi, izleme sütununun (orijinal satır sürümü) orijinal değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="2e7e2-176">Güncelleştirilmekte olan satır başka bir kullanıcı tarafından değiştirilmişse, `rowversion` sütundaki değer özgün değerden farklıdır, bu nedenle Update veya DELETE deyimi WHERE yan tümcesi nedeniyle güncelleştirilecek satırı bulamaz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="2e7e2-177">Entity Framework, Update veya delete komutuyla hiçbir satır güncelleştirilmediğini bulduğunda (yani, etkilenen satır sayısı sıfır olduğunda), bunu bir eşzamanlılık çakışması olarak yorumlar.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="2e7e2-178">Entity Framework, Update ve DELETE komutlarının WHERE yan tümcesindeki tablodaki her sütunun özgün değerlerini içerecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="2e7e2-179">İlk seçenekte olduğu gibi, satırdaki herhangi bir şey satırın ilk okuduğundan beri değiştiyse WHERE yan tümcesi güncelleştirilecek bir satır döndürmez, bu da Entity Framework eşzamanlılık çakışması olarak yorumlar.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="2e7e2-180">Birçok sütunu olan veritabanı tablolarında, bu yaklaşım çok büyük WHERE yan tümceleriyle sonuçlanabilir ve büyük miktarlarda durum bulundurmasını gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="2e7e2-181">Daha önce belirtildiği gibi, büyük miktarlarda durumu korumak uygulama performansını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="2e7e2-182">Bu nedenle bu yaklaşım genellikle önerilmez ve bu öğreticide kullanılan yöntem değildir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="2e7e2-183">Bu yaklaşımı eşzamanlılık 'e uygulamak istiyorsanız, kendisine `ConcurrencyCheck` özniteliği ekleyerek eşzamanlılık izlemek istediğiniz varlıktaki tüm birincil anahtar olmayan Özellikleri işaretlemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="2e7e2-184">Bu değişiklik Entity Framework, tüm sütunları Update ve DELETE deyimlerinin SQL WHERE yan tümcesinde içermesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="2e7e2-185">Bu öğreticinin geri kalanında, departman varlığına bir `rowversion` izleme özelliği ekleyecek, denetleyici ve görünümler oluşturacak ve her şeyin doğru şekilde çalıştığını doğrulamak için test edeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="2e7e2-186">İzleme özelliği Ekle</span><span class="sxs-lookup"><span data-stu-id="2e7e2-186">Add a tracking property</span></span>

<span data-ttu-id="2e7e2-187">İçinde *Models/Department.cs*, RowVersion adlı izleme özelliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-187">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="2e7e2-188">`Timestamp` Özniteliği, bu sütunun veritabanına gönderilen WHERE yan tümcesine ve DELETE komutlarına dahil edileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="2e7e2-189">Özniteliği, önceki SQL Server `Timestamp` sürümleri SQL `timestamp` `rowversion` veri türü tarafından değiştirilmeden önce kullanıldığından, bu öznitelik çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="2e7e2-190">İçin `rowversion` .NET türü bir bayt dizisidir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="2e7e2-191">Fluent API kullanmayı tercih ediyorsanız, aşağıdaki örnekte gösterildiği gibi, izleme özelliğini `IsConcurrencyToken` belirtmek için yöntemini ( *Data/SchoolContext. cs*) kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="2e7e2-192">Bir özellik ekleyerek, veritabanı modelini değiştirdiğiniz için başka bir geçiş yapmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="2e7e2-193">Değişikliklerinizi kaydedin ve projeyi derleyin ve ardından komut penceresine aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```console
dotnet ef migrations add RowVersion
```

```console
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="2e7e2-194">Departmanlar denetleyicisi ve görünümleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="2e7e2-194">Create Departments controller and views</span></span>

<span data-ttu-id="2e7e2-195">Daha önce öğrenciler, Kurslar ve Eğitmenler için yaptığınız gibi bir departman denetleyicisini ve görünümlerini derden katlayın.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Yapı iskelesi departmanı](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="2e7e2-197">*DepartmentsController.cs* dosyasında, "FirstMidName" sözcüğünün dört yinelemesini "FullName" olarak değiştirin, böylece Departman Yöneticisi açılan listeleri yalnızca soyadı yerine eğitmenin tam adını içerecektir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="2e7e2-198">Dizin görünümünü Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="2e7e2-198">Update Index view</span></span>

<span data-ttu-id="2e7e2-199">Yapı iskelesi altyapısı dizin görünümünde bir ROWVERSION sütunu oluşturdu, ancak bu alan gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="2e7e2-200">*Views/departmanlar/Index. cshtml* içindeki kodu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="2e7e2-201">Bu, başlığı "departmanlar" olarak değiştirir, RowVersion sütununu siler ve yöneticinin adı yerine tam adı gösterir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="2e7e2-202">Düzenleme yöntemlerini Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="2e7e2-202">Update Edit methods</span></span>

<span data-ttu-id="2e7e2-203">Hem HttpGet `Edit` yönteminde `Details` hem de yönteminde, öğesini ekleyin `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="2e7e2-204">HttpGet `Edit` yönteminde, yönetici için Eager yüklemesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="2e7e2-205">HttpPost `Edit` yöntemi için mevcut kodu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="2e7e2-206">Kod, güncellenen departmanı okumaya çalışırken başlar.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="2e7e2-207">`FirstOrDefaultAsync` Yöntem null döndürürse, departman başka bir kullanıcı tarafından silindi.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-207">If the `FirstOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="2e7e2-208">Bu durumda, kod, düzenleme sayfasının bir hata iletisiyle yeniden görüntülenebilmesi için bir departman varlığı oluşturmak üzere postalanan form değerlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="2e7e2-209">Alternatif olarak, departman alanlarını yeniden görüntülemeden yalnızca bir hata iletisi görüntülediğinizde, departman varlığını yeniden oluşturmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="2e7e2-210">Görünüm özgün `RowVersion` değeri gizli bir alanda depolar ve bu yöntem `rowVersion` parametresindeki değeri alır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="2e7e2-211">' İ çağırmadan `SaveChanges`önce, söz konusu özgün `RowVersion` özellik değerini `OriginalValues` varlığa yönelik koleksiyonuna koymanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="2e7e2-212">Entity Framework bir SQL Update komutu oluşturduğunda, bu komut özgün `RowVersion` değere sahip bir satırı aramak için bir where yan tümcesi içerecektir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="2e7e2-213">Update komutundan hiçbir satır etkilenmiyorsa (özgün `RowVersion` değere sahip hiçbir satır yoksa) Entity Framework bir `DbUpdateConcurrencyException` özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="2e7e2-214">Bu özel durum için catch bloğundaki kod, özel durum nesnesindeki `Entries` özelliğinden güncelleştirilmiş değerlere sahip etkilenen departman varlığını alır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="2e7e2-215">Koleksiyonda yalnızca bir `EntityEntry` nesne olacak. `Entries`</span><span class="sxs-lookup"><span data-stu-id="2e7e2-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="2e7e2-216">Kullanıcı tarafından girilen yeni değerleri ve geçerli veritabanı değerlerini almak için bu nesneyi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="2e7e2-217">Kod, kullanıcının düzenleme sayfasına girdikten farklı veritabanı değerleri olan her bir sütun için özel bir hata iletisi ekler (yalnızca bir alan, kısaltma için burada gösterilir).</span><span class="sxs-lookup"><span data-stu-id="2e7e2-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="2e7e2-218">Son olarak, kod `RowVersion` değerini `departmentToUpdate` veritabanından alınan yeni değere ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="2e7e2-219">Bu yeni `RowVersion` değer, düzenleme sayfası yeniden görüntülenirken gizli alanda saklanır ve Kullanıcı **Kaydet**' i tıkladığında, düzenleme sayfasının yeniden görüntülenmesinden bu yana yalnızca gerçekleşen eşzamanlılık hataları yakalanacaktır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="2e7e2-220">`ModelState.Remove` Deyimi, çünkü gereklidir `ModelState` eski olan `RowVersion` değeri.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="2e7e2-221">Görünümde, `ModelState` bir alanın değeri, her ikisi de varsa Model Özellik değerlerinden önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="2e7e2-222">Güncelleştirme düzenleme görünümü</span><span class="sxs-lookup"><span data-stu-id="2e7e2-222">Update Edit view</span></span>

<span data-ttu-id="2e7e2-223">*Görünümler/departmanlar/Düzenle. cshtml*'de aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-223">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="2e7e2-224">Özellik değerini kaydetmek `RowVersion` için, `DepartmentID` özelliği için gizli alandan hemen sonra bir gizli alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="2e7e2-225">Açılan listeye "Yönetici Seç" seçeneği ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="2e7e2-226">Eşzamanlılık çakışmalarını test et</span><span class="sxs-lookup"><span data-stu-id="2e7e2-226">Test concurrency conflicts</span></span>

<span data-ttu-id="2e7e2-227">Uygulamayı çalıştırın ve departmanlar dizini sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="2e7e2-228">Ingilizce departman için **düzenleme** köprüsüne sağ tıklayın ve **Yeni sekmesinde aç**' ı seçin ve ardından İngilizce bölümünün **düzenleme** Köprüsü ' ne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="2e7e2-229">İki tarayıcı sekmesi artık aynı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="2e7e2-230">İlk tarayıcı sekmesinde bir alanı değiştirin ve **Kaydet**' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-230">Change a field in the first browser tab and click **Save**.</span></span>

![Departman düzenleme değişikliğinden sonra sayfa 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="2e7e2-232">Tarayıcı, değiştirilen değeri olan dizin sayfasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="2e7e2-233">İkinci tarayıcı sekmesinden bir alanı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-233">Change a field in the second browser tab.</span></span>

![Departman düzenleme değişikliğinden sonra sayfa 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="2e7e2-235">**Kaydet**'e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-235">Click **Save**.</span></span> <span data-ttu-id="2e7e2-236">Bir hata iletisi görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-236">You see an error message:</span></span>

![Departman düzenleme sayfa hata iletisi](concurrency/_static/edit-error.png)

<span data-ttu-id="2e7e2-238">Tıklayın **Kaydet** yeniden.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-238">Click **Save** again.</span></span> <span data-ttu-id="2e7e2-239">İkinci tarayıcı sekmesinde girdiğiniz değer kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="2e7e2-240">Dizin sayfası göründüğünde kaydedilen değerleri görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="2e7e2-241">Silme sayfası</span><span class="sxs-lookup"><span data-stu-id="2e7e2-241">Update the Delete page</span></span>

<span data-ttu-id="2e7e2-242">Silme sayfası için Entity Framework, başka birinin departmanı benzer bir şekilde düzenlemesinden kaynaklanan eşzamanlılık çakışmalarını algılar.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="2e7e2-243">HttpGet `Delete` yönteminde onay görünümü görüntülendiğinde görünüm, gizli bir alanda orijinal `RowVersion` değeri içerir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="2e7e2-244">Bu değer daha sonra Kullanıcı silmeyi onayladığında çağrılan `Delete` HttpPost yöntemi için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="2e7e2-245">Entity Framework, SQL DELETE komutunu oluşturduğunda, özgün `RowVersion` değeri olan bir where yan tümcesi içerir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="2e7e2-246">Komut, sıfır satır etkileirse (silme onayı sayfası görüntülendikten sonra satırın değiştiği anlamına gelir), bir eşzamanlılık özel durumu oluşturulur ve HttpGet `Delete` yöntemi bir hata bayrağıyla birlikte çağrıldığında, hata iletisiyle onay sayfası.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="2e7e2-247">Satır başka bir kullanıcı tarafından silindiğinden, bu durumda herhangi bir hata iletisi görüntülenmediğinden sıfır satırların etkilenmesi de mümkündür.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="2e7e2-248">Departmanlar denetleyicisindeki silme yöntemlerini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="2e7e2-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="2e7e2-249">*DepartmentsController.cs*' de, HttpGet `Delete` yöntemini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-249">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="2e7e2-250">Yöntemi, sayfanın bir eşzamanlılık hatasından sonra yeniden görüntülenip görüntülenmeyeceğini belirten isteğe bağlı bir parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="2e7e2-251">Bu bayrak true ise ve belirtilen departman artık mevcut değilse, başka bir kullanıcı tarafından silindi.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="2e7e2-252">Bu durumda, kod dizin sayfasına yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="2e7e2-253">Bu bayrak true ise ve departman varsa, başka bir kullanıcı tarafından değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="2e7e2-254">Bu durumda, kod kullanarak `ViewData`görünüme bir hata mesajı gönderir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="2e7e2-255">HttpPost `Delete` yöntemindeki (adlı `DeleteConfirmed`) kodu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="2e7e2-256">Az önce değiştirdiğiniz scafkatlanmış kodda, bu yöntem yalnızca bir kayıt KIMLIĞI kabul etti:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="2e7e2-257">Bu parametreyi model Ciltçi tarafından oluşturulan bir departman varlığı örneğine değiştirdiniz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="2e7e2-258">Bu, kayıt anahtarına ek olarak ROWVERSION özellik değerine EF erişimi verir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="2e7e2-259">Ayrıca, eylem yöntemi adını `DeleteConfirmed` olarak olarak `Delete`değiştirdiniz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="2e7e2-260">Yapı iskelesi kodu, HttpPost yöntemine `DeleteConfirmed` benzersiz bir imza vermek için adı kullandı.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="2e7e2-261">(CLR aşırı yüklenmiş yöntemlerin farklı yöntem parametrelerine sahip olmasını gerektirir.) İmzalar benzersiz olduğuna göre, MVC kuralını seçebilir ve HttpPost ve HttpGet silme yöntemleri için aynı adı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="2e7e2-262">Departman zaten silinirse, `AnyAsync` yöntem false döndürür ve uygulama yalnızca dizin yöntemine geri döner.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="2e7e2-263">Bir eşzamanlılık hatası yakalanmışsa, kod silme onayı sayfasını yeniden görüntüler ve bir eşzamanlılık hata mesajı görüntülemesi gerektiğini belirten bir bayrak sağlar.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="2e7e2-264">Silme görünümünü Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="2e7e2-264">Update the Delete view</span></span>

<span data-ttu-id="2e7e2-265">*Views/departmanlar/delete. cshtml*içinde, scafkatkli kodunu, DepartmentID ve rowversion özellikleri için bir hata iletisi alanı ve gizli alanları ekleyen aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-265">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="2e7e2-266">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-266">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="2e7e2-267">Bu, aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-267">This makes the following changes:</span></span>

* <span data-ttu-id="2e7e2-268">`h2` Ve`h3` başlıkları arasına bir hata mesajı ekler.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="2e7e2-269">FullName FirstMidName değiştirir **yönetici** alan.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="2e7e2-270">RowVersion alanını kaldırır.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="2e7e2-271">`RowVersion` Özelliği için gizli bir alan ekler.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="2e7e2-272">Uygulamayı çalıştırın ve departmanlar dizini sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="2e7e2-273">Ingilizce departman için **Sil** köprüsünü sağ tıklayın ve **Yeni sekmede aç**' ı seçin ve ardından ilk sekmede İngilizce departman için **düzenleme** Köprüsü ' ne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="2e7e2-274">İlk pencerede, değerlerden birini değiştirin ve **Kaydet**' e tıklayın:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-274">In the first window, change one of the values, and click **Save**:</span></span>

![Silmeden önce değişiklikten sonra departman düzenleme sayfası](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="2e7e2-276">İkinci sekmede **Sil**' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="2e7e2-277">Eşzamanlılık hata iletisini görürsünüz ve departman değerleri şu anda veritabanında olan ile yenilenir.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Eşzamanlılık hatası olan departman silme onayı sayfası](concurrency/_static/delete-error.png)

<span data-ttu-id="2e7e2-279">Yeniden **Sil** ' e tıklarsanız, departmanın silindiğini gösteren dizin sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="2e7e2-280">Güncelleştirme ayrıntıları ve görünüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="2e7e2-280">Update Details and Create views</span></span>

<span data-ttu-id="2e7e2-281">İsteğe bağlı olarak ayrıntılarda bulunan kodu temizleyebilir ve görünümler oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="2e7e2-282">RowVersion sütununu silmek ve yöneticinin tam adını göstermek için *views/departmanlar/details. cshtml* içindeki kodu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="2e7e2-283">Açılan listeye bir SELECT seçeneği eklemek için *views/departmanlar/Create. cshtml* içindeki kodu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="2e7e2-284">Kodu alın</span><span class="sxs-lookup"><span data-stu-id="2e7e2-284">Get the code</span></span>

[<span data-ttu-id="2e7e2-285">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-285">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="2e7e2-286">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2e7e2-286">Additional resources</span></span>

 <span data-ttu-id="2e7e2-287">EF Core eşzamanlılık işleme hakkında daha fazla bilgi için bkz. [eşzamanlılık çakışmaları](/ef/core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="2e7e2-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="2e7e2-288">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="2e7e2-288">Next steps</span></span>

<span data-ttu-id="2e7e2-289">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="2e7e2-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2e7e2-290">Eşzamanlılık çakışmaları hakkında öğrenildi</span><span class="sxs-lookup"><span data-stu-id="2e7e2-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="2e7e2-291">İzleme özelliği eklendi</span><span class="sxs-lookup"><span data-stu-id="2e7e2-291">Added a tracking property</span></span>
> * <span data-ttu-id="2e7e2-292">Oluşturulan departmanlar denetleyicisi ve görünümleri</span><span class="sxs-lookup"><span data-stu-id="2e7e2-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="2e7e2-293">Güncelleştirilmiş dizin görünümü</span><span class="sxs-lookup"><span data-stu-id="2e7e2-293">Updated Index view</span></span>
> * <span data-ttu-id="2e7e2-294">Güncelleştirilmiş düzenleme yöntemleri</span><span class="sxs-lookup"><span data-stu-id="2e7e2-294">Updated Edit methods</span></span>
> * <span data-ttu-id="2e7e2-295">Güncelleştirilmiş düzenleme görünümü</span><span class="sxs-lookup"><span data-stu-id="2e7e2-295">Updated Edit view</span></span>
> * <span data-ttu-id="2e7e2-296">Test edilen eşzamanlılık çakışmaları</span><span class="sxs-lookup"><span data-stu-id="2e7e2-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="2e7e2-297">Silme sayfası güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="2e7e2-297">Updated the Delete page</span></span>
> * <span data-ttu-id="2e7e2-298">Güncelleştirilmiş Ayrıntılar ve görünüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="2e7e2-298">Updated Details and Create views</span></span>

<span data-ttu-id="2e7e2-299">Eğitmen ve öğrenci varlıkları için tablo başına devralma devralmayı nasıl uygulayacağınızı öğrenmek için sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="2e7e2-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="2e7e2-300">İleri Tablo-hiyerarşi devralmayı Uygula</span><span class="sxs-lookup"><span data-stu-id="2e7e2-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
