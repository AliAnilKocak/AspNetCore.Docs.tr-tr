---
title: ASP.NET core'da - CRUD - 2 8 EF çekirdekli Razor sayfaları
author: rick-anderson
description: Oluşturma, okuma, güncelleştirme ve EF Core ile silme işlemini gösterir
ms.author: riande
ms.date: 6/31/2017
uid: data/ef-rp/crud
ms.openlocfilehash: 4af16bdf3928609214c1255cdd411312c8b7d3f3
ms.sourcegitcommit: f5d403004f3550e8c46585fdbb16c49e75f495f3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/20/2018
ms.locfileid: "49477442"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---crud---2-of-8"></a><span data-ttu-id="c7104-103">ASP.NET core'da - CRUD - 2 8 EF çekirdekli Razor sayfaları</span><span class="sxs-lookup"><span data-stu-id="c7104-103">Razor Pages with EF Core in ASP.NET Core - CRUD - 2 of 8</span></span>

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c7104-104">Tarafından [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c7104-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="c7104-105">Bu öğreticide, iskele kurulmuş CRUD (oluşturma, okuma, güncelleştirme ve silme) kod inceleme ve özelleştirilmiş.</span><span class="sxs-lookup"><span data-stu-id="c7104-105">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

<span data-ttu-id="c7104-106">Karmaşıklığı en aza indirmek ve EF Core üzerine odaklanan bu öğreticileri tutmak için EF Core kod sayfası modellerinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c7104-106">To minimize complexity and keep these tutorials focused on EF Core, EF Core code is used in the page models.</span></span> <span data-ttu-id="c7104-107">Bazı geliştiriciler, kullanıcı Arabirimi (Razor sayfaları) ve veri erişim katmanı arasında bir Soyutlama Katmanı oluşturmak için bir hizmet katmanı veya depo deseni kullanın.</span><span class="sxs-lookup"><span data-stu-id="c7104-107">Some developers use a service layer or repository pattern in to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span>

<span data-ttu-id="c7104-108">Bu öğreticide, oluştur, Düzenle, Sil ve ayrıntıları Razor sayfaları *Öğrenciler* klasör incelenir.</span><span class="sxs-lookup"><span data-stu-id="c7104-108">In this tutorial, the Create, Edit, Delete, and Details Razor Pages in the *Students* folder are examined.</span></span>

<span data-ttu-id="c7104-109">İskele kurulan kodu oluştur, Düzenle ve Sil sayfaları için şu biçimi kullanır:</span><span class="sxs-lookup"><span data-stu-id="c7104-109">The scaffolded code uses the following pattern for Create, Edit, and Delete pages:</span></span>

* <span data-ttu-id="c7104-110">Alma ve HTTP GET yöntemi ile istenen veri görüntüleme `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="c7104-110">Get and display the requested data with the HTTP GET method `OnGetAsync`.</span></span>
* <span data-ttu-id="c7104-111">HTTP POST yöntemi verilerde yapılan değişiklikleri kaydetmek `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="c7104-111">Save changes to the data with the HTTP POST method `OnPostAsync`.</span></span>

<span data-ttu-id="c7104-112">Dizin ve ayrıntıları sayfaları almak ve istenen verileri HTTP GET yöntemi ile görüntüleme `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="c7104-112">The Index and Details pages get and display the requested data with the HTTP GET method `OnGetAsync`</span></span>

## <a name="singleordefaultasync-vs-firstordefaultasync"></a><span data-ttu-id="c7104-113">SingleOrDefaultAsync vs. FirstOrDefaultAsync</span><span class="sxs-lookup"><span data-stu-id="c7104-113">SingleOrDefaultAsync vs. FirstOrDefaultAsync</span></span>

<span data-ttu-id="c7104-114">Oluşturulan kod [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), genellikle tercih üzerinden olduğu [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span><span class="sxs-lookup"><span data-stu-id="c7104-114">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), which is generally preferred over [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

 <span data-ttu-id="c7104-115">`FirstOrDefaultAsync` Daha fazla verimlidir `SingleOrDefaultAsync` bir varlık alma sırasında:</span><span class="sxs-lookup"><span data-stu-id="c7104-115">`FirstOrDefaultAsync` is more efficient than `SingleOrDefaultAsync` at fetching one entity:</span></span>

* <span data-ttu-id="c7104-116">Kodu doğrulayın gerek duymadığı sürece sorgusundan döndürülen birden fazla varlık yok.</span><span class="sxs-lookup"><span data-stu-id="c7104-116">Unless the code needs to verify that there's not more than one entity returned from the query.</span></span>
* <span data-ttu-id="c7104-117">`SingleOrDefaultAsync` Daha fazla veri getirir ve gereksiz çalışır.</span><span class="sxs-lookup"><span data-stu-id="c7104-117">`SingleOrDefaultAsync` fetches more data and does unnecessary work.</span></span>
* <span data-ttu-id="c7104-118">`SingleOrDefaultAsync` Sorgunuzun filtre bölümü uygun birden fazla varlık ise bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c7104-118">`SingleOrDefaultAsync` throws an exception if there's more than one entity that fits the filter part.</span></span>
* <span data-ttu-id="c7104-119">`FirstOrDefaultAsync` Sorgunuzun filtre bölümü uygun birden fazla varlık ise oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="c7104-119">`FirstOrDefaultAsync` doesn't throw if there's more than one entity that fits the filter part.</span></span>

<a name="FindAsync"></a>

### <a name="findasync"></a><span data-ttu-id="c7104-120">FindAsync</span><span class="sxs-lookup"><span data-stu-id="c7104-120">FindAsync</span></span>

<span data-ttu-id="c7104-121">İskele kurulan kodu çoğunu içinde [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) yerine kullanılan `FirstOrDefaultAsync`.</span><span class="sxs-lookup"><span data-stu-id="c7104-121">In much of the scaffolded code, [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) can be used in place of `FirstOrDefaultAsync`.</span></span>

<span data-ttu-id="c7104-122">`FindAsync`:</span><span class="sxs-lookup"><span data-stu-id="c7104-122">`FindAsync`:</span></span>

* <span data-ttu-id="c7104-123">Bir varlığın birincil anahtarla (PK) bulur.</span><span class="sxs-lookup"><span data-stu-id="c7104-123">Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="c7104-124">Bir varlıkla PK bağlam tarafından izleniyorsa, Veritabanına gerek olmadan isteği döndürülür.</span><span class="sxs-lookup"><span data-stu-id="c7104-124">If an entity with the PK is being tracked by the context, it's returned without a request to the DB.</span></span>
* <span data-ttu-id="c7104-125">Basit ve kısa maliyetlidir.</span><span class="sxs-lookup"><span data-stu-id="c7104-125">Is simple and concise.</span></span>
* <span data-ttu-id="c7104-126">Tek bir varlığı aramak için optimize edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="c7104-126">Is optimized to look up a single entity.</span></span>
* <span data-ttu-id="c7104-127">Performans açısından faydalı bazı durumlarda olabilir, ancak normal web apps için nadiren gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="c7104-127">Can have perf benefits in some situations, but that rarely happens for typical web apps.</span></span>
* <span data-ttu-id="c7104-128">Örtülü olarak kullanan [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) yerine [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span><span class="sxs-lookup"><span data-stu-id="c7104-128">Implicitly uses [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) instead of [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

<span data-ttu-id="c7104-129">Ancak isterseniz `Include` diğer varlıklar, ardından `FindAsync` artık uygun değil.</span><span class="sxs-lookup"><span data-stu-id="c7104-129">But if you want to `Include` other entities, then `FindAsync` is no longer appropriate.</span></span> <span data-ttu-id="c7104-130">Bu iptal gerekebilir anlamına gelir `FindAsync` ve bir sorgu için uygulama ilerledikçe taşıyın.</span><span class="sxs-lookup"><span data-stu-id="c7104-130">This means that you may need to abandon `FindAsync` and move to a query as your app progresses.</span></span>

## <a name="customize-the-details-page"></a><span data-ttu-id="c7104-131">Ayrıntılar sayfasını özelleştirme</span><span class="sxs-lookup"><span data-stu-id="c7104-131">Customize the Details page</span></span>

<span data-ttu-id="c7104-132">Gözat `Pages/Students` sayfası.</span><span class="sxs-lookup"><span data-stu-id="c7104-132">Browse to `Pages/Students` page.</span></span> <span data-ttu-id="c7104-133">**Düzenle**, **ayrıntıları**, ve **Sil** tarafından oluşturulan bağlantıları [yer işareti etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) içinde *sayfaları/Öğrenciler / Index.cshtml* dosya.</span><span class="sxs-lookup"><span data-stu-id="c7104-133">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Students/Index.cshtml* file.</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

<span data-ttu-id="c7104-134">Uygulamayı çalıştırmak ve seçmek bir **ayrıntıları** bağlantı.</span><span class="sxs-lookup"><span data-stu-id="c7104-134">Run the app and select a **Details** link.</span></span> <span data-ttu-id="c7104-135">URL biçimindedir `http://localhost:5000/Students/Details?id=2`.</span><span class="sxs-lookup"><span data-stu-id="c7104-135">The URL is of the form `http://localhost:5000/Students/Details?id=2`.</span></span> <span data-ttu-id="c7104-136">Öğrenci Kimliği, sorgu dizesi kullanılarak geçirilir (`?id=2`).</span><span class="sxs-lookup"><span data-stu-id="c7104-136">The Student ID is passed using a query string (`?id=2`).</span></span>

<span data-ttu-id="c7104-137">Düzenle, Ayrıntılar ve Sil Razor sayfaları kullanmaya güncelleştirme `"{id:int}"` rota şablonu.</span><span class="sxs-lookup"><span data-stu-id="c7104-137">Update the Edit, Details, and Delete Razor Pages to use the `"{id:int}"` route template.</span></span> <span data-ttu-id="c7104-138">Her biri bu sayfaları için sayfa yönergesi değiştirme `@page` için `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="c7104-138">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span>

<span data-ttu-id="c7104-139">"{Kimliği: int}" rota şablonuyla yapan sayfasına bir istek **değil** tamsayı rota değeri döndürür bir HTTP 404 (bulunamadı) hatası içerir.</span><span class="sxs-lookup"><span data-stu-id="c7104-139">A request to the page with the "{id:int}" route template that does **not** include a integer route value returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="c7104-140">Örneğin, `http://localhost:5000/Students/Details` 404 hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="c7104-140">For example, `http://localhost:5000/Students/Details` returns a 404 error.</span></span> <span data-ttu-id="c7104-141">Kimliği isteğe bağlı yapmak için URL'ye `?` rota kısıtlaması için:</span><span class="sxs-lookup"><span data-stu-id="c7104-141">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="c7104-142">Uygulamayı çalıştırmak için bir ayrıntıları bağlantıya tıkladığınızda URL rota verilerini kimliği geçirerek doğrulayın (`http://localhost:5000/Students/Details/2`).</span><span class="sxs-lookup"><span data-stu-id="c7104-142">Run the app, click on a Details link, and verify the URL is passing the ID as route data (`http://localhost:5000/Students/Details/2`).</span></span>

<span data-ttu-id="c7104-143">Genel olarak değiştirme `@page` için `@page "{id:int}"`, bunu sonu bağlantılar giriş yapmak ve sayfaları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c7104-143">Don't globally change `@page` to `@page "{id:int}"`, doing so breaks the links to the Home and Create pages.</span></span>

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a><span data-ttu-id="c7104-144">İlgili veri ekleme</span><span class="sxs-lookup"><span data-stu-id="c7104-144">Add related data</span></span>

<span data-ttu-id="c7104-145">Öğrenciler dizin sayfası için iskele kurulmuş kod içermeyen `Enrollments` özelliği.</span><span class="sxs-lookup"><span data-stu-id="c7104-145">The scaffolded code for the Students Index page doesn't include the `Enrollments` property.</span></span> <span data-ttu-id="c7104-146">Bu bölümde, içeriğini `Enrollments` koleksiyon Ayrıntıları sayfasında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c7104-146">In this section, the contents of the `Enrollments` collection is displayed in the Details page.</span></span>

<span data-ttu-id="c7104-147">`OnGetAsync` Yöntemi *Pages/Students/Details.cshtml.cs* kullanan `FirstOrDefaultAsync` yönteminin tek bir almak için `Student` varlık.</span><span class="sxs-lookup"><span data-stu-id="c7104-147">The `OnGetAsync` method of *Pages/Students/Details.cshtml.cs* uses the `FirstOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="c7104-148">Aşağıdaki vurgulanmış kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c7104-148">Add the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="c7104-149">[INCLUDE](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) ve [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) yöntemlerinin neden yüklemek bağlam `Student.Enrollments` gezinme özelliği ve her kaydı `Enrollment.Course` gezinme özelliği.</span><span class="sxs-lookup"><span data-stu-id="c7104-149">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="c7104-150">Bu yöntemler, ilgili okuma veri öğreticide ayrıntılı olarak incelenir.</span><span class="sxs-lookup"><span data-stu-id="c7104-150">These methods are examined in detail in the reading-related data tutorial.</span></span>

<span data-ttu-id="c7104-151">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) yöntemi varlıkları güncelleştirilmez geçerli bağlamda döndürüldüğünde senaryolarda performansı artırır.</span><span class="sxs-lookup"><span data-stu-id="c7104-151">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios when the entities returned are not updated in the current context.</span></span> <span data-ttu-id="c7104-152">`AsNoTracking` Bu öğreticinin sonraki bölümlerinde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="c7104-152">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-related-enrollments-on-the-details-page"></a><span data-ttu-id="c7104-153">Ayrıntılar sayfasında ilgili kayıtları görüntüle</span><span class="sxs-lookup"><span data-stu-id="c7104-153">Display related enrollments on the Details page</span></span>

<span data-ttu-id="c7104-154">Açık *Pages/Students/Details.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c7104-154">Open *Pages/Students/Details.cshtml*.</span></span> <span data-ttu-id="c7104-155">Kayıtları bir listesini görüntülemek için aşağıdaki vurgulanmış kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c7104-155">Add the following highlighted code to display a list of enrollments:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="c7104-156">Kod girintileme kodu yapıştırılır sonra yanlışsa, düzeltmek için CTRL-K-D tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="c7104-156">If code indentation is wrong after the code is pasted, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="c7104-157">Yukarıdaki kod, varlıklarda döngü `Enrollments` gezinme özelliği.</span><span class="sxs-lookup"><span data-stu-id="c7104-157">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="c7104-158">Her kayıt için bu kurs başlığı ve sınıf görüntüler.</span><span class="sxs-lookup"><span data-stu-id="c7104-158">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="c7104-159">Kurs başlığı depolanan kurs varlık alınır `Course` kayıtları varlık gezinme özelliği.</span><span class="sxs-lookup"><span data-stu-id="c7104-159">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="c7104-160">Uygulamayı çalıştırın, seçin **Öğrenciler** sekmesine ve tıklayın **ayrıntıları** bir öğrenci bağlantısı.</span><span class="sxs-lookup"><span data-stu-id="c7104-160">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="c7104-161">Kursları ve notlarınızı için seçilen Öğrenci listesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c7104-161">The list of courses and grades for the selected student is displayed.</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="c7104-162">Güncelleştirme Oluştur sayfası</span><span class="sxs-lookup"><span data-stu-id="c7104-162">Update the Create page</span></span>

<span data-ttu-id="c7104-163">Güncelleştirme `OnPostAsync` yönteminde *Pages/Students/Create.cshtml.cs* aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="c7104-163">Update the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="c7104-164">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="c7104-164">TryUpdateModelAsync</span></span>

<span data-ttu-id="c7104-165">İnceleme [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) kod:</span><span class="sxs-lookup"><span data-stu-id="c7104-165">Examine the [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

<span data-ttu-id="c7104-166">Önceki kodda, `TryUpdateModelAsync<Student>` güncelleştirmeyi dener `emptyStudent` gönderilen form değerleri kullanarak nesne [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) özelliğinde [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span><span class="sxs-lookup"><span data-stu-id="c7104-166">In the preceding code, `TryUpdateModelAsync<Student>` tries to update the `emptyStudent` object using the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span> <span data-ttu-id="c7104-167">`TryUpdateModelAsync` Yalnızca listelenen özelliklerini güncelleştirir (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span><span class="sxs-lookup"><span data-stu-id="c7104-167">`TryUpdateModelAsync` only updates the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>

<span data-ttu-id="c7104-168">Önceki örnekte:</span><span class="sxs-lookup"><span data-stu-id="c7104-168">In the preceding sample:</span></span>

* <span data-ttu-id="c7104-169">İkinci bağımsız değişkeni (`"student", // Prefix`) öneki değerleri aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c7104-169">The second argument (`"student", // Prefix`) is the prefix uses to look up values.</span></span> <span data-ttu-id="c7104-170">Büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="c7104-170">It's not case sensitive.</span></span>
* <span data-ttu-id="c7104-171">Gönderilen form değerleri türleri dönüştürülür `Student` modelini [model bağlama](xref:mvc/models/model-binding#how-model-binding-works).</span><span class="sxs-lookup"><span data-stu-id="c7104-171">The posted form values are converted to the types in the `Student` model using [model binding](xref:mvc/models/model-binding#how-model-binding-works).</span></span>

<a id="overpost"></a>

### <a name="overposting"></a><span data-ttu-id="c7104-172">Overposting</span><span class="sxs-lookup"><span data-stu-id="c7104-172">Overposting</span></span>

<span data-ttu-id="c7104-173">Kullanarak `TryUpdateModel` overposting önlediği için gönderilen değerler alanları güncelleştirmek için bir güvenlik en iyi uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="c7104-173">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="c7104-174">Örneğin, Öğrenci varlık içerdiğini varsayın bir `Secret` eden bu web sayfası güncelleştirin veya olmamalıdır özelliği:</span><span class="sxs-lookup"><span data-stu-id="c7104-174">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="c7104-175">Uygulama almasa bile bir `Secret` Razor sayfası, bir bilgisayar korsanının oluştur/güncelleştir alanı ayarlayabilirsiniz `Secret` overposting tarafından değeri.</span><span class="sxs-lookup"><span data-stu-id="c7104-175">Even if the app doesn't have a `Secret` field on the create/update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="c7104-176">Bir bilgisayar korsanının Fiddler gibi bir araç kullanın veya göndermek için bazı JavaScript Yazma bir `Secret` form değeri.</span><span class="sxs-lookup"><span data-stu-id="c7104-176">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="c7104-177">Özgün koda bir öğrenci örneği oluşturduğunda, model Bağlayıcısı kullandığını alanları kısıtlamaz.</span><span class="sxs-lookup"><span data-stu-id="c7104-177">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="c7104-178">İnovasyonunuz ne olursa olsun değer için belirtilen korsanın `Secret` form alanı DB'de güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c7104-178">Whatever value the hacker specified for the `Secret` form field is updated in the DB.</span></span> <span data-ttu-id="c7104-179">Fiddler aracı ekleme, aşağıdaki resimde gösterilmektedir `Secret` gönderilen form değerlerine alanı ("OverPost" değeriyle).</span><span class="sxs-lookup"><span data-stu-id="c7104-179">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler'ı ekleme gizli alan](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="c7104-181">Değerin "OverPost" eklenen başarıyla `Secret` eklenen satırın özelliği.</span><span class="sxs-lookup"><span data-stu-id="c7104-181">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="c7104-182">Hedeflenen hiçbir Uygulama Tasarımcısı `Secret` Oluştur sayfası ayarlanacak özelliği.</span><span class="sxs-lookup"><span data-stu-id="c7104-182">The app designer never intended the `Secret` property to be set with the Create page.</span></span>

<a name="vm"></a>

### <a name="view-model"></a><span data-ttu-id="c7104-183">Görünüm modeli</span><span class="sxs-lookup"><span data-stu-id="c7104-183">View model</span></span>

<span data-ttu-id="c7104-184">Bir görünüm modeli, genellikle bir uygulama tarafından kullanılan modeline dahil özellik alt kümesi içerir.</span><span class="sxs-lookup"><span data-stu-id="c7104-184">A view model typically contains a subset of the properties included in the model used by the application.</span></span> <span data-ttu-id="c7104-185">Uygulama modeli, genellikle etki alanı modeli adı verilir.</span><span class="sxs-lookup"><span data-stu-id="c7104-185">The application model is often called the domain model.</span></span> <span data-ttu-id="c7104-186">Etki alanı modeli, genellikle DB'de karşılık gelen varlığın gerektirdiği tüm özellikleri içerir.</span><span class="sxs-lookup"><span data-stu-id="c7104-186">The domain model typically contains all the properties required by the corresponding entity in the DB.</span></span> <span data-ttu-id="c7104-187">Görünüm modeli yalnızca UI yerleşiminde (örneğin, oluşturma sayfası) için gereken özellikleri içerir.</span><span class="sxs-lookup"><span data-stu-id="c7104-187">The view model contains only the properties needed for the UI layer (for example, the Create page).</span></span> <span data-ttu-id="c7104-188">Görünüm modeli ek olarak, bazı uygulamalar, Razor sayfaları sayfa modeli sınıfı ile tarayıcı arasında veri iletmek için bağlama modeli veya giriş modeli kullanın.</span><span class="sxs-lookup"><span data-stu-id="c7104-188">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> <span data-ttu-id="c7104-189">Aşağıdakileri göz önünde bulundurun `Student` görünüm modeli:</span><span class="sxs-lookup"><span data-stu-id="c7104-189">Consider the following `Student` view model:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

<span data-ttu-id="c7104-190">Görünüm modelleri overposting önlemek için alternatif bir yol sağlar.</span><span class="sxs-lookup"><span data-stu-id="c7104-190">View models provide an alternative way to prevent overposting.</span></span> <span data-ttu-id="c7104-191">Görünüm modeli (görüntüleme) görüntülemek veya güncelleştirmek için yalnızca özellikleri içerir.</span><span class="sxs-lookup"><span data-stu-id="c7104-191">The view model contains only the properties to view (display) or update.</span></span>

<span data-ttu-id="c7104-192">Aşağıdaki kod `StudentVM` görünüm modeli yeni bir öğrenci oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="c7104-192">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="c7104-193">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) yöntemi, başka değerleri okuyarak bu nesnenin değerleri ayarlar [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) nesne.</span><span class="sxs-lookup"><span data-stu-id="c7104-193">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="c7104-194">`SetValues` özellik adıyla eşleşen kullanır.</span><span class="sxs-lookup"><span data-stu-id="c7104-194">`SetValues` uses property name matching.</span></span> <span data-ttu-id="c7104-195">Görünüm modeli türü modeli türüyle ilişkili gerekmez, bunu yalnızca eşleşen özelliklere sahip olması.</span><span class="sxs-lookup"><span data-stu-id="c7104-195">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="c7104-196">Kullanarak `StudentVM` gerektirir [CreateVM.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) güncelleştirilmesi kullanılacak `StudentVM` yerine `Student`.</span><span class="sxs-lookup"><span data-stu-id="c7104-196">Using `StudentVM` requires [CreateVM.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

<span data-ttu-id="c7104-197">Razor sayfalarında `PageModel` türetilmiş sınıf, görünüm modeli.</span><span class="sxs-lookup"><span data-stu-id="c7104-197">In Razor Pages, the `PageModel` derived class is the view model.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="c7104-198">Güncelleştirme düzenleme sayfası</span><span class="sxs-lookup"><span data-stu-id="c7104-198">Update the Edit page</span></span>

<span data-ttu-id="c7104-199">Sayfa modeli düzenleme sayfası için güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="c7104-199">Update the page model for the Edit page.</span></span> <span data-ttu-id="c7104-200">Önemli değişiklikler vurgulanmıştır:</span><span class="sxs-lookup"><span data-stu-id="c7104-200">The major changes are highlighted:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

<span data-ttu-id="c7104-201">Kod değişikliklerini, birkaç özel durum Oluştur sayfasında benzerdir:</span><span class="sxs-lookup"><span data-stu-id="c7104-201">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="c7104-202">`OnPostAsync` İsteğe bağlı olan `id` parametresi.</span><span class="sxs-lookup"><span data-stu-id="c7104-202">`OnPostAsync` has an optional `id` parameter.</span></span>
* <span data-ttu-id="c7104-203">Geçerli Öğrenci DB'den getirilir. yerine boş bir öğrenci oluşturma.</span><span class="sxs-lookup"><span data-stu-id="c7104-203">The current student is fetched from the DB, rather than creating an empty student.</span></span>
* <span data-ttu-id="c7104-204">`FirstOrDefaultAsync` ile değiştirilmiştir [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span><span class="sxs-lookup"><span data-stu-id="c7104-204">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="c7104-205">`FindAsync` bir varlığın birincil anahtarı seçerken olduğunda iyi bir seçimdir.</span><span class="sxs-lookup"><span data-stu-id="c7104-205">`FindAsync` is a good choice when selecting an entity from the primary key.</span></span> <span data-ttu-id="c7104-206">Bkz: [FindAsync](#FindAsync) daha fazla bilgi için.</span><span class="sxs-lookup"><span data-stu-id="c7104-206">See [FindAsync](#FindAsync) for more information.</span></span>

### <a name="test-the-edit-and-create-pages"></a><span data-ttu-id="c7104-207">Testi düzenleme ve sayfa oluşturma</span><span class="sxs-lookup"><span data-stu-id="c7104-207">Test the Edit and Create pages</span></span>

<span data-ttu-id="c7104-208">Oluşturun ve birkaç Öğrenci varlıklarını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="c7104-208">Create and edit a few student entities.</span></span>

## <a name="entity-states"></a><span data-ttu-id="c7104-209">Varlık durumları</span><span class="sxs-lookup"><span data-stu-id="c7104-209">Entity States</span></span>

<span data-ttu-id="c7104-210">DB bağlamı varlıkları bellekte DB'de karşılık gelen satırlarında ile eşitlenmiş durumda olup olmadığını izler.</span><span class="sxs-lookup"><span data-stu-id="c7104-210">The DB context keeps track of whether entities in memory are in sync with their corresponding rows in the DB.</span></span> <span data-ttu-id="c7104-211">DB bağlamı eşitleme bilgileri ne olacağını belirler, [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c7104-211">The DB context sync information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="c7104-212">Örneğin, ne zaman yeni bir varlık geçirildiğinde [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) varlığın durumu ayarlanmış yöntemi [eklenen](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span><span class="sxs-lookup"><span data-stu-id="c7104-212">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="c7104-213">Zaman `SaveChangesAsync` çağrıldığında DB bağlamı veren bir SQL Ekle komutu.</span><span class="sxs-lookup"><span data-stu-id="c7104-213">When `SaveChangesAsync` is called, the DB context issues a SQL INSERT command.</span></span>

<span data-ttu-id="c7104-214">Bir varlık birinde olabilir [durumları izleyen](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span><span class="sxs-lookup"><span data-stu-id="c7104-214">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="c7104-215">`Added`: Varlık DB'de henüz mevcut değil.</span><span class="sxs-lookup"><span data-stu-id="c7104-215">`Added`: The entity doesn't yet exist in the DB.</span></span> <span data-ttu-id="c7104-216">`SaveChanges` Yöntemi sorunları INSERT deyimi.</span><span class="sxs-lookup"><span data-stu-id="c7104-216">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="c7104-217">`Unchanged`: Herhangi bir değişiklik ile bu varlık kaydedilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="c7104-217">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="c7104-218">Veritabanından okurken bir varlık bu duruma sahip.</span><span class="sxs-lookup"><span data-stu-id="c7104-218">An entity has this status when it's read from the DB.</span></span>

* <span data-ttu-id="c7104-219">`Modified`: Bazılarını veya tümünü varlığın özellik değerleri değiştirilmiş.</span><span class="sxs-lookup"><span data-stu-id="c7104-219">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="c7104-220">`SaveChanges` Yöntemi, bir güncelleştirme deyimiyle verir.</span><span class="sxs-lookup"><span data-stu-id="c7104-220">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="c7104-221">`Deleted`: Varlık silinmek üzere işaretlendi.</span><span class="sxs-lookup"><span data-stu-id="c7104-221">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="c7104-222">`SaveChanges` Yöntemi DELETE deyimi verir.</span><span class="sxs-lookup"><span data-stu-id="c7104-222">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="c7104-223">`Detached`: Varlık DB bağlamı tarafından izleniyor değil.</span><span class="sxs-lookup"><span data-stu-id="c7104-223">`Detached`: The entity isn't being tracked by the DB context.</span></span>

<span data-ttu-id="c7104-224">İçinde bir masaüstü uygulaması, durum değişikliklerini genellikle otomatik olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c7104-224">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="c7104-225">Bir varlık okuma, değişiklik yapıldıysa ve otomatik olarak değiştirilecek şekilde varlık durumu `Modified`.</span><span class="sxs-lookup"><span data-stu-id="c7104-225">An entity is read, changes are made, and the entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="c7104-226">Çağırma `SaveChanges` yalnızca değiştirilen özellikler güncelleştiren SQL UPDATE deyimi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c7104-226">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="c7104-227">Bir web uygulaması `DbContext` varlık ve verileri bir sayfa oluşturulduğunda elden görüntüler okur.</span><span class="sxs-lookup"><span data-stu-id="c7104-227">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="c7104-228">Bir sayfa ayarlandığında `OnPostAsync` yöntemi çağrıldığında, yeni bir web isteği yapılır ve yeni bir örneğini `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="c7104-228">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="c7104-229">Varlık bu yeni bir bağlam içinde yeniden okuma Masaüstü işleme benzetimini yapar.</span><span class="sxs-lookup"><span data-stu-id="c7104-229">Re-reading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="c7104-230">Silme sayfası</span><span class="sxs-lookup"><span data-stu-id="c7104-230">Update the Delete page</span></span>

<span data-ttu-id="c7104-231">Bu bölümde, kod bir özel hata uygulamak için eklenen zaman ileti çağrısı `SaveChanges` başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="c7104-231">In this section, code is added to implement a custom error message when the call to `SaveChanges` fails.</span></span> <span data-ttu-id="c7104-232">Olası hata iletileri içeren bir dize ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c7104-232">Add a string to contain possible error messages:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

<span data-ttu-id="c7104-233">Değiştirin `OnGetAsync` yöntemini aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="c7104-233">Replace the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

<span data-ttu-id="c7104-234">Yukarıdaki kod, isteğe bağlı parametre içeren `saveChangesError`.</span><span class="sxs-lookup"><span data-stu-id="c7104-234">The preceding code contains the optional parameter `saveChangesError`.</span></span> <span data-ttu-id="c7104-235">`saveChangesError` Öğrenci nesneyi silmek için bir hatadan sonra yöntemi çağrıldı olup olmadığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="c7104-235">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="c7104-236">Silme işlemi, geçici ağ sorunları nedeniyle başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="c7104-236">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="c7104-237">Geçici ağ hataları bulutta daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="c7104-237">Transient network errors are more likely in the cloud.</span></span> <span data-ttu-id="c7104-238">`saveChangesError`false olduğunda silme sayfası `OnGetAsync` Arabiriminden çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c7104-238">`saveChangesError`is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="c7104-239">Zaman `OnGetAsync` tarafından çağrılır `OnPostAsync` (silme işlemi başarısız olduğundan), `saveChangesError` parametre değeri true.</span><span class="sxs-lookup"><span data-stu-id="c7104-239">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

### <a name="the-delete-pages-onpostasync-method"></a><span data-ttu-id="c7104-240">Silmeyi sayfalar OnPostAsync yöntemi</span><span class="sxs-lookup"><span data-stu-id="c7104-240">The Delete pages OnPostAsync method</span></span>

<span data-ttu-id="c7104-241">Değiştirin `OnPostAsync` aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="c7104-241">Replace the `OnPostAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="c7104-242">Yukarıdaki kod, seçili varlığı alır. ardından çağırır [Kaldır](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) varlığın durumu ayarlamak için yöntemi `Deleted`.</span><span class="sxs-lookup"><span data-stu-id="c7104-242">The preceding code retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="c7104-243">Zaman `SaveChanges` çağrılır, SQL'i Sil komut oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c7104-243">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="c7104-244">Varsa `Remove` başarısız olur:</span><span class="sxs-lookup"><span data-stu-id="c7104-244">If `Remove` fails:</span></span>

* <span data-ttu-id="c7104-245">DB özel durum yakalandı.</span><span class="sxs-lookup"><span data-stu-id="c7104-245">The DB exception is caught.</span></span>
* <span data-ttu-id="c7104-246">Silmeyi sayfalar `OnGetAsync` yöntemi çağrıldığında `saveChangesError=true`.</span><span class="sxs-lookup"><span data-stu-id="c7104-246">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="c7104-247">Delete Razor sayfası</span><span class="sxs-lookup"><span data-stu-id="c7104-247">Update the Delete Razor Page</span></span>

<span data-ttu-id="c7104-248">Aşağıdaki vurgulanan hata iletisini Sil Razor sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c7104-248">Add the following highlighted error message to the Delete Razor Page.</span></span>
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

<span data-ttu-id="c7104-249">Delete test edin.</span><span class="sxs-lookup"><span data-stu-id="c7104-249">Test Delete.</span></span>

## <a name="common-errors"></a><span data-ttu-id="c7104-250">Sık karşılaşılan hatalar</span><span class="sxs-lookup"><span data-stu-id="c7104-250">Common errors</span></span>

<span data-ttu-id="c7104-251">Öğrenciler/dizin veya diğer bağlantılar çalışmaz:</span><span class="sxs-lookup"><span data-stu-id="c7104-251">Students/Index or other links don't work:</span></span>

<span data-ttu-id="c7104-252">Razor sayfası doğru içerdiğini doğrulayın `@page` yönergesi.</span><span class="sxs-lookup"><span data-stu-id="c7104-252">Verify the Razor Page contains the correct `@page` directive.</span></span> <span data-ttu-id="c7104-253">Örneğin, Öğrenciler/dizin Razor sayfası gereken **değil** rota şablonu içerir:</span><span class="sxs-lookup"><span data-stu-id="c7104-253">For example, The Students/Index Razor Page should **not** contain a route template:</span></span>

```cshtml
@page "{id:int}"
```

<span data-ttu-id="c7104-254">Her bir Razor sayfası içermelidir `@page` yönergesi.</span><span class="sxs-lookup"><span data-stu-id="c7104-254">Each Razor Page must include the `@page` directive.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> <span data-ttu-id="c7104-255">[Önceki](xref:data/ef-rp/intro)
> [İleri](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="c7104-255">[Previous](xref:data/ef-rp/intro)
[Next](xref:data/ef-rp/sort-filter-page)</span></span>
