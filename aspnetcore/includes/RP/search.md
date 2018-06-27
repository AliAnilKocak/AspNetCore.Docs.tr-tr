# <a name="add-search-to-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="30966-101">Arama için bir ASP.NET Core Razor sayfalarının uygulama ekleyin</span><span class="sxs-lookup"><span data-stu-id="30966-101">Add search to an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="30966-102">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="30966-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="30966-103">Bu belgede, arama özelliği tarafından arama filmler etkinleştirir dizin sayfası eklenir *Tarz* veya *adı*.</span><span class="sxs-lookup"><span data-stu-id="30966-103">In this document, search capability is added to the Index page that enables searching movies by *genre* or *name*.</span></span>

<span data-ttu-id="30966-104">Dizin sayfasının güncelleştirme `OnGetAsync` aşağıdaki kod ile yöntemi:</span><span class="sxs-lookup"><span data-stu-id="30966-104">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-cshtml[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_ViewStart.cshtml)]

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="30966-105">İlk satırı `OnGetAsync` yöntemi oluşturur bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) filmler seçmek için sorgu:</span><span class="sxs-lookup"><span data-stu-id="30966-105">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="30966-106">Sorgu *yalnızca* bu noktada tanımlı olan **değil** veritabanına karşı çalışırlar bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="30966-106">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="30966-107">Varsa `searchString` parametre içeren bir dize, filmler sorgu filtre arama dizesi şekilde değiştirilir:</span><span class="sxs-lookup"><span data-stu-id="30966-107">If the `searchString` parameter contains a string, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="30966-108">`s => s.Title.Contains()` Kodu bir [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="30966-108">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="30966-109">Lambda'lar yöntemi tabanlı içinde kullanılan [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) standart sorgu işleci yöntemlerinden bağımsız değişken olarak gibi sorgular [nerede](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) yöntemi veya `Contains` (Yukarıdaki kod içinde kullanılan).</span><span class="sxs-lookup"><span data-stu-id="30966-109">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="30966-110">LINQ sorgularını değil tanımlanan veya ne zaman bir yöntemini çağırarak değiştirilmeden yürütülen (gibi `Where`, `Contains` veya `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="30966-110">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="30966-111">Bunun yerine, sorgu yürütme ertelenir.</span><span class="sxs-lookup"><span data-stu-id="30966-111">Rather, query execution is deferred.</span></span> <span data-ttu-id="30966-112">Bir ifadenin değerlendirmesine üzerinden gerçekleşen değerini yinelendiğinde kadar Gecikmeli anlamına veya `ToListAsync` yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="30966-112">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="30966-113">Bkz: [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution) daha fazla bilgi için.</span><span class="sxs-lookup"><span data-stu-id="30966-113">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="30966-114">**Not:** [içerir](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi, C# kodu değil, veritabanında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="30966-114">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="30966-115">Büyük küçük harfe duyarlılığın sorgusu, veritabanı ve harmanlama bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="30966-115">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="30966-116">SQL Server `Contains` eşlendiği [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), büyük küçük harfe duyarlı olduğu.</span><span class="sxs-lookup"><span data-stu-id="30966-116">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="30966-117">SQLite içinde varsayılan harmanlaması ile büyük küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="30966-117">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="30966-118">Film sayfasına gidin ve bir sorgu dizesi gibi ilave `?searchString=Ghost` URL (örneğin, `http://localhost:5000/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="30966-118">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `http://localhost:5000/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="30966-119">Filtrelenmiş filmler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="30966-119">The filtered movies are displayed.</span></span>

![Dizin görünümü](../../tutorials/razor-pages/search/_static/ghost.png)

<span data-ttu-id="30966-121">Aşağıdaki rota şablonu dizin sayfasına eklediyseniz, arama dizesini bir URL kesimi geçirilebilir (örneğin, `http://localhost:5000/Movies/ghost`).</span><span class="sxs-lookup"><span data-stu-id="30966-121">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `http://localhost:5000/Movies/ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="30966-122">Önceki rota kısıtlaması, bir sorgu dizesi değeri olarak rota verilerini (URL kesimi) yerine unvanını arama sağlar.</span><span class="sxs-lookup"><span data-stu-id="30966-122">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="30966-123">`?` İçinde `"{searchString?}"` bu bir isteğe bağlı bir rota parametresini anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="30966-123">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Url ve iki filmler, Ghostbusters ve Ghostbusters 2 döndürülen film listesi eklenen word hayalet dizin görünümünün](../../tutorials/razor-pages/search/_static/g2.png)

<span data-ttu-id="30966-125">Ancak, bir filmi için aranacak URL'sini değiştirmek için kullanıcıların beklediğiniz olamaz.</span><span class="sxs-lookup"><span data-stu-id="30966-125">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="30966-126">Bu adımda, filmler filtrelemek için kullanıcı Arabirimi eklenir.</span><span class="sxs-lookup"><span data-stu-id="30966-126">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="30966-127">Rota kısıtlaması eklediyseniz `"{searchString?}"`, kaldırın.</span><span class="sxs-lookup"><span data-stu-id="30966-127">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="30966-128">Açık *Pages/Movies/Index.cshtml* dosya ve ekleme `<form>` aşağıdaki kodda vurgulanan biçimlendirme:</span><span class="sxs-lookup"><span data-stu-id="30966-128">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="30966-129">HTML `<form>` etiketi kullanır [Form etiketi yardımcı](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="30966-129">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="30966-130">Form gönderildiğinde, filtre dizesi gönderilen *filmler/sayfalar/dizin* sayfası.</span><span class="sxs-lookup"><span data-stu-id="30966-130">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page.</span></span> <span data-ttu-id="30966-131">Değişiklikleri kaydetmek ve filtre sınayın.</span><span class="sxs-lookup"><span data-stu-id="30966-131">Save the changes and test the filter.</span></span>

![Word hayalet başlığı filtre metin kutusuna yazdığınız dizin görünümünün](../../tutorials/razor-pages/search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="30966-133">Türe göre ara</span><span class="sxs-lookup"><span data-stu-id="30966-133">Search by genre</span></span>

<span data-ttu-id="30966-134">Aşağıdaki vurgulanan özellikleri ekleyin *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="30966-134">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

<span data-ttu-id="30966-135">`SelectList Genres` Türler listesini içerir.</span><span class="sxs-lookup"><span data-stu-id="30966-135">The `SelectList Genres` contains the list of genres.</span></span> <span data-ttu-id="30966-136">Bu kullanıcının listeden bir tarzını seçmesine olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="30966-136">This allows the user to select a genre from the list.</span></span>

<span data-ttu-id="30966-137">`MovieGenre` Özelliği, kullanıcı seçer (örneğin, "Batı") belirli bir tarzını içerir.</span><span class="sxs-lookup"><span data-stu-id="30966-137">The `MovieGenre` property contains the specific genre the user selects (for example, "Western").</span></span>

<span data-ttu-id="30966-138">Güncelleştirme `OnGetAsync` aşağıdaki kod ile yöntemi:</span><span class="sxs-lookup"><span data-stu-id="30966-138">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="30966-139">Tüm türler veritabanından alır bir LINQ Sorgu kodudur.</span><span class="sxs-lookup"><span data-stu-id="30966-139">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="30966-140">`SelectList` Türler farklı türler yansıtma tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="30966-140">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

<!-- BUG in OPS
Tag snippet_selectlist's start line '75' should be less than end line '29' when resolving "[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]"

There's no start line.

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]
-->

```csharp
Genres = new SelectList(await genreQuery.Distinct().ToListAsync());
```

### <a name="adding-search-by-genre"></a><span data-ttu-id="30966-141">Türe göre arama ekleme</span><span class="sxs-lookup"><span data-stu-id="30966-141">Adding search by genre</span></span>

<span data-ttu-id="30966-142">Güncelleştirme *Index.cshtml* gibi:</span><span class="sxs-lookup"><span data-stu-id="30966-142">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="30966-143">Genre, filmi ve her ikisi tarafından arayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="30966-143">Test the app by searching by genre, by movie title, and by both.</span></span>
