---
title: Bölüm 6, Razor ASP.NET Core EF Core olan sayfalar-Ilgili verileri oku
author: rick-anderson
description: RazorSayfaların 6. bölümü ve Entity Framework öğretici serisi.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 171607544bfe89fdd0a1ed9efb68f7a532f9aee1
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212652"
---
# <a name="part-6-razor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="422b6-103">Bölüm 6, Razor ASP.NET Core EF Core olan sayfalar-Ilgili verileri oku</span><span class="sxs-lookup"><span data-stu-id="422b6-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="422b6-104">, [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="422b6-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="422b6-105">Bu öğreticide, ilgili verilerin nasıl okunacağı ve görüntüleneceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="422b6-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="422b6-106">İlgili veriler, EF Core gezinti özelliklerine yüklediği veri.</span><span class="sxs-lookup"><span data-stu-id="422b6-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="422b6-107">Aşağıdaki çizimler, Bu öğreticinin tamamlanan sayfalarını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="422b6-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index30.png)

![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="422b6-110">Eager, açık ve yavaş yükleme</span><span class="sxs-lookup"><span data-stu-id="422b6-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="422b6-111">EF Core bir varlığın gezinti özelliklerine ilgili verileri yükleyebilmenin birkaç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="422b6-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="422b6-112">[Eager yükleniyor](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="422b6-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="422b6-113">Ekip yükleme, bir varlık türü için bir sorgu aynı zamanda ilgili varlıkları de yüklediğinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="422b6-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="422b6-114">Bir varlık okunmadığınızda ilgili veriler alınır.</span><span class="sxs-lookup"><span data-stu-id="422b6-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="422b6-115">Bu, genellikle gereken tüm verileri alan tek bir JOIN sorgusuna neden olur.</span><span class="sxs-lookup"><span data-stu-id="422b6-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="422b6-116">EF Core, bazı Eager yükleme türleri için birden çok sorgu yayımlayacak.</span><span class="sxs-lookup"><span data-stu-id="422b6-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="422b6-117">Birden çok sorgu verme, çok büyük paketlerini tek sorgusundan daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="422b6-118">Eager yüklemesi ve yöntemleriyle birlikte belirtilir `Include` `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="422b6-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="422b6-120">Bir koleksiyon gezintisi eklendiğinde Eager yüklemesi birden çok sorgu gönderir:</span><span class="sxs-lookup"><span data-stu-id="422b6-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="422b6-121">Ana sorgu için bir sorgu</span><span class="sxs-lookup"><span data-stu-id="422b6-121">One query for the main query</span></span> 
  * <span data-ttu-id="422b6-122">Yükleme ağacındaki her koleksiyon "Edge" için bir sorgu.</span><span class="sxs-lookup"><span data-stu-id="422b6-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="422b6-123">Sorguları ile ayır `Load` : veriler ayrı sorgularda alınabilir ve gezinti özellikleri EF Core "düzeltir".</span><span class="sxs-lookup"><span data-stu-id="422b6-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="422b6-124">"Düzeltmeler", EF Core gezinti özelliklerini otomatik olarak dolduran anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="422b6-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="422b6-125">İle ayrı sorgular `Load` , ek yükleme Eager yüklemeden daha benzer.</span><span class="sxs-lookup"><span data-stu-id="422b6-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="422b6-127">**Note:** EF Core, daha önce bağlam örneğine yüklenmiş olan diğer varlıklara gezinti özelliklerini otomatik olarak düzeltir.</span><span class="sxs-lookup"><span data-stu-id="422b6-127">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="422b6-128">Bir gezinti özelliği için veriler açıkça dahil *edilmese* bile, ilgili varlıkların bazıları veya tümü daha önce yüklenmişse Özellik yine de doldurulabilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="422b6-129">[Açık yükleme](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="422b6-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="422b6-130">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="422b6-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="422b6-131">Gerektiğinde ilgili verileri almak için kodun yazılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="422b6-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="422b6-132">Ayrı sorgularla açık yükleme, veritabanına birden çok sorgu gönderilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="422b6-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="422b6-133">Açık yükleme ile kod, yüklenecek gezinti özelliklerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="422b6-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="422b6-134">`Load`Açık yükleme yapmak için yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="422b6-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="422b6-135">Örnek:</span><span class="sxs-lookup"><span data-stu-id="422b6-135">For example:</span></span>

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="422b6-137">[Yavaş yükleme](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="422b6-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="422b6-138">[Sürüm 2,1 ' de EF Core geç yükleme eklendi](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="422b6-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="422b6-139">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="422b6-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="422b6-140">Gezinti özelliğine ilk kez erişildiğinde, bu gezinti özelliği için gereken veriler otomatik olarak alınır.</span><span class="sxs-lookup"><span data-stu-id="422b6-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="422b6-141">Bir gezinti özelliğine ilk kez erişildiğinde bir sorgu veritabanına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="422b6-142">Kurs sayfaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="422b6-142">Create Course pages</span></span>

<span data-ttu-id="422b6-143">`Course`Varlık, ilgili varlığı içeren bir gezinti özelliği içerir `Department` .</span><span class="sxs-lookup"><span data-stu-id="422b6-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Kurs. Departmanı](read-related-data/_static/dep-crs.png)

<span data-ttu-id="422b6-145">Bir kurs için atanan departmanın adını göstermek için:</span><span class="sxs-lookup"><span data-stu-id="422b6-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="422b6-146">İlgili `Department` varlığı `Course.Department` gezinti özelliğine yükleyin.</span><span class="sxs-lookup"><span data-stu-id="422b6-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="422b6-147">`Department`Varlığın özelliğinden adı alın `Name` .</span><span class="sxs-lookup"><span data-stu-id="422b6-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="422b6-148">Yapı iskelesi kurs sayfaları</span><span class="sxs-lookup"><span data-stu-id="422b6-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="422b6-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="422b6-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="422b6-150">Aşağıdaki özel durumlarla birlikte [Yapı Fkatlama öğrenci sayfalarındaki](xref:data/ef-rp/intro#scaffold-student-pages) yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="422b6-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="422b6-151">Bir *Sayfalar/kurslar* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="422b6-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="422b6-152">`Course`Model sınıfı için kullanın.</span><span class="sxs-lookup"><span data-stu-id="422b6-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="422b6-153">Yeni bir tane oluşturmak yerine var olan bağlam sınıfını kullanın.</span><span class="sxs-lookup"><span data-stu-id="422b6-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="422b6-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="422b6-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="422b6-155">Bir *Sayfalar/kurslar* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="422b6-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="422b6-156">Kurs sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="422b6-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="422b6-157">**Windows 'da:**</span><span class="sxs-lookup"><span data-stu-id="422b6-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="422b6-158">**Linux veya macOS 'ta:**</span><span class="sxs-lookup"><span data-stu-id="422b6-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="422b6-159">*Pages/kurslar/Index. cshtml. cs* dosyasını açın ve `OnGetAsync` metodunu inceleyin.</span><span class="sxs-lookup"><span data-stu-id="422b6-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="422b6-160">Yapı iskelesi altyapısı, gezinti özelliği için belirtilen Eager 'yi belirtti `Department` .</span><span class="sxs-lookup"><span data-stu-id="422b6-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="422b6-161">`Include`Yöntemi Eager yüklemeyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="422b6-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="422b6-162">Uygulamayı çalıştırın ve **Kurslar** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="422b6-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="422b6-163">Departman sütunu, `DepartmentID` yararlı olmayan öğesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="422b6-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="422b6-164">Departmanın adını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="422b6-164">Display the department name</span></span>

<span data-ttu-id="422b6-165">Pages/kurslar/Index. cshtml. cs dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="422b6-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="422b6-166">Önceki kod, `Course` özelliği olarak değiştirir `Courses` ve ekler `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="422b6-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="422b6-167">`AsNoTracking`Döndürülen varlıklar izlenmediğinden performansı geliştirir.</span><span class="sxs-lookup"><span data-stu-id="422b6-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="422b6-168">Varlıkların geçerli bağlamda güncelleştirilmediği için izlenmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="422b6-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="422b6-169">*Pages/kurslar/Index. cshtml* 'yi aşağıdaki kodla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="422b6-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="422b6-170">Yapı iskelesi kodunda aşağıdaki değişiklikler yapılmıştır:</span><span class="sxs-lookup"><span data-stu-id="422b6-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="422b6-171">`Course`Özellik adı olarak değiştirildi `Courses` .</span><span class="sxs-lookup"><span data-stu-id="422b6-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="422b6-172">Özellik değerini gösteren bir **sayı** sütunu eklendi `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="422b6-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="422b6-173">Birincil anahtarlar, genellikle son kullanıcılara anlamlı olduklarından, varsayılan olarak yapı iskelesi göstermemektedir.</span><span class="sxs-lookup"><span data-stu-id="422b6-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="422b6-174">Ancak, bu durumda birincil anahtar anlamlı olur.</span><span class="sxs-lookup"><span data-stu-id="422b6-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="422b6-175">Departman adını göstermek için **Departman** sütunu değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="422b6-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="422b6-176">Kod, `Name` `Department` gezinti özelliğine yüklenen varlığın özelliğini görüntüler `Department` :</span><span class="sxs-lookup"><span data-stu-id="422b6-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="422b6-177">Uygulamayı çalıştırın ve bölüm adlarıyla listeyi görmek için **Kurslar** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="422b6-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="422b6-179">Select ile ilgili verileri yükleme</span><span class="sxs-lookup"><span data-stu-id="422b6-179">Loading related data with Select</span></span>

<span data-ttu-id="422b6-180">`OnGetAsync`Yöntemi, yöntemiyle ilgili verileri yükler `Include` .</span><span class="sxs-lookup"><span data-stu-id="422b6-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="422b6-181">`Select`Yöntemi, yalnızca gerekli ilgili verileri yükleyen bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="422b6-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="422b6-182">Tek öğeler için, örneğin `Department.Name` BIR SQL Iç birleşimi kullanır.</span><span class="sxs-lookup"><span data-stu-id="422b6-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="422b6-183">Koleksiyonlar için başka bir veritabanı erişimi kullanır, ancak `Include` koleksiyonlar üzerindeki işleci bu şekilde yapar.</span><span class="sxs-lookup"><span data-stu-id="422b6-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="422b6-184">Aşağıdaki kod, yöntemiyle ilgili verileri yükler `Select` :</span><span class="sxs-lookup"><span data-stu-id="422b6-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="422b6-185">Yukarıdaki kod hiçbir varlık türü döndürmez, bu nedenle hiçbir izleme yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="422b6-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="422b6-186">EF izleme hakkında daha fazla bilgi için bkz. [izleme ve Izleme sorguları karşılaştırması](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="422b6-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="422b6-187">`CourseViewModel`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="422b6-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="422b6-188">Tüm örnek için bkz. [ındexselect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) ve [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="422b6-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="422b6-189">Eğitmen sayfaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="422b6-189">Create Instructor pages</span></span>

<span data-ttu-id="422b6-190">Bu bölüm, eğitmen sayfalarını derler ve ilgili Kurslar ve kayıtları eğitmenler dizin sayfasına ekler.</span><span class="sxs-lookup"><span data-stu-id="422b6-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="422b6-191">![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="422b6-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="422b6-192">Bu sayfa aşağıdaki yollarla ilgili verileri okur ve görüntüler:</span><span class="sxs-lookup"><span data-stu-id="422b6-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="422b6-193">Eğitmenler listesi, varlıktaki ilgili verileri `OfficeAssignment` (önceki görüntüde Office) görüntüler.</span><span class="sxs-lookup"><span data-stu-id="422b6-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="422b6-194">`Instructor`Ve `OfficeAssignment` varlıkları bire sıfır veya-bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="422b6-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="422b6-195">Varlıklar için Eager yüklemesi kullanılır `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="422b6-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="422b6-196">Eager yüklemesi, ilgili verilerin görüntülenmesi gerektiğinde genellikle daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="422b6-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="422b6-197">Bu durumda, Eğitmenler için Office atamaları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="422b6-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="422b6-198">Kullanıcı bir eğitmen seçtiğinde ilgili `Course` varlıklar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="422b6-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="422b6-199">`Instructor`Ve `Course` varlıkları çoktan çoğa bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="422b6-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="422b6-200">Eager yüklemesi, `Course` varlıklar ve ilgili varlıklar için kullanılır `Department` .</span><span class="sxs-lookup"><span data-stu-id="422b6-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="422b6-201">Bu durumda, yalnızca seçili eğitmen için kurslar gerektiğinden ayrı sorgular daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="422b6-202">Bu örnek, gezinti özelliklerinde olan varlıklarda gezinti özellikleri için Eager yükleme 'nin nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="422b6-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="422b6-203">Kullanıcı bir kurs seçtiğinde, varlıktaki ilgili veriler `Enrollments` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="422b6-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="422b6-204">Önceki görüntüde öğrenci adı ve sınıf görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="422b6-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="422b6-205">`Course`Ve `Enrollment` varlıkları bire çok ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="422b6-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="422b6-206">Görünüm modeli oluşturma</span><span class="sxs-lookup"><span data-stu-id="422b6-206">Create a view model</span></span>

<span data-ttu-id="422b6-207">Eğitmenler sayfasında, üç farklı tablodan alınan veriler gösterilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="422b6-208">Üç tabloyu temsil eden üç özellik içeren bir görünüm modeli gerekir.</span><span class="sxs-lookup"><span data-stu-id="422b6-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="422b6-209">Aşağıdaki kodla *SchoolViewModels/ıncpctorındexdata. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="422b6-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="422b6-210">Yapı iskelesi eğitmeni sayfaları</span><span class="sxs-lookup"><span data-stu-id="422b6-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="422b6-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="422b6-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="422b6-212">Öğrenci sayfalarını aşağıdaki özel durumlarla birlikte [Scaffold](xref:data/ef-rp/intro#scaffold-student-pages) içindeki yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="422b6-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="422b6-213">Bir *sayfa/eğitmenler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="422b6-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="422b6-214">`Instructor`Model sınıfı için kullanın.</span><span class="sxs-lookup"><span data-stu-id="422b6-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="422b6-215">Yeni bir tane oluşturmak yerine var olan bağlam sınıfını kullanın.</span><span class="sxs-lookup"><span data-stu-id="422b6-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="422b6-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="422b6-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="422b6-217">Bir *sayfa/eğitmenler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="422b6-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="422b6-218">Eğitmen sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="422b6-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="422b6-219">**Windows 'da:**</span><span class="sxs-lookup"><span data-stu-id="422b6-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="422b6-220">**Linux veya macOS 'ta:**</span><span class="sxs-lookup"><span data-stu-id="422b6-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="422b6-221">Yapı iskelesi sayfasının güncelleştirmeden önce nasıl göründüğünü görmek için, uygulamayı çalıştırın ve eğitmenler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="422b6-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="422b6-222">*Pages/eğitmenler/Index. cshtml. cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="422b6-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="422b6-223">`OnGetAsync`Yöntemi, seçilen EĞITMENIN kimliği için isteğe bağlı rota verilerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="422b6-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="422b6-224">*Pages/eğitmenler/Index. cshtml. cs* dosyasındaki sorguyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="422b6-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="422b6-225">Kod, aşağıdaki gezinti özellikleri için Eager yüklemeyi belirtir:</span><span class="sxs-lookup"><span data-stu-id="422b6-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="422b6-226">Ve `Include` `ThenInclude` için ve yöntemlerinin yinelendiğine dikkat `CourseAssignments` edin `Course` .</span><span class="sxs-lookup"><span data-stu-id="422b6-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="422b6-227">Bu yineleme, varlığın iki gezinti özelliği için Eager yüklemesi belirtmek için gereklidir `Course` .</span><span class="sxs-lookup"><span data-stu-id="422b6-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="422b6-228">Aşağıdaki kod, bir eğitmen seçildiğinde ( `id != null` ) yürütülür.</span><span class="sxs-lookup"><span data-stu-id="422b6-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="422b6-229">Seçilen eğitmen, görünüm modelindeki eğitmenler listesinden alınır.</span><span class="sxs-lookup"><span data-stu-id="422b6-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="422b6-230">Görünüm modelinin özelliği, `Courses` `Course` bu eğitmenin gezinti özelliğinden alınan varlıklarla birlikte yüklenir `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="422b6-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="422b6-231">`Where`Yöntemi bir koleksiyon döndürür.</span><span class="sxs-lookup"><span data-stu-id="422b6-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="422b6-232">Ancak bu durumda, filtre tek bir varlık seçer. bu nedenle, `Single` yöntemi koleksiyonu tek bir varlığa dönüştürmek için çağırılır `Instructor` .</span><span class="sxs-lookup"><span data-stu-id="422b6-232">But in this case, the filter will select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="422b6-233">`Instructor`Varlık, özelliğine erişim sağlar `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="422b6-233">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="422b6-234">`CourseAssignments`ilgili varlıklara erişim sağlar `Course` .</span><span class="sxs-lookup"><span data-stu-id="422b6-234">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="422b6-236">`Single`Koleksiyonda yalnızca bir öğe olduğunda yöntem bir koleksiyonda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="422b6-236">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="422b6-237">`Single`Koleksiyon boşsa veya birden fazla öğe varsa Yöntem bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="422b6-237">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="422b6-238">`SingleOrDefault`Koleksiyon boşsa varsayılan bir değer (Bu durumda null) döndüren alternatif bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="422b6-238">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="422b6-239">Aşağıdaki kod, `Enrollments` bir kurs seçildiğinde görünüm modelinin özelliğini doldurur:</span><span class="sxs-lookup"><span data-stu-id="422b6-239">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="422b6-240">Eğitmenler Dizin sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="422b6-240">Update the instructors Index page</span></span>

<span data-ttu-id="422b6-241">*Pages/eğitmenler/Index. cshtml* dosyasını aşağıdaki kodla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="422b6-241">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="422b6-242">Yukarıdaki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="422b6-242">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="422b6-243">`page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="422b6-243">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="422b6-244">`"{id:int?}"`bir yol şablonudur.</span><span class="sxs-lookup"><span data-stu-id="422b6-244">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="422b6-245">Yol şablonu, verileri yönlendirmek için URL 'deki tamsayı Sorgu dizelerini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="422b6-245">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="422b6-246">Örneğin, yalnızca yönergeyle bir eğitmenin **Select** bağlantısına tıklanması `@page` AŞAĞıDAKINE benzer bir URL oluşturur:</span><span class="sxs-lookup"><span data-stu-id="422b6-246">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="422b6-247">Sayfa yönergesi olduğunda `@page "{id:int?}"` URL şu şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="422b6-247">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="422b6-248">Yalnızca null değilse görüntülenen bir **Office** sütunu ekler `item.OfficeAssignment.Location` `item.OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="422b6-248">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="422b6-249">Bu bire sıfır veya-bir ilişki olduğundan ilgili bir OfficeAssignment varlığı bulunmayabilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-249">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="422b6-250">Her bir eğitmen tarafından taders kurslarını görüntüleyen bir **Kurslar** sütunu ekler.</span><span class="sxs-lookup"><span data-stu-id="422b6-250">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="422b6-251">Bu Razor sözdizimi hakkında daha fazla bilgi için bkz. [açık hat geçişi](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="422b6-251">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="422b6-252">`class="success"` `tr` Seçilen eğitmenin ve kursun öğesine dinamik olarak ekleyen kodu ekler.</span><span class="sxs-lookup"><span data-stu-id="422b6-252">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="422b6-253">Bu, bir önyükleme sınıfı kullanarak seçili satır için bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="422b6-253">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="422b6-254">**Select**etiketli yeni bir köprü ekler.</span><span class="sxs-lookup"><span data-stu-id="422b6-254">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="422b6-255">Bu bağlantı, seçilen eğitmenin KIMLIĞINI `Index` yönteme gönderir ve bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="422b6-255">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="422b6-256">Seçili eğitmen için bir kurs tablosu ekler.</span><span class="sxs-lookup"><span data-stu-id="422b6-256">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="422b6-257">Seçili kurs için bir öğrenci kayıtları tablosu ekler.</span><span class="sxs-lookup"><span data-stu-id="422b6-257">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="422b6-258">Uygulamayı çalıştırın ve **eğitmenler** sekmesini seçin. Sayfa, `Location` ilgili varlıktaki (Office) öğesini görüntüler `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="422b6-258">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="422b6-259">`OfficeAssignment`Null ise boş bir tablo hücresi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="422b6-259">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="422b6-260">Bir eğitmenin **Seç** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="422b6-260">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="422b6-261">Bu eğitmenin atandığı satır stili değişiklikleri ve kurslar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="422b6-261">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="422b6-262">Kayıtlı öğrenciler ve bunların onların listesini görmek için bir kurs seçin.</span><span class="sxs-lookup"><span data-stu-id="422b6-262">Select a course to see the list of enrolled students and their grades.</span></span>

![Eğitmenler Dizin sayfası eğitmeni ve kursu seçildi](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="422b6-264">Tek kullanımı</span><span class="sxs-lookup"><span data-stu-id="422b6-264">Using Single</span></span>

<span data-ttu-id="422b6-265">`Single`Yöntemi `Where` yöntemi ayrı çağırmak yerine koşulu geçirebilir `Where` :</span><span class="sxs-lookup"><span data-stu-id="422b6-265">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="422b6-266">`Single`Bir Where koşulunun kullanımı, kişisel tercihden bağımsız olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="422b6-266">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="422b6-267">Yöntemi kullanılarak herhangi bir avantaj sağlamaz `Where` .</span><span class="sxs-lookup"><span data-stu-id="422b6-267">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="422b6-268">Açık yükleme</span><span class="sxs-lookup"><span data-stu-id="422b6-268">Explicit loading</span></span>

<span data-ttu-id="422b6-269">Geçerli kod ve için Eager yüklemeyi belirtir `Enrollments` `Students` :</span><span class="sxs-lookup"><span data-stu-id="422b6-269">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="422b6-270">Kullanıcıların bir kursa kayıtları nadiren görmek istediğini varsayalım.</span><span class="sxs-lookup"><span data-stu-id="422b6-270">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="422b6-271">Bu durumda, bir iyileştirme yalnızca isteniyorsa kayıt verilerini yüklemek olacaktır.</span><span class="sxs-lookup"><span data-stu-id="422b6-271">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="422b6-272">Bu bölümde, ve ' `OnGetAsync` nin açık yüklemesini kullanacak şekilde güncelleştirilir `Enrollments` `Students` .</span><span class="sxs-lookup"><span data-stu-id="422b6-272">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="422b6-273">*Pages/eğitmenler/Index. cshtml. cs* dosyasını aşağıdaki kodla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="422b6-273">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="422b6-274">Yukarıdaki kod, kayıt ve öğrenci verileri için *Thenınclude* Yöntem çağrılarını bırakır.</span><span class="sxs-lookup"><span data-stu-id="422b6-274">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="422b6-275">Bir kurs seçilirse, açık yükleme kodu alır:</span><span class="sxs-lookup"><span data-stu-id="422b6-275">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="422b6-276">`Enrollment`Seçili kurs için varlıklar.</span><span class="sxs-lookup"><span data-stu-id="422b6-276">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="422b6-277">`Student`Her biri için varlıklar `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="422b6-277">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="422b6-278">Yukarıdaki kodun yorumdığına dikkat edin `.AsNoTracking()` .</span><span class="sxs-lookup"><span data-stu-id="422b6-278">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="422b6-279">Gezinti özellikleri yalnızca izlenen varlıklar için açık bir şekilde yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-279">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="422b6-280">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="422b6-280">Test the app.</span></span> <span data-ttu-id="422b6-281">Bir kullanıcının perspektifinden, uygulama önceki sürümle aynı şekilde davranır.</span><span class="sxs-lookup"><span data-stu-id="422b6-281">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="422b6-282">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="422b6-282">Next steps</span></span>

<span data-ttu-id="422b6-283">Sonraki öğreticide ilgili verileri güncelleştirme gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="422b6-283">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="422b6-284">[Önceki öğretici](xref:data/ef-rp/complex-data-model) 
> [Sonraki öğretici](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="422b6-284">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="422b6-285">Bu öğreticide ilgili veriler okundu ve görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="422b6-285">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="422b6-286">İlgili veriler, EF Core gezinti özelliklerine yüklediği veri.</span><span class="sxs-lookup"><span data-stu-id="422b6-286">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="422b6-287">Sorun yaşıyorsanız, bu [uygulamayı indiremez veya görüntüleyemezsiniz.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="422b6-287">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="422b6-288">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="422b6-288">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="422b6-289">Aşağıdaki çizimler, Bu öğreticinin tamamlanan sayfalarını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="422b6-289">The following illustrations show the completed pages for this tutorial:</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index.png)

![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="422b6-292">İlgili verilerin Eager, açık ve geç yüklemesi</span><span class="sxs-lookup"><span data-stu-id="422b6-292">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="422b6-293">EF Core bir varlığın gezinti özelliklerine ilgili verileri yükleyebilmenin birkaç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="422b6-293">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="422b6-294">[Eager yükleniyor](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="422b6-294">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="422b6-295">Ekip yükleme, bir varlık türü için bir sorgu aynı zamanda ilgili varlıkları de yüklediğinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="422b6-295">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="422b6-296">Varlık okunmadığınızda ilgili veriler alınır.</span><span class="sxs-lookup"><span data-stu-id="422b6-296">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="422b6-297">Bu, genellikle gereken tüm verileri alan tek bir JOIN sorgusuna neden olur.</span><span class="sxs-lookup"><span data-stu-id="422b6-297">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="422b6-298">EF Core, bazı Eager yükleme türleri için birden çok sorgu yayımlayacak.</span><span class="sxs-lookup"><span data-stu-id="422b6-298">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="422b6-299">Birden çok sorgu verilmesi, EF6 içindeki bazı sorgular için tek bir sorgunun bulunduğu durumda daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-299">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="422b6-300">Eager yüklemesi ve yöntemleriyle birlikte belirtilir `Include` `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="422b6-300">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="422b6-302">Bir koleksiyon gezintisi eklendiğinde Eager yüklemesi birden çok sorgu gönderir:</span><span class="sxs-lookup"><span data-stu-id="422b6-302">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="422b6-303">Ana sorgu için bir sorgu</span><span class="sxs-lookup"><span data-stu-id="422b6-303">One query for the main query</span></span> 
  * <span data-ttu-id="422b6-304">Yükleme ağacındaki her koleksiyon "Edge" için bir sorgu.</span><span class="sxs-lookup"><span data-stu-id="422b6-304">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="422b6-305">Sorguları ile ayır `Load` : veriler ayrı sorgularda alınabilir ve gezinti özellikleri EF Core "düzeltir".</span><span class="sxs-lookup"><span data-stu-id="422b6-305">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="422b6-306">"düzeltmeler", EF Core gezinti özelliklerini otomatik olarak dolduran anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="422b6-306">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="422b6-307">İle ayrı sorgular `Load` , ek yükleme Eager yüklemeden daha benzer.</span><span class="sxs-lookup"><span data-stu-id="422b6-307">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="422b6-309">Note: EF Core, daha önce bağlam örneğine yüklenmiş olan diğer varlıklar için gezinti özelliklerini otomatik olarak düzeltir.</span><span class="sxs-lookup"><span data-stu-id="422b6-309">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="422b6-310">Bir gezinti özelliği için veriler açıkça dahil *edilmese* bile, ilgili varlıkların bazıları veya tümü daha önce yüklenmişse Özellik yine de doldurulabilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-310">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="422b6-311">[Açık yükleme](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="422b6-311">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="422b6-312">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="422b6-312">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="422b6-313">Gerektiğinde ilgili verileri almak için kodun yazılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="422b6-313">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="422b6-314">Ayrı sorgularla açık yükleme, VERITABANıNA birden çok sorgu gönderilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="422b6-314">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="422b6-315">Açık yükleme ile kod, yüklenecek gezinti özelliklerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="422b6-315">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="422b6-316">`Load`Açık yükleme yapmak için yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="422b6-316">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="422b6-317">Örnek:</span><span class="sxs-lookup"><span data-stu-id="422b6-317">For example:</span></span>

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="422b6-319">[Yavaş yükleme](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="422b6-319">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="422b6-320">[Sürüm 2,1 ' de EF Core geç yükleme eklendi](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="422b6-320">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="422b6-321">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="422b6-321">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="422b6-322">Gezinti özelliğine ilk kez erişildiğinde, bu gezinti özelliği için gereken veriler otomatik olarak alınır.</span><span class="sxs-lookup"><span data-stu-id="422b6-322">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="422b6-323">Bir gezinti özelliğine ilk kez erişildiğinde bir sorgu VERITABANıNA gönderilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-323">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="422b6-324">`Select`İşleci yalnızca gerekli ilgili verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="422b6-324">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="422b6-325">Bölüm adını görüntüleyen bir kurs sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="422b6-325">Create a Course page that displays department name</span></span>

<span data-ttu-id="422b6-326">Kurs varlığı, varlığı içeren bir gezinti özelliği içerir `Department` .</span><span class="sxs-lookup"><span data-stu-id="422b6-326">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="422b6-327">`Department`Varlık, kursun atandığı departmanı içerir.</span><span class="sxs-lookup"><span data-stu-id="422b6-327">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="422b6-328">Bir kurs listesinde atanan departmanın adını göstermek için:</span><span class="sxs-lookup"><span data-stu-id="422b6-328">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="422b6-329">`Name`Varlıktaki özelliği alın `Department` .</span><span class="sxs-lookup"><span data-stu-id="422b6-329">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="422b6-330">`Department`Varlık, `Course.Department` Gezinti özelliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="422b6-330">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Kurs. Departmanı](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="422b6-332">Kurs modelini temklesi</span><span class="sxs-lookup"><span data-stu-id="422b6-332">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="422b6-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="422b6-333">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="422b6-334">[Öğrenci modelini Scafkatlama](xref:data/ef-rp/intro#scaffold-the-student-model) ve `Course` model sınıfı için kullanma bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="422b6-334">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="422b6-335">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="422b6-335">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="422b6-336">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="422b6-336">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="422b6-337">Yukarıdaki komut, modeli bir daha yasaklıyor `Course` .</span><span class="sxs-lookup"><span data-stu-id="422b6-337">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="422b6-338">Projeyi Visual Studio'da açın.</span><span class="sxs-lookup"><span data-stu-id="422b6-338">Open the project in Visual Studio.</span></span>

<span data-ttu-id="422b6-339">*Pages/kurslar/Index. cshtml. cs* dosyasını açın ve `OnGetAsync` metodunu inceleyin.</span><span class="sxs-lookup"><span data-stu-id="422b6-339">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="422b6-340">Yapı iskelesi altyapısı, gezinti özelliği için belirtilen Eager 'yi belirtti `Department` .</span><span class="sxs-lookup"><span data-stu-id="422b6-340">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="422b6-341">`Include`Yöntemi Eager yüklemeyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="422b6-341">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="422b6-342">Uygulamayı çalıştırın ve **Kurslar** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="422b6-342">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="422b6-343">Departman sütunu, `DepartmentID` yararlı olmayan öğesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="422b6-343">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="422b6-344">`OnGetAsync`Yöntemi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="422b6-344">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="422b6-345">Yukarıdaki kod ekler `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="422b6-345">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="422b6-346">`AsNoTracking`Döndürülen varlıklar izlenmediğinden performansı geliştirir.</span><span class="sxs-lookup"><span data-stu-id="422b6-346">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="422b6-347">Geçerli bağlamda güncelleştirilmemiş oldukları için varlıklar izlenmiyor.</span><span class="sxs-lookup"><span data-stu-id="422b6-347">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="422b6-348">*Pages/kurslar/Index. cshtml* 'yi aşağıdaki vurgulanmış işaretlerle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="422b6-348">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="422b6-349">Yapı iskelesi kodunda aşağıdaki değişiklikler yapılmıştır:</span><span class="sxs-lookup"><span data-stu-id="422b6-349">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="422b6-350">Başlık dizinden kurslar olarak değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="422b6-350">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="422b6-351">Özellik değerini gösteren bir **sayı** sütunu eklendi `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="422b6-351">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="422b6-352">Birincil anahtarlar, genellikle son kullanıcılara anlamlı olduklarından, varsayılan olarak yapı iskelesi göstermemektedir.</span><span class="sxs-lookup"><span data-stu-id="422b6-352">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="422b6-353">Ancak, bu durumda birincil anahtar anlamlı olur.</span><span class="sxs-lookup"><span data-stu-id="422b6-353">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="422b6-354">Departman adını göstermek için **Departman** sütunu değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="422b6-354">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="422b6-355">Kod, `Name` `Department` gezinti özelliğine yüklenen varlığın özelliğini görüntüler `Department` :</span><span class="sxs-lookup"><span data-stu-id="422b6-355">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="422b6-356">Uygulamayı çalıştırın ve bölüm adlarıyla listeyi görmek için **Kurslar** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="422b6-356">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="422b6-358">Select ile ilgili verileri yükleme</span><span class="sxs-lookup"><span data-stu-id="422b6-358">Loading related data with Select</span></span>

<span data-ttu-id="422b6-359">`OnGetAsync`Yöntemi, yöntemiyle ilgili verileri yükler `Include` :</span><span class="sxs-lookup"><span data-stu-id="422b6-359">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="422b6-360">`Select`İşleci yalnızca gerekli ilgili verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="422b6-360">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="422b6-361">Tek öğeler için, örneğin `Department.Name` BIR SQL Iç birleşimi kullanır.</span><span class="sxs-lookup"><span data-stu-id="422b6-361">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="422b6-362">Koleksiyonlar için başka bir veritabanı erişimi kullanır, ancak `Include` koleksiyonlar üzerindeki işleci bu şekilde yapar.</span><span class="sxs-lookup"><span data-stu-id="422b6-362">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="422b6-363">Aşağıdaki kod, yöntemiyle ilgili verileri yükler `Select` :</span><span class="sxs-lookup"><span data-stu-id="422b6-363">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="422b6-364">`CourseViewModel`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="422b6-364">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="422b6-365">Tüm örnek için bkz. [ındexselect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) ve [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="422b6-365">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="422b6-366">Kurslar ve kayıtları gösteren bir eğitmenler sayfası oluşturun</span><span class="sxs-lookup"><span data-stu-id="422b6-366">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="422b6-367">Bu bölümde, Eğitmenler sayfası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="422b6-367">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="422b6-368">![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="422b6-368">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="422b6-369">Bu sayfa aşağıdaki yollarla ilgili verileri okur ve görüntüler:</span><span class="sxs-lookup"><span data-stu-id="422b6-369">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="422b6-370">Eğitmenler listesi, varlıktaki ilgili verileri `OfficeAssignment` (önceki görüntüde Office) görüntüler.</span><span class="sxs-lookup"><span data-stu-id="422b6-370">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="422b6-371">`Instructor`Ve `OfficeAssignment` varlıkları bire sıfır veya-bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="422b6-371">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="422b6-372">Varlıklar için Eager yüklemesi kullanılır `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="422b6-372">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="422b6-373">Eager yüklemesi, ilgili verilerin görüntülenmesi gerektiğinde genellikle daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="422b6-373">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="422b6-374">Bu durumda, Eğitmenler için Office atamaları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="422b6-374">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="422b6-375">Kullanıcı bir eğitmen (önceki görüntüde Haruı) seçtiğinde ilgili `Course` varlıklar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="422b6-375">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="422b6-376">`Instructor`Ve `Course` varlıkları çoktan çoğa bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="422b6-376">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="422b6-377">Eager yüklemesi, `Course` varlıklar ve ilgili varlıklar için kullanılır `Department` .</span><span class="sxs-lookup"><span data-stu-id="422b6-377">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="422b6-378">Bu durumda, yalnızca seçili eğitmen için kurslar gerektiğinden ayrı sorgular daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-378">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="422b6-379">Bu örnek, gezinti özelliklerinde olan varlıklarda gezinti özellikleri için Eager yükleme 'nin nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="422b6-379">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="422b6-380">Kullanıcı bir kurs seçtiğinde (önceki görüntüde Chemistry), varlıktaki ilgili veriler `Enrollments` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="422b6-380">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="422b6-381">Önceki görüntüde öğrenci adı ve sınıf görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="422b6-381">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="422b6-382">`Course`Ve `Enrollment` varlıkları bire çok ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="422b6-382">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="422b6-383">Eğitmen dizini görünümü için bir görünüm modeli oluşturun</span><span class="sxs-lookup"><span data-stu-id="422b6-383">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="422b6-384">Eğitmenler sayfasında, üç farklı tablodan alınan veriler gösterilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-384">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="422b6-385">Üç tabloyu temsil eden üç varlığı içeren bir görünüm modeli oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="422b6-385">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="422b6-386">*SchoolViewModels* klasöründe, aşağıdaki kodla *InstructorIndexData.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="422b6-386">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="422b6-387">Eğitmen modelini scafkatlama</span><span class="sxs-lookup"><span data-stu-id="422b6-387">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="422b6-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="422b6-388">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="422b6-389">[Öğrenci modelini Scafkatlama](xref:data/ef-rp/intro#scaffold-the-student-model) ve `Instructor` model sınıfı için kullanma bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="422b6-389">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="422b6-390">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="422b6-390">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="422b6-391">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="422b6-391">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="422b6-392">Yukarıdaki komut, modeli bir daha yasaklıyor `Instructor` .</span><span class="sxs-lookup"><span data-stu-id="422b6-392">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="422b6-393">Uygulamayı çalıştırın ve eğitmenler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="422b6-393">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="422b6-394">*Pages/eğitmenler/Index. cshtml. cs* öğesini şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="422b6-394">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="422b6-395">`OnGetAsync`Yöntemi, seçilen EĞITMENIN kimliği için isteğe bağlı rota verilerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="422b6-395">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="422b6-396">*Pages/eğitmenler/Index. cshtml. cs* dosyasındaki sorguyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="422b6-396">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="422b6-397">Sorgunun iki içerme vardır:</span><span class="sxs-lookup"><span data-stu-id="422b6-397">The query has two includes:</span></span>

* <span data-ttu-id="422b6-398">`OfficeAssignment`: [Eğitmenler görünümünde](#IP)görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="422b6-398">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="422b6-399">`CourseAssignments`: Kurslar taöğretme.</span><span class="sxs-lookup"><span data-stu-id="422b6-399">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="422b6-400">Eğitmenler Dizin sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="422b6-400">Update the instructors Index page</span></span>

<span data-ttu-id="422b6-401">*Sayfaları/eğitmenler/Index. cshtml* 'yi şu biçimlendirmeyle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="422b6-401">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="422b6-402">Yukarıdaki biçimlendirme aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="422b6-402">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="422b6-403">`page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="422b6-403">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="422b6-404">`"{id:int?}"`bir yol şablonudur.</span><span class="sxs-lookup"><span data-stu-id="422b6-404">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="422b6-405">Yol şablonu, verileri yönlendirmek için URL 'deki tamsayı Sorgu dizelerini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="422b6-405">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="422b6-406">Örneğin, yalnızca yönergeyle bir eğitmenin **Select** bağlantısına tıklanması `@page` AŞAĞıDAKINE benzer bir URL oluşturur:</span><span class="sxs-lookup"><span data-stu-id="422b6-406">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="422b6-407">Sayfa yönergesi olduğunda `@page "{id:int?}"` , ÖNCEKI URL şu şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="422b6-407">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="422b6-408">Sayfa başlığı **eğitmenler**' dir.</span><span class="sxs-lookup"><span data-stu-id="422b6-408">Page title is **Instructors**.</span></span>
* <span data-ttu-id="422b6-409">Yalnızca null olmaması halinde görüntülenen bir **Office** sütunu eklendi `item.OfficeAssignment.Location` `item.OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="422b6-409">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="422b6-410">Bu bire sıfır veya-bir ilişki olduğundan ilgili bir OfficeAssignment varlığı bulunmayabilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-410">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="422b6-411">Her bir eğitmen tarafından taders kurslarını görüntüleyen bir **Kurslar** sütunu eklendi.</span><span class="sxs-lookup"><span data-stu-id="422b6-411">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="422b6-412">Bu Razor sözdizimi hakkında daha fazla bilgi için bkz. [açık hat geçişi](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="422b6-412">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="422b6-413">`class="success"`Seçilen eğitmenin öğesine dinamik olarak ekleyen kod eklendi `tr` .</span><span class="sxs-lookup"><span data-stu-id="422b6-413">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="422b6-414">Bu, bir önyükleme sınıfı kullanarak seçili satır için bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="422b6-414">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="422b6-415">**Select**etiketli yeni bir köprü eklendi.</span><span class="sxs-lookup"><span data-stu-id="422b6-415">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="422b6-416">Bu bağlantı, seçilen eğitmenin KIMLIĞINI `Index` yönteme gönderir ve bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="422b6-416">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="422b6-417">Uygulamayı çalıştırın ve **eğitmenler** sekmesini seçin. Sayfa, `Location` ilgili varlıktaki (Office) öğesini görüntüler `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="422b6-417">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="422b6-418">Eğer OfficeAssignment ' null ise boş bir tablo hücresi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="422b6-418">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="422b6-419">**Seç** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="422b6-419">Click on the **Select** link.</span></span> <span data-ttu-id="422b6-420">Satır stili değişir.</span><span class="sxs-lookup"><span data-stu-id="422b6-420">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="422b6-421">Seçili eğitmen 'e göre kurslar ekleyin</span><span class="sxs-lookup"><span data-stu-id="422b6-421">Add courses taught by selected instructor</span></span>

<span data-ttu-id="422b6-422">`OnGetAsync` *Pages/eğitmenler/Index. cshtml. cs* dosyasındaki yöntemi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="422b6-422">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="422b6-423">Ekleyemiyorum`public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="422b6-423">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="422b6-424">Güncelleştirilmiş sorguyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="422b6-424">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="422b6-425">Önceki sorgu `Department` varlıkları ekler.</span><span class="sxs-lookup"><span data-stu-id="422b6-425">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="422b6-426">Aşağıdaki kod, bir eğitmen seçildiğinde ( `id != null` ) yürütülür.</span><span class="sxs-lookup"><span data-stu-id="422b6-426">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="422b6-427">Seçilen eğitmen, görünüm modelindeki eğitmenler listesinden alınır.</span><span class="sxs-lookup"><span data-stu-id="422b6-427">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="422b6-428">Görünüm modelinin özelliği, `Courses` `Course` bu eğitmenin gezinti özelliğinden alınan varlıklarla birlikte yüklenir `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="422b6-428">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="422b6-429">`Where`Yöntemi bir koleksiyon döndürür.</span><span class="sxs-lookup"><span data-stu-id="422b6-429">The `Where` method returns a collection.</span></span> <span data-ttu-id="422b6-430">Önceki `Where` yöntemde yalnızca tek bir `Instructor` varlık döndürülür.</span><span class="sxs-lookup"><span data-stu-id="422b6-430">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="422b6-431">`Single`Yöntemi, koleksiyonu tek bir `Instructor` varlığa dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="422b6-431">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="422b6-432">`Instructor`Varlık, özelliğine erişim sağlar `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="422b6-432">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="422b6-433">`CourseAssignments`ilgili varlıklara erişim sağlar `Course` .</span><span class="sxs-lookup"><span data-stu-id="422b6-433">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="422b6-435">`Single`Koleksiyonda yalnızca bir öğe olduğunda yöntem bir koleksiyonda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="422b6-435">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="422b6-436">`Single`Koleksiyon boşsa veya birden fazla öğe varsa Yöntem bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="422b6-436">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="422b6-437">`SingleOrDefault`Koleksiyon boşsa varsayılan bir değer (Bu durumda null) döndüren alternatif bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="422b6-437">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="422b6-438">`SingleOrDefault`Boş bir koleksiyonda kullanma:</span><span class="sxs-lookup"><span data-stu-id="422b6-438">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="422b6-439">Bir özel durum sonucu oluşur ( `Courses` null başvuru üzerinde bir özellik bulmaya çalışırken).</span><span class="sxs-lookup"><span data-stu-id="422b6-439">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="422b6-440">Özel durum iletisi sorunun nedenini daha az gösterir.</span><span class="sxs-lookup"><span data-stu-id="422b6-440">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="422b6-441">Aşağıdaki kod, `Enrollments` bir kurs seçildiğinde görünüm modelinin özelliğini doldurur:</span><span class="sxs-lookup"><span data-stu-id="422b6-441">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="422b6-442">*Pages/eğitmenler/Index. cshtml* sayfasının sonuna aşağıdaki biçimlendirmeyi ekleyin Razor :</span><span class="sxs-lookup"><span data-stu-id="422b6-442">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="422b6-443">Yukarıdaki biçimlendirme, bir eğitmen seçildiğinde bir eğitmenin ilgili kursların bir listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="422b6-443">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="422b6-444">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="422b6-444">Test the app.</span></span> <span data-ttu-id="422b6-445">Eğitmenler sayfasında bir **seçme** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="422b6-445">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="422b6-446">Öğrenci verilerini göster</span><span class="sxs-lookup"><span data-stu-id="422b6-446">Show student data</span></span>

<span data-ttu-id="422b6-447">Bu bölümde, uygulama seçili bir kurs için öğrenci verilerini gösterecek şekilde güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-447">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="422b6-448">`OnGetAsync` *Pages/eğitmenler/Index. cshtml. cs* içindeki yöntemdeki sorguyu aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="422b6-448">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="422b6-449">Güncelleştirme *sayfaları/eğitmenler/Index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="422b6-449">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="422b6-450">Aşağıdaki biçimlendirmeyi dosyanın sonuna ekleyin:</span><span class="sxs-lookup"><span data-stu-id="422b6-450">Add the following markup to the end of the file:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="422b6-451">Yukarıdaki biçimlendirme, seçili kursa kayıtlı öğrencilerin bir listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="422b6-451">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="422b6-452">Sayfayı yenileyin ve bir eğitmen seçin.</span><span class="sxs-lookup"><span data-stu-id="422b6-452">Refresh the page and select an instructor.</span></span> <span data-ttu-id="422b6-453">Kayıtlı öğrenciler ve bunların onların listesini görmek için bir kurs seçin.</span><span class="sxs-lookup"><span data-stu-id="422b6-453">Select a course to see the list of enrolled students and their grades.</span></span>

![Eğitmenler Dizin sayfası eğitmeni ve kursu seçildi](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="422b6-455">Tek kullanımı</span><span class="sxs-lookup"><span data-stu-id="422b6-455">Using Single</span></span>

<span data-ttu-id="422b6-456">`Single`Yöntemi `Where` yöntemi ayrı çağırmak yerine koşulu geçirebilir `Where` :</span><span class="sxs-lookup"><span data-stu-id="422b6-456">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="422b6-457">Yukarıdaki `Single` yaklaşım, kullanarak herhangi bir avantaj sağlamaz `Where` .</span><span class="sxs-lookup"><span data-stu-id="422b6-457">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="422b6-458">Bazı geliştiriciler `Single` yaklaşım stilini tercih eder.</span><span class="sxs-lookup"><span data-stu-id="422b6-458">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="422b6-459">Açık yükleme</span><span class="sxs-lookup"><span data-stu-id="422b6-459">Explicit loading</span></span>

<span data-ttu-id="422b6-460">Geçerli kod ve için Eager yüklemeyi belirtir `Enrollments` `Students` :</span><span class="sxs-lookup"><span data-stu-id="422b6-460">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="422b6-461">Kullanıcıların bir kursa kayıtları nadiren görmek istediğini varsayalım.</span><span class="sxs-lookup"><span data-stu-id="422b6-461">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="422b6-462">Bu durumda, bir iyileştirme yalnızca isteniyorsa kayıt verilerini yüklemek olacaktır.</span><span class="sxs-lookup"><span data-stu-id="422b6-462">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="422b6-463">Bu bölümde, ve ' `OnGetAsync` nin açık yüklemesini kullanacak şekilde güncelleştirilir `Enrollments` `Students` .</span><span class="sxs-lookup"><span data-stu-id="422b6-463">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="422b6-464">`OnGetAsync`Aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="422b6-464">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="422b6-465">Yukarıdaki kod, kayıt ve öğrenci verileri için *Thenınclude* Yöntem çağrılarını bırakır.</span><span class="sxs-lookup"><span data-stu-id="422b6-465">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="422b6-466">Bir kurs seçilirse vurgulanan kod şunu alır:</span><span class="sxs-lookup"><span data-stu-id="422b6-466">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="422b6-467">`Enrollment`Seçili kurs için varlıklar.</span><span class="sxs-lookup"><span data-stu-id="422b6-467">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="422b6-468">`Student`Her biri için varlıklar `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="422b6-468">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="422b6-469">Yukarıdaki kod açıklamalarının olduğunu fark edin `.AsNoTracking()` .</span><span class="sxs-lookup"><span data-stu-id="422b6-469">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="422b6-470">Gezinti özellikleri yalnızca izlenen varlıklar için açık bir şekilde yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="422b6-470">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="422b6-471">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="422b6-471">Test the app.</span></span> <span data-ttu-id="422b6-472">Bir kullanıcının perspektifinden, uygulama önceki sürümle aynı şekilde davranır.</span><span class="sxs-lookup"><span data-stu-id="422b6-472">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="422b6-473">Sonraki öğreticide ilgili verileri güncelleştirme gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="422b6-473">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="422b6-474">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="422b6-474">Additional resources</span></span>

* [<span data-ttu-id="422b6-475">Bu öğreticinin YouTube sürümü (part1)</span><span class="sxs-lookup"><span data-stu-id="422b6-475">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="422b6-476">Bu öğreticinin YouTube sürümü (part2)</span><span class="sxs-lookup"><span data-stu-id="422b6-476">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="422b6-477">[Önceki](xref:data/ef-rp/complex-data-model) 
> [Sonraki](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="422b6-477">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
