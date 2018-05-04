---
title: Arama ASP.NET Core Razor sayfalara ekleme
author: rick-anderson
description: Arama ASP.NET Core Razor sayfalara eklemek nasıl gösterir
manager: wpickett
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 08/07/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages/search
ms.openlocfilehash: b547b67b3e51562633ea06d3730145f49c6043ea
ms.sourcegitcommit: 5130b3034165f5cf49d829fe7475a84aa33d2693
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/03/2018
---
# <a name="add-search-to-aspnet-core-razor-pages"></a><span data-ttu-id="06794-103">Arama ASP.NET Core Razor sayfalara ekleme</span><span class="sxs-lookup"><span data-stu-id="06794-103">Add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="06794-104">tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="06794-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="06794-105">Bu belgede, arama özelliği tarafından arama filmler etkinleştirir dizin sayfası eklenir *Tarz* veya *adı*.</span><span class="sxs-lookup"><span data-stu-id="06794-105">In this document, search capability is added to the Index page that enables searching movies by *genre* or *name*.</span></span>

<span data-ttu-id="06794-106">Dizin sayfasının güncelleştirme `OnGetAsync` aşağıdaki kod ile yöntemi:</span><span class="sxs-lookup"><span data-stu-id="06794-106">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="06794-107">İlk satırı `OnGetAsync` yöntemi oluşturur bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) filmler seçmek için sorgu:</span><span class="sxs-lookup"><span data-stu-id="06794-107">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="06794-108">Sorgu *yalnızca* bu noktada tanımlı olan **değil** veritabanına karşı çalışırlar bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="06794-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="06794-109">Varsa `searchString` parametre içeren bir dize, filmler sorgu filtre arama dizesi şekilde değiştirilir:</span><span class="sxs-lookup"><span data-stu-id="06794-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="06794-110">`s => s.Title.Contains()` Kodu bir [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="06794-110">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="06794-111">Lambda'lar yöntemi tabanlı içinde kullanılan [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) standart sorgu işleci yöntemlerinden bağımsız değişken olarak gibi sorgular [nerede](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) yöntemi veya `Contains` (Yukarıdaki kod içinde kullanılan).</span><span class="sxs-lookup"><span data-stu-id="06794-111">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="06794-112">LINQ sorgularını değil tanımlanan veya ne zaman bir yöntemini çağırarak değiştirilmeden yürütülen (gibi `Where`, `Contains` veya `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="06794-112">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="06794-113">Bunun yerine, sorgu yürütme ertelenir.</span><span class="sxs-lookup"><span data-stu-id="06794-113">Rather, query execution is deferred.</span></span> <span data-ttu-id="06794-114">Bir ifadenin değerlendirmesine üzerinden gerçekleşen değerini yinelendiğinde kadar Gecikmeli anlamına veya `ToListAsync` yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="06794-114">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="06794-115">Bkz: [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution) daha fazla bilgi için.</span><span class="sxs-lookup"><span data-stu-id="06794-115">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="06794-116">**Not:** [içerir](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi, C# kodu değil, veritabanında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="06794-116">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="06794-117">Büyük küçük harfe duyarlılığın sorgusu, veritabanı ve harmanlama bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="06794-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="06794-118">SQL Server `Contains` eşlendiği [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), büyük küçük harfe duyarlı olduğu.</span><span class="sxs-lookup"><span data-stu-id="06794-118">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="06794-119">SQLite içinde varsayılan harmanlaması ile büyük küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="06794-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="06794-120">Film sayfasına gidin ve bir sorgu dizesi gibi ilave `?searchString=Ghost` URL (örneğin, `http://localhost:5000/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="06794-120">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `http://localhost:5000/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="06794-121">Filtrelenmiş filmler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="06794-121">The filtered movies are displayed.</span></span>

![Dizin görünümü](search/_static/ghost.png)

<span data-ttu-id="06794-123">Aşağıdaki rota şablonu dizin sayfasına eklediyseniz, arama dizesini bir URL kesimi geçirilebilir (örneğin, `http://localhost:5000/Movies/ghost`).</span><span class="sxs-lookup"><span data-stu-id="06794-123">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `http://localhost:5000/Movies/ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="06794-124">Önceki rota kısıtlaması, bir sorgu dizesi değeri olarak rota verilerini (URL kesimi) yerine unvanını arama sağlar.</span><span class="sxs-lookup"><span data-stu-id="06794-124">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="06794-125">`?` İçinde `"{searchString?}"` bu bir isteğe bağlı bir rota parametresini anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="06794-125">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Url ve iki filmler, Ghostbusters ve Ghostbusters 2 döndürülen film listesi eklenen word hayalet dizin görünümünün](search/_static/g2.png)

<span data-ttu-id="06794-127">Ancak, bir filmi için aranacak URL'sini değiştirmek için kullanıcıların beklediğiniz olamaz.</span><span class="sxs-lookup"><span data-stu-id="06794-127">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="06794-128">Bu adımda, filmler filtrelemek için kullanıcı Arabirimi eklenir.</span><span class="sxs-lookup"><span data-stu-id="06794-128">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="06794-129">Rota kısıtlaması eklediyseniz `"{searchString?}"`, kaldırın.</span><span class="sxs-lookup"><span data-stu-id="06794-129">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="06794-130">Açık *Pages/Movies/Index.cshtml* dosya ve ekleme `<form>` aşağıdaki kodda vurgulanan biçimlendirme:</span><span class="sxs-lookup"><span data-stu-id="06794-130">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="06794-131">HTML `<form>` etiketi kullanır [Form etiketi yardımcı](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="06794-131">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="06794-132">Form gönderildiğinde, filtre dizesi gönderilen *filmler/sayfalar/dizin* sayfası.</span><span class="sxs-lookup"><span data-stu-id="06794-132">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page.</span></span> <span data-ttu-id="06794-133">Değişiklikleri kaydetmek ve filtre sınayın.</span><span class="sxs-lookup"><span data-stu-id="06794-133">Save the changes and test the filter.</span></span>

![Word hayalet başlığı filtre metin kutusuna yazdığınız dizin görünümünün](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="06794-135">Türe göre ara</span><span class="sxs-lookup"><span data-stu-id="06794-135">Search by genre</span></span>

<span data-ttu-id="06794-136">Aşağıdaki vurgulanan özellikleri ekleyin *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="06794-136">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

<span data-ttu-id="06794-137">`SelectList Genres` Türler listesini içerir.</span><span class="sxs-lookup"><span data-stu-id="06794-137">The `SelectList Genres` contains the list of genres.</span></span> <span data-ttu-id="06794-138">Bu kullanıcının listeden bir tarzını seçmesine olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="06794-138">This allows the user to select a genre from the list.</span></span>

<span data-ttu-id="06794-139">`MovieGenre` Özelliği, kullanıcı seçer (örneğin, "Batı") belirli bir tarzını içerir.</span><span class="sxs-lookup"><span data-stu-id="06794-139">The `MovieGenre` property contains the specific genre the user selects (for example, "Western").</span></span>

<span data-ttu-id="06794-140">Güncelleştirme `OnGetAsync` aşağıdaki kod ile yöntemi:</span><span class="sxs-lookup"><span data-stu-id="06794-140">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="06794-141">Tüm türler veritabanından alır bir LINQ Sorgu kodudur.</span><span class="sxs-lookup"><span data-stu-id="06794-141">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="06794-142">`SelectList` Türler farklı türler yansıtma tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="06794-142">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

<!-- BUG in OPS
Tag snippet_selectlist's start line '75' should be less than end line '29' when resolving "[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]"

There's no start line.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]
-->

```csharp
Genres = new SelectList(await genreQuery.Distinct().ToListAsync());
```

### <a name="adding-search-by-genre"></a><span data-ttu-id="06794-143">Türe göre arama ekleme</span><span class="sxs-lookup"><span data-stu-id="06794-143">Adding search by genre</span></span>

<span data-ttu-id="06794-144">Güncelleştirme *Index.cshtml* gibi:</span><span class="sxs-lookup"><span data-stu-id="06794-144">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="06794-145">Genre, filmi ve her ikisi tarafından arayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="06794-145">Test the app by searching by genre, by movie title, and by both.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="06794-146">[Önceki: sayfalarını güncelleştirme](xref:tutorials/razor-pages/da1)
> [sonraki: yeni bir alan ekleme](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="06794-146">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>
