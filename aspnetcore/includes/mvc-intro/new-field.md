# <a name="adding-a-new-field"></a><span data-ttu-id="87d2b-101">Yeni bir alan ekleme</span><span class="sxs-lookup"><span data-stu-id="87d2b-101">Adding a new field</span></span>

<span data-ttu-id="87d2b-102">tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="87d2b-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="87d2b-103">Bu öğretici için yeni bir alan ekler `Movies` tablo.</span><span class="sxs-lookup"><span data-stu-id="87d2b-103">This tutorial will add a new field to the `Movies` table.</span></span> <span data-ttu-id="87d2b-104">Biz veritabanını bırakıp biz şema değiştirdiğinizde, yeni bir tane oluşturun (yeni bir alan ekleyin).</span><span class="sxs-lookup"><span data-stu-id="87d2b-104">We'll drop the database and create a new one when we change the schema (add a new field).</span></span> <span data-ttu-id="87d2b-105">Biz perserve için herhangi bir üretim veri bulunmadığında bu iş akışı da erken geliştirme çalışır.</span><span class="sxs-lookup"><span data-stu-id="87d2b-105">This workflow works well early in development when we don't have any production data to perserve.</span></span>

<span data-ttu-id="87d2b-106">Şema değiştirmeniz gerektiğinde, uygulamanın dağıtıldığı ve perserve için gereksinim duyduğunuz verilerine sahip sonra DB bırakılamıyor.</span><span class="sxs-lookup"><span data-stu-id="87d2b-106">Once your app is deployed and you have data that you need to perserve, you can't drop your DB when you need to change the schema.</span></span> <span data-ttu-id="87d2b-107">Entity Framework [Code First Migrations](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) şemanızı güncelleştirmek ve verileri kaybetmeden veritabanını geçirme sağlar.</span><span class="sxs-lookup"><span data-stu-id="87d2b-107">Entity Framework [Code First Migrations](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) allows you to update your schema and migrate the database without losing data.</span></span> <span data-ttu-id="87d2b-108">Geçişler bir özelliktir popüler birçok geçiş şema işlemleri, SQL Server, ancak SQLlite kullanarak desteklemediğinde şekilde yalnızca çok basitçe geçişler mümkündür.</span><span class="sxs-lookup"><span data-stu-id="87d2b-108">Migrations is a popular feature when using SQL Server, but SQLlite doesn't support many migration schema operations, so only very simply migrations are possible.</span></span> <span data-ttu-id="87d2b-109">Bkz: [SQLite sınırlamalar](https://docs.microsoft.com/ef/core/providers/sqlite/limitations) daha fazla bilgi için.</span><span class="sxs-lookup"><span data-stu-id="87d2b-109">See [SQLite Limitations](https://docs.microsoft.com/ef/core/providers/sqlite/limitations) for more information.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="87d2b-110">Film modeline derecelendirme özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="87d2b-110">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="87d2b-111">Açık *Models/Movie.cs* dosya ve ekleme bir `Rating` özelliği:</span><span class="sxs-lookup"><span data-stu-id="87d2b-111">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

<span data-ttu-id="87d2b-112">Yeni bir alan eklediğiniz çünkü `Movie` sınıfı, siz de bu yeni özelliği eklenecek şekilde bağlama beyaz liste güncellemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="87d2b-112">Because you've added a new field to the `Movie` class, you also need to update the binding whitelist so this new property will be included.</span></span> <span data-ttu-id="87d2b-113">İçinde *MoviesController.cs*, güncelleştirme `[Bind]` özniteliği her ikisi için de `Create` ve `Edit` dahil etmek için eylem yöntemleri `Rating` özelliği:</span><span class="sxs-lookup"><span data-stu-id="87d2b-113">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="87d2b-114">Aynı zamanda görüntülemek için görünümü şablonlarını güncelleştirmek için oluşturmak ve yeni düzenlemek `Rating` tarayıcı görünümünde özelliği.</span><span class="sxs-lookup"><span data-stu-id="87d2b-114">You also need to update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="87d2b-115">Düzen */Views/Movies/Index.cshtml* dosya ve ekleme bir `Rating` alan:</span><span class="sxs-lookup"><span data-stu-id="87d2b-115">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]

<span data-ttu-id="87d2b-116">Güncelleştirme */Views/Movies/Create.cshtml* ile bir `Rating` alan.</span><span class="sxs-lookup"><span data-stu-id="87d2b-116">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

<span data-ttu-id="87d2b-117">Uygulama, yeni alanı içerecek şekilde DB güncelleştiriyoruz kadar çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="87d2b-117">The app won't work until we update the DB to include the new field.</span></span> <span data-ttu-id="87d2b-118">Şimdi çalıştırırsanız, aşağıdaki elde edersiniz `SqliteException`:</span><span class="sxs-lookup"><span data-stu-id="87d2b-118">If you run it now, you'll get the following `SqliteException`:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: m.Rating'.
```

<span data-ttu-id="87d2b-119">Güncelleştirilmiş film model sınıfı var olan veritabanının film tablonun şeması farklı olduğundan bu hata görüyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="87d2b-119">You're seeing this error because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="87d2b-120">(Var. hiçbir `Rating` veritabanı tablosundaki sütun.)</span><span class="sxs-lookup"><span data-stu-id="87d2b-120">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="87d2b-121">Hatayı çözümlemek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="87d2b-121">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="87d2b-122">Veritabanını bırakın ve yeni model sınıfı şemasını temel alan veritabanı otomatik olarak yeniden oluşturma Entity Framework sahip.</span><span class="sxs-lookup"><span data-stu-id="87d2b-122">Drop the database and have the Entity Framework automatically re-create the database based on the new model class schema.</span></span> <span data-ttu-id="87d2b-123">Bu yaklaşımda, veritabanında var olan veri kaybı — bir üretim veritabanıyla yapamadığı şekilde!</span><span class="sxs-lookup"><span data-stu-id="87d2b-123">With this approach, you lose existing data in the database — so you can't do this with a production database!</span></span> <span data-ttu-id="87d2b-124">Otomatik olarak test verileri olan bir veritabanı oluşturmak için bir başlatıcı kullanarak bir uygulama geliştirmek için verimli bir şekilde görülür.</span><span class="sxs-lookup"><span data-stu-id="87d2b-124">Using an initializer to automatically seed a database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="87d2b-125">El ile modeli sınıfları eşleşecek şekilde var olan veritabanının şema değiştirin.</span><span class="sxs-lookup"><span data-stu-id="87d2b-125">Manually modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="87d2b-126">Bu yaklaşımın avantajı, verilerinizi tutmak ' dir.</span><span class="sxs-lookup"><span data-stu-id="87d2b-126">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="87d2b-127">Bu değişikliği yapmak ya da el ile veya komut dosyası bir veritabanı oluşturarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="87d2b-127">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="87d2b-128">Veritabanı şeması güncelleştirmek için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="87d2b-128">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="87d2b-129">Bu öğreticide, biz bırakın ve şema değiştiğinde veritabanını yeniden oluşturmanız.</span><span class="sxs-lookup"><span data-stu-id="87d2b-129">For this tutorial, we'll drop and re-create the database when the schema changes.</span></span> <span data-ttu-id="87d2b-130">Terminal durumundan db bırakmak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="87d2b-130">Run the following command from a terminal to drop the db:</span></span>

`dotnet ef database drop`

<span data-ttu-id="87d2b-131">Güncelleştirme `SeedData` böylece yeni bir sütun için bir değer sağlayan sınıf.</span><span class="sxs-lookup"><span data-stu-id="87d2b-131">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="87d2b-132">Bir örnek değişiklik aşağıda gösterilen, ancak her biri için bu değişikliği yapmak istersiniz `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="87d2b-132">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="87d2b-133">Ekleme `Rating` alanı `Edit`, `Details`, ve `Delete` görünümü.</span><span class="sxs-lookup"><span data-stu-id="87d2b-133">Add the `Rating` field to the `Edit`, `Details`, and `Delete` view.</span></span>

<span data-ttu-id="87d2b-134">Uygulamayı çalıştırın ve oluşturma/düzenleme/görüntüleme filmler ile yapabilecekleriniz doğrulayın bir `Rating` alan.</span><span class="sxs-lookup"><span data-stu-id="87d2b-134">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="87d2b-135">Şablonlar.</span><span class="sxs-lookup"><span data-stu-id="87d2b-135">templates.</span></span>
