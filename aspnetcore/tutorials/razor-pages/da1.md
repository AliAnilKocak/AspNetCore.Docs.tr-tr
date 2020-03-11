---
title: ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme
author: rick-anderson
description: ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme hakkında bilgi edinin.
ms.author: riande
ms.date: 12/20/2018
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 0f6535462fe2d308825bf7289c10d2b0690cebd4
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666217"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="06e16-103">ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="06e16-103">Update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="06e16-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="06e16-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="06e16-105">Yapı iskelesi film uygulamasının iyi bir başlangıcı vardır ancak sunum ideal değildir.</span><span class="sxs-lookup"><span data-stu-id="06e16-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="06e16-106">**ReleaseDate** **Yayın tarihi** (iki sözcük) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="06e16-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Chrome 'da açık film uygulaması](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="06e16-108">Oluşturulan kodu Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="06e16-108">Update the generated code</span></span>

<span data-ttu-id="06e16-109">*Modeller/film. cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanmış satırları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="06e16-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="06e16-110">`[Column(TypeName = "decimal(18, 2)")]` veri ek açıklaması, Entity Framework Core veritabanında `Price` para birimine doğru şekilde eşlemesine olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="06e16-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="06e16-111">Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="06e16-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="06e16-112">[Veri açıklamaları](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="06e16-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="06e16-113">[Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) özniteliği bir alanın adı için (Bu durumda "ReleaseDate" yerine "Yayın tarihi") görüntüleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="06e16-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="06e16-114">[DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği verilerin türünü belirtir (Tarih), bu nedenle alanda depolanan zaman bilgileri gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="06e16-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="06e16-115">Hedef URL 'yi görmek için sayfalara/filmlere gidin ve bir **düzenleme** bağlantısının üzerine gelin.</span><span class="sxs-lookup"><span data-stu-id="06e16-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Düzenleme bağlantısı üzerinde fare olan tarayıcı penceresi ve http://localhost:1234/Movies/Edit/5 bağlantı URL 'Si gösteriliyor](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="06e16-117">**Düzenle**, **Ayrıntılar**ve **Sil** bağlantıları, *Sayfalar/filmler/Index. cshtml* dosyasındaki [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="06e16-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="06e16-118">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.</span><span class="sxs-lookup"><span data-stu-id="06e16-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="06e16-119">Yukarıdaki kodda `AnchorTagHelper`, Razor sayfasından (yol göreli), `asp-page`ve yol kimliği (`asp-route-id`) HTML `href` öznitelik değerini dinamik olarak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="06e16-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="06e16-120">Daha fazla bilgi için bkz. [Sayfalar Için URL oluşturma](xref:razor-pages/index#url-generation-for-pages) .</span><span class="sxs-lookup"><span data-stu-id="06e16-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="06e16-121">Oluşturulan biçimlendirmeyi incelemek için sık kullandığınız tarayıcıdan **Görünüm kaynağını** kullanın.</span><span class="sxs-lookup"><span data-stu-id="06e16-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="06e16-122">Oluşturulan HTML 'nin bir bölümü aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="06e16-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="06e16-123">Dinamik olarak oluşturulan bağlantılar, film KIMLIĞINI bir sorgu dizesiyle (örneğin, `https://localhost:5001/Movies/Details?id=1``?id=1`) iletir.</span><span class="sxs-lookup"><span data-stu-id="06e16-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

### <a name="add-route-template"></a><span data-ttu-id="06e16-124">Rota şablonu Ekle</span><span class="sxs-lookup"><span data-stu-id="06e16-124">Add route template</span></span>

<span data-ttu-id="06e16-125">"{İd: int}" yol şablonunu kullanmak için Düzenle, Ayrıntılar ve Sil Razor Pages güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="06e16-125">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="06e16-126">Bu sayfaların her biri için Page yönergesini `@page "{id:int}"``@page` değiştirin.</span><span class="sxs-lookup"><span data-stu-id="06e16-126">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="06e16-127">Uygulamayı çalıştırın ve kaynağı görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="06e16-127">Run the app and then view source.</span></span> <span data-ttu-id="06e16-128">Oluşturulan HTML, URL 'nin yol bölümüne KIMLIĞI ekler:</span><span class="sxs-lookup"><span data-stu-id="06e16-128">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="06e16-129">Tamsayıyı **içermeyen "** {id: int}" yol şablonuna sahip sayfaya yönelik bir Istek, HTTP 404 (bulunamadı) hatası döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="06e16-129">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="06e16-130">Örneğin, `http://localhost:5000/Movies/Details` bir 404 hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="06e16-130">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="06e16-131">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="06e16-131">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="06e16-132">`@page "{id:int?}"`davranışını test etmek için:</span><span class="sxs-lookup"><span data-stu-id="06e16-132">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="06e16-133">*Pages/filmler/details. cshtml* içindeki page yönergesini `@page "{id:int?}"`olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="06e16-133">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="06e16-134">`public async Task<IActionResult> OnGetAsync(int? id)` ( *sayfalarda/filmlerde/details. cshtml. cs*) bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="06e16-134">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="06e16-135">`https://localhost:5001/Movies/Details/` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="06e16-135">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="06e16-136">`@page "{id:int}"` yönergesi ile, kesme noktası hiçbir şekilde vurılmaz.</span><span class="sxs-lookup"><span data-stu-id="06e16-136">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="06e16-137">Yönlendirme Altyapısı HTTP 404 döndürür.</span><span class="sxs-lookup"><span data-stu-id="06e16-137">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="06e16-138">`@page "{id:int?}"`kullanarak `OnGetAsync` yöntemi `NotFound` (HTTP 404) döndürür.</span><span class="sxs-lookup"><span data-stu-id="06e16-138">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="06e16-139">Eşzamanlılık özel durum işlemeyi gözden geçirme</span><span class="sxs-lookup"><span data-stu-id="06e16-139">Review concurrency exception handling</span></span>

<span data-ttu-id="06e16-140">*Pages/filmler/Edit. cshtml. cs* dosyasındaki `OnPostAsync` yöntemini gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="06e16-140">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="06e16-141">Önceki kod, bir istemci filmi sildiği ve diğer istemci filmle değişiklik yaptığı zaman eşzamanlılık özel durumlarını algılar.</span><span class="sxs-lookup"><span data-stu-id="06e16-141">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="06e16-142">`catch` bloğunu test etmek için:</span><span class="sxs-lookup"><span data-stu-id="06e16-142">To test the `catch` block:</span></span>

* <span data-ttu-id="06e16-143">`catch (DbUpdateConcurrencyException)` kesme noktası ayarlama</span><span class="sxs-lookup"><span data-stu-id="06e16-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="06e16-144">Film için **Düzenle** ' yi seçin, değişiklikler yapın, ancak **Kaydet**' i girmeyin.</span><span class="sxs-lookup"><span data-stu-id="06e16-144">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="06e16-145">Başka bir tarayıcı penceresinde, aynı filmin **Sil** bağlantısını seçin ve ardından filmi silin.</span><span class="sxs-lookup"><span data-stu-id="06e16-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="06e16-146">Önceki tarayıcı penceresinde filmdeki değişiklikleri gönderin.</span><span class="sxs-lookup"><span data-stu-id="06e16-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="06e16-147">Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="06e16-147">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="06e16-148">Daha fazla bilgi için bkz. [eşzamanlılık çakışmalarını işleme](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="06e16-148">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="06e16-149">Gönderme ve bağlama incelemesi</span><span class="sxs-lookup"><span data-stu-id="06e16-149">Posting and binding review</span></span>

<span data-ttu-id="06e16-150">*Pages/filmler/Edit. cshtml. cs* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="06e16-150">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="06e16-151">Filmler/düzenleme sayfasına HTTP GET isteği yapıldığında (örneğin, `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="06e16-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="06e16-152">`OnGetAsync` yöntemi, filmi veritabanından getirir ve `Page` yöntemini döndürür.</span><span class="sxs-lookup"><span data-stu-id="06e16-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="06e16-153">`Page` yöntemi, *Pages/filmler/Edit. cshtml* Razor sayfasını işler.</span><span class="sxs-lookup"><span data-stu-id="06e16-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="06e16-154">*Pages/filmler/Edit. cshtml* dosyası, film modelinin sayfada kullanılabilir olmasını sağlayan model yönergesini (`@model RazorPagesMovie.Pages.Movies.EditModel`) içerir.</span><span class="sxs-lookup"><span data-stu-id="06e16-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="06e16-155">Düzenleme formu filmdeki değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="06e16-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="06e16-156">Filmler/Düzenle sayfası gönderildiğinde:</span><span class="sxs-lookup"><span data-stu-id="06e16-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="06e16-157">Sayfadaki form değerleri `Movie` özelliğine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="06e16-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="06e16-158">`[BindProperty]` özniteliği [model bağlamayı](xref:mvc/models/model-binding)mümkün.</span><span class="sxs-lookup"><span data-stu-id="06e16-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="06e16-159">Model durumunda hatalar varsa (örneğin, `ReleaseDate` bir tarihe dönüştürülemiyorsa), form gönderilen değerlerle yeniden görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="06e16-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="06e16-160">Model hatası yoksa, film kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="06e16-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="06e16-161">Razor sayfalarında Dizin, oluşturma ve silme gibi HTTP GET yöntemleri benzer bir düzende yer alır.</span><span class="sxs-lookup"><span data-stu-id="06e16-161">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="06e16-162">Razor Oluştur sayfasındaki HTTP POST `OnPostAsync` yöntemi, Razor düzenleme sayfasındaki `OnPostAsync` yöntemine benzer bir düzen izler.</span><span class="sxs-lookup"><span data-stu-id="06e16-162">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06e16-163">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="06e16-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="06e16-164">[Önceki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql)
> [Ileri: Arama ekle](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="06e16-164">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="06e16-165">Yapı iskelesi film uygulamasının iyi bir başlangıcı vardır ancak sunum ideal değildir.</span><span class="sxs-lookup"><span data-stu-id="06e16-165">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="06e16-166">**ReleaseDate** **Yayın tarihi** (iki sözcük) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="06e16-166">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Chrome 'da açık film uygulaması](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="06e16-168">Oluşturulan kodu Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="06e16-168">Update the generated code</span></span>

<span data-ttu-id="06e16-169">*Modeller/film. cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanmış satırları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="06e16-169">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="06e16-170">`[Column(TypeName = "decimal(18, 2)")]` veri ek açıklaması, Entity Framework Core veritabanında `Price` para birimine doğru şekilde eşlemesine olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="06e16-170">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="06e16-171">Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="06e16-171">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="06e16-172">[Veri açıklamaları](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="06e16-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="06e16-173">[Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) özniteliği bir alanın adı için (Bu durumda "ReleaseDate" yerine "Yayın tarihi") görüntüleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="06e16-173">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="06e16-174">[DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği verilerin türünü belirtir (Tarih), bu nedenle alanda depolanan zaman bilgileri gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="06e16-174">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="06e16-175">Hedef URL 'yi görmek için sayfalara/filmlere gidin ve bir **düzenleme** bağlantısının üzerine gelin.</span><span class="sxs-lookup"><span data-stu-id="06e16-175">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Düzenleme bağlantısı üzerinde fare olan tarayıcı penceresi ve http://localhost:1234/Movies/Edit/5 bağlantı URL 'Si gösteriliyor](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="06e16-177">**Düzenle**, **Ayrıntılar**ve **Sil** bağlantıları, *Sayfalar/filmler/Index. cshtml* dosyasındaki [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="06e16-177">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="06e16-178">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.</span><span class="sxs-lookup"><span data-stu-id="06e16-178">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="06e16-179">Yukarıdaki kodda `AnchorTagHelper`, Razor sayfasından (yol göreli), `asp-page`ve yol kimliği (`asp-route-id`) HTML `href` öznitelik değerini dinamik olarak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="06e16-179">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="06e16-180">Daha fazla bilgi için bkz. [Sayfalar Için URL oluşturma](xref:razor-pages/index#url-generation-for-pages) .</span><span class="sxs-lookup"><span data-stu-id="06e16-180">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="06e16-181">Oluşturulan biçimlendirmeyi incelemek için sık kullandığınız tarayıcıdan **Görünüm kaynağını** kullanın.</span><span class="sxs-lookup"><span data-stu-id="06e16-181">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="06e16-182">Oluşturulan HTML 'nin bir bölümü aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="06e16-182">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="06e16-183">Dinamik olarak oluşturulan bağlantılar, film KIMLIĞINI bir sorgu dizesiyle (örneğin, `https://localhost:5001/Movies/Details?id=1``?id=1`) iletir.</span><span class="sxs-lookup"><span data-stu-id="06e16-183">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="06e16-184">"{İd: int}" yol şablonunu kullanmak için Düzenle, Ayrıntılar ve Sil Razor Pages güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="06e16-184">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="06e16-185">Bu sayfaların her biri için Page yönergesini `@page "{id:int}"``@page` değiştirin.</span><span class="sxs-lookup"><span data-stu-id="06e16-185">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="06e16-186">Uygulamayı çalıştırın ve kaynağı görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="06e16-186">Run the app and then view source.</span></span> <span data-ttu-id="06e16-187">Oluşturulan HTML, URL 'nin yol bölümüne KIMLIĞI ekler:</span><span class="sxs-lookup"><span data-stu-id="06e16-187">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="06e16-188">Tamsayıyı **içermeyen "** {id: int}" yol şablonuna sahip sayfaya yönelik bir Istek, HTTP 404 (bulunamadı) hatası döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="06e16-188">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="06e16-189">Örneğin, `http://localhost:5000/Movies/Details` bir 404 hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="06e16-189">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="06e16-190">KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="06e16-190">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="06e16-191">`@page "{id:int?}"`davranışını test etmek için:</span><span class="sxs-lookup"><span data-stu-id="06e16-191">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="06e16-192">*Pages/filmler/details. cshtml* içindeki page yönergesini `@page "{id:int?}"`olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="06e16-192">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="06e16-193">`public async Task<IActionResult> OnGetAsync(int? id)` ( *sayfalarda/filmlerde/details. cshtml. cs*) bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="06e16-193">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="06e16-194">`https://localhost:5001/Movies/Details/` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="06e16-194">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="06e16-195">`@page "{id:int}"` yönergesi ile, kesme noktası hiçbir şekilde vurılmaz.</span><span class="sxs-lookup"><span data-stu-id="06e16-195">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="06e16-196">Yönlendirme Altyapısı HTTP 404 döndürür.</span><span class="sxs-lookup"><span data-stu-id="06e16-196">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="06e16-197">`@page "{id:int?}"`kullanarak `OnGetAsync` yöntemi `NotFound` (HTTP 404) döndürür.</span><span class="sxs-lookup"><span data-stu-id="06e16-197">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="06e16-198">Eşzamanlılık özel durum işlemeyi gözden geçirme</span><span class="sxs-lookup"><span data-stu-id="06e16-198">Review concurrency exception handling</span></span>

<span data-ttu-id="06e16-199">*Pages/filmler/Edit. cshtml. cs* dosyasındaki `OnPostAsync` yöntemini gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="06e16-199">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="06e16-200">Önceki kod, bir istemci filmi sildiği ve diğer istemci filmle değişiklik yaptığı zaman eşzamanlılık özel durumlarını algılar.</span><span class="sxs-lookup"><span data-stu-id="06e16-200">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="06e16-201">`catch` bloğunu test etmek için:</span><span class="sxs-lookup"><span data-stu-id="06e16-201">To test the `catch` block:</span></span>

* <span data-ttu-id="06e16-202">`catch (DbUpdateConcurrencyException)` kesme noktası ayarlama</span><span class="sxs-lookup"><span data-stu-id="06e16-202">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="06e16-203">Film için **Düzenle** ' yi seçin, değişiklikler yapın, ancak **Kaydet**' i girmeyin.</span><span class="sxs-lookup"><span data-stu-id="06e16-203">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="06e16-204">Başka bir tarayıcı penceresinde, aynı filmin **Sil** bağlantısını seçin ve ardından filmi silin.</span><span class="sxs-lookup"><span data-stu-id="06e16-204">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="06e16-205">Önceki tarayıcı penceresinde filmdeki değişiklikleri gönderin.</span><span class="sxs-lookup"><span data-stu-id="06e16-205">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="06e16-206">Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="06e16-206">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="06e16-207">Daha fazla bilgi için bkz. [eşzamanlılık çakışmalarını işleme](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="06e16-207">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="06e16-208">Gönderme ve bağlama incelemesi</span><span class="sxs-lookup"><span data-stu-id="06e16-208">Posting and binding review</span></span>

<span data-ttu-id="06e16-209">*Pages/filmler/Edit. cshtml. cs* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="06e16-209">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="06e16-210">Filmler/düzenleme sayfasına HTTP GET isteği yapıldığında (örneğin, `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="06e16-210">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="06e16-211">`OnGetAsync` yöntemi, filmi veritabanından getirir ve `Page` yöntemini döndürür.</span><span class="sxs-lookup"><span data-stu-id="06e16-211">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="06e16-212">`Page` yöntemi, *Pages/filmler/Edit. cshtml* Razor sayfasını işler.</span><span class="sxs-lookup"><span data-stu-id="06e16-212">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="06e16-213">*Pages/filmler/Edit. cshtml* dosyası, film modelinin sayfada kullanılabilir olmasını sağlayan model yönergesini (`@model RazorPagesMovie.Pages.Movies.EditModel`) içerir.</span><span class="sxs-lookup"><span data-stu-id="06e16-213">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="06e16-214">Düzenleme formu filmdeki değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="06e16-214">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="06e16-215">Filmler/Düzenle sayfası gönderildiğinde:</span><span class="sxs-lookup"><span data-stu-id="06e16-215">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="06e16-216">Sayfadaki form değerleri `Movie` özelliğine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="06e16-216">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="06e16-217">`[BindProperty]` özniteliği [model bağlamayı](xref:mvc/models/model-binding)mümkün.</span><span class="sxs-lookup"><span data-stu-id="06e16-217">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="06e16-218">Model durumunda hatalar varsa (örneğin, `ReleaseDate` bir tarihe dönüştürülemiyorsa), form gönderilen değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="06e16-218">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="06e16-219">Model hatası yoksa, film kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="06e16-219">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="06e16-220">Razor sayfalarında Dizin, oluşturma ve silme gibi HTTP GET yöntemleri benzer bir düzende yer alır.</span><span class="sxs-lookup"><span data-stu-id="06e16-220">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="06e16-221">Razor Oluştur sayfasındaki HTTP POST `OnPostAsync` yöntemi, Razor düzenleme sayfasındaki `OnPostAsync` yöntemine benzer bir düzen izler.</span><span class="sxs-lookup"><span data-stu-id="06e16-221">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="06e16-222">Arama sonraki öğreticiye eklenir.</span><span class="sxs-lookup"><span data-stu-id="06e16-222">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06e16-223">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="06e16-223">Additional resources</span></span>

* [<span data-ttu-id="06e16-224">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="06e16-224">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="06e16-225">[Önceki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql)
> [Ileri: Arama ekle](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="06e16-225">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
