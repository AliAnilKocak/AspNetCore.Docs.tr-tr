---
title: 8. bölüm, ASP.NET Core MVC uygulamasına yeni bir alan ekleyin
author: rick-anderson
description: ASP.NET Core MVC 'de öğretici serisinin 8. bölümü.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 40e615d0698a0ed1d3ef40a222e064d72184f0c8
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635300"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="e2548-103">8. bölüm, ASP.NET Core MVC uygulamasına yeni bir alan ekleyin</span><span class="sxs-lookup"><span data-stu-id="e2548-103">Part 8, add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="e2548-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e2548-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e2548-105">Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:</span><span class="sxs-lookup"><span data-stu-id="e2548-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="e2548-106">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e2548-106">Add a new field to the model.</span></span>
* <span data-ttu-id="e2548-107">Yeni alanı veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="e2548-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="e2548-108">EF Code First otomatik olarak bir veritabanı oluşturmak için kullanıldığında, Code First:</span><span class="sxs-lookup"><span data-stu-id="e2548-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="e2548-109">Veritabanının şemasını izlemek için veritabanına tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="e2548-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="e2548-110">Veritabanının oluşturulduğu model sınıflarıyla eşitlenmiş olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="e2548-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="e2548-111">Bunlar eşitlenmiş değilse EF bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e2548-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="e2548-112">Bu, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="e2548-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="e2548-113">Film modeline bir derecelendirme özelliği ekleyin</span><span class="sxs-lookup"><span data-stu-id="e2548-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="e2548-114">`Rating` *Modeller/film. cs*'ye bir özellik ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e2548-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="e2548-115">Uygulamayı oluşturma</span><span class="sxs-lookup"><span data-stu-id="e2548-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="e2548-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2548-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="e2548-117">Ctrl+Shift+B</span><span class="sxs-lookup"><span data-stu-id="e2548-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="e2548-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2548-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2548-119">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2548-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2548-120">Komut ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="e2548-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="e2548-121">Sınıfa yeni bir alan eklediyseniz `Movie` , bu yeni özelliğin dahil edilmesini sağlamak için bağlama beyaz listesini güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="e2548-121">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="e2548-122">*MoviesController.cs*içinde, `[Bind]` `Create` `Edit` özelliği dahil etmek için hem hem de eylem yöntemlerinin özniteliğini güncelleştirin `Rating` :</span><span class="sxs-lookup"><span data-stu-id="e2548-122">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="e2548-123">Yeni özelliği tarayıcı görünümünde görüntülemek, oluşturmak ve düzenlemek için görünüm şablonlarını güncelleştirin `Rating` .</span><span class="sxs-lookup"><span data-stu-id="e2548-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="e2548-124">*/Views/movies/Index.cshtml* dosyasını düzenleyin ve bir alan ekleyin `Rating` :</span><span class="sxs-lookup"><span data-stu-id="e2548-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="e2548-125">*/Views/movies/Create.cshtml* ile bir alanı güncelleştirin `Rating` .</span><span class="sxs-lookup"><span data-stu-id="e2548-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="e2548-126">Visual Studio/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2548-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="e2548-127">Önceki "form grubunu kopyalayabilir/yapıştırabilir" ve IntelliSense 'in alanları güncelleştirmenize yardımcı olmasına izin verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e2548-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="e2548-128">IntelliSense, [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)ile birlikte çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e2548-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Geliştirici, görünümün ikinci Label öğesinde için ASP-for öznitelik değeri için R harfini yazmıştır.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2548-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2548-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="e2548-133">Kalan şablonları güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="e2548-133">Update the remaining templates.</span></span>

<span data-ttu-id="e2548-134">`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="e2548-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="e2548-135">Aşağıda örnek bir değişiklik gösterilmektedir, ancak her biri için bu değişikliği yapmak isteyeceksiniz `new Movie` .</span><span class="sxs-lookup"><span data-stu-id="e2548-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="e2548-136">VERITABANı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="e2548-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="e2548-137">Şimdi çalıştırıldığında, şunlar `SqlException` oluşur:</span><span class="sxs-lookup"><span data-stu-id="e2548-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="e2548-138">Bu hata, güncelleştirilmiş film modeli sınıfı varolan veritabanının film tablosunun şemasından farklı olduğu için oluşur.</span><span class="sxs-lookup"><span data-stu-id="e2548-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="e2548-139">( `Rating` Veritabanı tablosunda sütun yok.)</span><span class="sxs-lookup"><span data-stu-id="e2548-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="e2548-140">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="e2548-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="e2548-141">Entity Framework yeni model sınıfı şemasına göre otomatik olarak veritabanını bırakıp yeniden oluşturmayı sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e2548-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="e2548-142">Bu yaklaşım, bir test veritabanı üzerinde etkin geliştirme yaparken geliştirme döngüsünün başlarında çok daha kolay. modeli ve veritabanı şemasını birlikte hızla gelişmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="e2548-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="e2548-143">Bunun yanında, bu yaklaşımı bir üretim veritabanında kullanmak istemezsiniz, ancak bu, veritabanında var olan verileri kaybetmeniz olur.</span><span class="sxs-lookup"><span data-stu-id="e2548-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="e2548-144">Bir veritabanının test verileriyle otomatik olarak çekirdeği oluşturmak için bir başlatıcı kullanılması, genellikle bir uygulama geliştirmenin üretken bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="e2548-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="e2548-145">Bu, erken geliştirme ve SQLite kullanılırken iyi bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="e2548-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="e2548-146">Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e2548-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="e2548-147">Bu yaklaşımın avantajı, verilerinizi tutmanızı kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="e2548-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="e2548-148">Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e2548-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="e2548-149">Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="e2548-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="e2548-150">Bu öğretici için Code First Migrations kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e2548-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2548-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2548-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2548-152">**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="e2548-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC menüsü](adding-model/_static/pmc.png)

<span data-ttu-id="e2548-154">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="e2548-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="e2548-155">Bu `Add-Migration` komut, geçiş çerçevesinin geçerli `Movie` DB şemasıyla geçerli modeli INCELEMESINI `Movie` ve veritabanını yeni modele geçirmek için gerekli kodu oluşturmasını söyler.</span><span class="sxs-lookup"><span data-stu-id="e2548-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="e2548-156">"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e2548-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="e2548-157">Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="e2548-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="e2548-158">VERITABANıNDAKI tüm kayıtlar silinirse, Initialize yöntemi VERITABANıNı temel alır ve `Rating` alanını içerir.</span><span class="sxs-lookup"><span data-stu-id="e2548-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e2548-159">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2548-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="e2548-160">Veritabanını silin ve geçişleri kullanarak veritabanını yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e2548-160">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="e2548-161">Veritabanını silmek için veritabanı dosyasını (*Mvcmovie. db*) silin.</span><span class="sxs-lookup"><span data-stu-id="e2548-161">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="e2548-162">Ardından şu `ef database update` komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e2548-162">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

<span data-ttu-id="e2548-163">Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz, düzenleyebileceğiniz ve görüntüleyebilen bir doğrulama yapabilirsiniz `Rating` .</span><span class="sxs-lookup"><span data-stu-id="e2548-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e2548-164">[Önceki](search.md) 
>  [Sonraki](validation.md)</span><span class="sxs-lookup"><span data-stu-id="e2548-164">[Previous](search.md)
[Next](validation.md)</span></span>
