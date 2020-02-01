---
title: ASP.NET Core MVC uygulamasına model ekleme
author: rick-anderson
description: Basit bir ASP.NET Core uygulamasına model ekleyin.
ms.author: riande
ms.date: 01/13/2020
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 2b656f316a25759456a1d00a9176ea160b2fe5bf
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928584"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="935f2-103">ASP.NET Core MVC uygulamasına model ekleme</span><span class="sxs-lookup"><span data-stu-id="935f2-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="935f2-104">[Rick Anderson](https://twitter.com/RickAndMSFT) ve [Tom Dykstra](https://github.com/tdykstra) tarafından</span><span class="sxs-lookup"><span data-stu-id="935f2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="935f2-105">Bu bölümde, bir veritabanında film yönetmeye yönelik sınıflar eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="935f2-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="935f2-106">Bu sınıflar, **d**VC uygulamasının "**d**odel" parçası olacaktır.</span><span class="sxs-lookup"><span data-stu-id="935f2-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="935f2-107">Bu sınıfları bir veritabanıyla çalışmak için [Entity Framework Core](/ef/core) (EF Core) ile birlikte kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="935f2-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="935f2-108">EF Core, yazmanız gereken veri erişim kodunu kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="935f2-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="935f2-109">Oluşturduğunuz model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları olarak bilinir ( **P**Lain **O**).</span><span class="sxs-lookup"><span data-stu-id="935f2-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="935f2-110">Yalnızca veritabanında depolanacak verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="935f2-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="935f2-111">Bu öğreticide, önce model sınıflarını yazdığınızda EF Core veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="935f2-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="935f2-112">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="935f2-112">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="935f2-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="935f2-114"> > **sınıf** **eklemek** > *modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="935f2-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="935f2-115">Dosyayı *Movie.cs*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="935f2-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="935f2-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="935f2-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="935f2-117">*Modeller* klasörüne *Movie.cs* adlı bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="935f2-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="935f2-118">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="935f2-119">*Modeller* klasörüne sağ tıklayın > **yeni > Class** > **boş sınıf** **ekleyin** .</span><span class="sxs-lookup"><span data-stu-id="935f2-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="935f2-120">Dosyayı *Movie.cs*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="935f2-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="935f2-121">*Movie.cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="935f2-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="935f2-122">`Movie` sınıfı, birincil anahtar için veritabanı için gerekli olan bir `Id` alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="935f2-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="935f2-123">`ReleaseDate` [veri türü özniteliği,](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) verilerin türünü belirtir (`Date`).</span><span class="sxs-lookup"><span data-stu-id="935f2-123">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="935f2-124">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="935f2-124">With this attribute:</span></span>

* <span data-ttu-id="935f2-125">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="935f2-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="935f2-126">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="935f2-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="935f2-127">[Veri açıklamaları](/dotnet/api/system.componentmodel.dataannotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="935f2-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="935f2-128">NuGet paketleri Ekle</span><span class="sxs-lookup"><span data-stu-id="935f2-128">Add NuGet packages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="935f2-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="935f2-130">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="935f2-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="935f2-132">PMC 'de şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="935f2-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="935f2-133">Yukarıdaki komut, EF Core SQL Server sağlayıcısını ekler.</span><span class="sxs-lookup"><span data-stu-id="935f2-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="935f2-134">Sağlayıcı paketi, EF Core paketini bir bağımlılık olarak yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="935f2-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="935f2-135">Ek paketler, öğreticinin sonraki bölümlerinde bulunan yapı iskelesi adımında otomatik olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="935f2-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="935f2-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="935f2-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="935f2-137">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="935f2-138">**Proje** menüsünde, **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="935f2-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="935f2-139">Sağ üst köşedeki **arama** alanına `Microsoft.EntityFrameworkCore.SQLite` girin ve aramak için **dönüş** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="935f2-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="935f2-140">Eşleşen NuGet paketini seçin ve **paket Ekle** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="935f2-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet paketi Ekle](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="935f2-142">Proje **Seç** iletişim kutusu, `MvcMovie` projesi seçili olacak şekilde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="935f2-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="935f2-143">**Tamam** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="935f2-143">Press the **Ok** button.</span></span>

<span data-ttu-id="935f2-144">Bir **Lisans kabul** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="935f2-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="935f2-145">Lisansları istenen şekilde gözden geçirin ve ardından **kabul et** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="935f2-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="935f2-146">Aşağıdaki NuGet paketlerini yüklemek için yukarıdaki adımları yineleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="935f2-147">Veritabanı bağlamı sınıfı oluşturma</span><span class="sxs-lookup"><span data-stu-id="935f2-147">Create a database context class</span></span>

<span data-ttu-id="935f2-148">`Movie` modeli için EF Core işlevselliği (oluşturma, okuma, güncelleştirme, silme) koordine etmek için bir veritabanı bağlamı sınıfı gerekir.</span><span class="sxs-lookup"><span data-stu-id="935f2-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="935f2-149">Veritabanı bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) öğesinden türetilir ve veri modeline dahil edilecek varlıkları belirtir.</span><span class="sxs-lookup"><span data-stu-id="935f2-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="935f2-150">Bir *veri* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="935f2-150">Create a *Data* folder.</span></span>

<span data-ttu-id="935f2-151">Aşağıdaki kodla bir *Data/MvcMovieContext. cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="935f2-152">Önceki kod, varlık kümesi için bir [Dbset\<filmi >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="935f2-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="935f2-153">Entity Framework terminolojisinde, bir varlık kümesini genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="935f2-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="935f2-154">Bir varlık tablosunda bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="935f2-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="935f2-155">Veritabanı bağlamı Kaydet</span><span class="sxs-lookup"><span data-stu-id="935f2-155">Register the database context</span></span>

<span data-ttu-id="935f2-156">ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="935f2-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="935f2-157">Hizmetlerin (EF Core DB bağlamı gibi) uygulama başlatma sırasında DI ile kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="935f2-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="935f2-158">Bu hizmetler (örneğin, Razor sayfaları) gerektiren bileşenler bu hizmetler Oluşturucu parametresi üzerinden sağlanır.</span><span class="sxs-lookup"><span data-stu-id="935f2-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="935f2-159">Bir DB bağlamı örneği alır Oluşturucu kodu öğreticinin ilerleyen bölümlerinde gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="935f2-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="935f2-160">Bu bölümde, veritabanı bağlamını dı kapsayıcısına kaydedersiniz.</span><span class="sxs-lookup"><span data-stu-id="935f2-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="935f2-161">Aşağıdaki `using` deyimlerini *Startup.cs*üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-161">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="935f2-162">Aşağıdaki Vurgulanan kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="935f2-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="935f2-164">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="935f2-165">Bağlantı dizesi adı için bağlam üzerinde bir yöntemi çağırarak geçirilen bir [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesne.</span><span class="sxs-lookup"><span data-stu-id="935f2-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="935f2-166">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) bağlantı dizesinden okur *appsettings.json* dosya.</span><span class="sxs-lookup"><span data-stu-id="935f2-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="935f2-167">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="935f2-167">Add a database connection string</span></span>

<span data-ttu-id="935f2-168">*AppSettings. JSON* dosyasına bir bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="935f2-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="935f2-170">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="935f2-171">Projeyi derleyici hatalarına yönelik bir denetim olarak derleyin.</span><span class="sxs-lookup"><span data-stu-id="935f2-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="935f2-172">Yapı iskelesi film sayfaları</span><span class="sxs-lookup"><span data-stu-id="935f2-172">Scaffold movie pages</span></span>

<span data-ttu-id="935f2-173">Film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) sayfaları üretmek için scafkatlama aracını kullanın.</span><span class="sxs-lookup"><span data-stu-id="935f2-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="935f2-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="935f2-175">**Çözüm Gezgini**, *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi > öğesi ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="935f2-175">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="935f2-177">**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="935f2-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="935f2-179">**Denetleyici Ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="935f2-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="935f2-180">**Model sınıfı:** *Film (mvcmovie. modeller)*</span><span class="sxs-lookup"><span data-stu-id="935f2-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="935f2-181">**Veri bağlamı sınıfı:** *mvcmoviecontext (mvcmovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="935f2-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Veri bağlamı Ekle](adding-model/_static/dc3.png)

* <span data-ttu-id="935f2-183">**Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut</span><span class="sxs-lookup"><span data-stu-id="935f2-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="935f2-184">**Denetleyici adı:** Varsayılan *MoviesController* tut</span><span class="sxs-lookup"><span data-stu-id="935f2-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="935f2-185">**Ekle** 'yi seçin</span><span class="sxs-lookup"><span data-stu-id="935f2-185">Select **Add**</span></span>

<span data-ttu-id="935f2-186">Visual Studio şunları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="935f2-186">Visual Studio creates:</span></span>

* <span data-ttu-id="935f2-187">Bir filmler denetleyicisi (*denetleyiciler/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="935f2-187">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="935f2-188">Razor oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme (*Görünümler/filmler/\*. cshtml*)</span><span class="sxs-lookup"><span data-stu-id="935f2-188">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="935f2-189">Bu dosyaların otomatik olarak oluşturulması, *Yapı iskelesi*olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="935f2-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="935f2-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="935f2-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="935f2-191">Proje dizininde bir komut penceresi açın (içeren dizine *Program.cs*, *Startup.cs*, ve *.csproj* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="935f2-191">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="935f2-192">Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="935f2-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="935f2-193">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="935f2-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="935f2-194">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="935f2-195">Proje dizininde bir komut penceresi açın (içeren dizine *Program.cs*, *Startup.cs*, ve *.csproj* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="935f2-195">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="935f2-196">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="935f2-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="935f2-197">Veritabanı mevcut olmadığından, scafkatmış sayfaları henüz kullanamazsınız.</span><span class="sxs-lookup"><span data-stu-id="935f2-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="935f2-198">Uygulamayı çalıştırır ve **film uygulaması** bağlantısına tıklarsanız, bir *veritabanı* açılamıyor veya *böyle bir tablo yok: film* hata iletisi.</span><span class="sxs-lookup"><span data-stu-id="935f2-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="935f2-199">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="935f2-199">Initial migration</span></span>

<span data-ttu-id="935f2-200">Veritabanını oluşturmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="935f2-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="935f2-201">Geçişler, veri modelinizle eşleşecek bir veritabanı oluşturmanıza ve güncelleştirmenize olanak sağlayan bir araç kümesidir.</span><span class="sxs-lookup"><span data-stu-id="935f2-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="935f2-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="935f2-203">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="935f2-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="935f2-204">PMC'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="935f2-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="935f2-205">`Add-Migration InitialCreate`: bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="935f2-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="935f2-206">`InitialCreate` bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="935f2-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="935f2-207">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="935f2-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="935f2-208">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="935f2-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="935f2-209">Veritabanı şeması, `MvcMovieContext` sınıfında belirtilen modeli temel alır.</span><span class="sxs-lookup"><span data-stu-id="935f2-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="935f2-210">`Update-Database`: veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="935f2-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="935f2-211">Bu komut, veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında `Up` yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="935f2-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="935f2-212">Database Update komutu aşağıdaki uyarıyı üretir:</span><span class="sxs-lookup"><span data-stu-id="935f2-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="935f2-213">' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="935f2-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="935f2-214">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="935f2-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="935f2-215">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açıkça belirtin.</span><span class="sxs-lookup"><span data-stu-id="935f2-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="935f2-216">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="935f2-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="935f2-217">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="935f2-218">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="935f2-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="935f2-219">`ef migrations add InitialCreate`: bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturuyor.</span><span class="sxs-lookup"><span data-stu-id="935f2-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="935f2-220">`InitialCreate` bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="935f2-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="935f2-221">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="935f2-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="935f2-222">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="935f2-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="935f2-223">Veritabanı şeması, `MvcMovieContext` sınıfında belirtilen modeli temel alır ( *Data/MvcMovieContext. cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="935f2-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="935f2-224">`ef database update`: veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="935f2-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="935f2-225">Bu komut, veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında `Up` yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="935f2-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="935f2-226">Initialcreate sınıfı</span><span class="sxs-lookup"><span data-stu-id="935f2-226">The InitialCreate class</span></span>

<span data-ttu-id="935f2-227">*Geçişleri/{timestamp} _InitialCreate. cs* geçiş dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="935f2-228">`Up` yöntemi, film tablosunu oluşturur ve `Id` birincil anahtar olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="935f2-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="935f2-229">`Down` yöntemi, `Up` geçişi tarafından yapılan şema değişikliklerini geri alır.</span><span class="sxs-lookup"><span data-stu-id="935f2-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="935f2-230">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="935f2-230">Test the app</span></span>

* <span data-ttu-id="935f2-231">Uygulamayı çalıştırın ve **film uygulaması** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="935f2-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="935f2-232">Aşağıdakilerden birine benzer bir özel durum alırsanız:</span><span class="sxs-lookup"><span data-stu-id="935f2-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="935f2-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="935f2-234">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="935f2-235">Muhtemelen [geçişler adımını](#migration)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="935f2-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="935f2-236">**Oluştur** sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="935f2-236">Test the **Create** page.</span></span> <span data-ttu-id="935f2-237">Veri girin ve gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="935f2-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="935f2-238">Ondalık virgül kullanımı girmeniz mümkün olmayabilir `Price` alan.</span><span class="sxs-lookup"><span data-stu-id="935f2-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="935f2-239">Desteklemek için [jQuery doğrulama](https://jqueryvalidation.org/) virgül İngilizce olmayan yerel (",") bir ondalık noktasının ve ABD İngilizce olmayan tarih biçimleri, uygulamayı Eğer gerekir.</span><span class="sxs-lookup"><span data-stu-id="935f2-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="935f2-240">Genelleştirme hakkında yönergeler için bkz. [bu GitHub sorunu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="935f2-240">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="935f2-241">**Düzenleme**, **Ayrıntılar**ve **silme** sayfalarını test edin.</span><span class="sxs-lookup"><span data-stu-id="935f2-241">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="935f2-242">Denetleyiciye bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="935f2-242">Dependency injection in the controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="935f2-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="935f2-244">*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="935f2-245">Oluşturucu, veritabanı bağlamını (`MvcMovieContext`) denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="935f2-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="935f2-246">Her bir veritabanı bağlamı kullanılan [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) denetleyici yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="935f2-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="935f2-247">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="935f2-248">Oluşturucu, veritabanı bağlamını (`MvcMovieContext`) denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="935f2-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="935f2-249">Her bir veritabanı bağlamı kullanılan [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) denetleyici yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="935f2-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="935f2-250">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="935f2-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="935f2-251">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="935f2-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="935f2-252">Aşağıdaki kod, <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> başlangıca nasıl ekleneceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="935f2-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="935f2-253">`IWebHostEnvironment`, geliştirme ve üretimde SQL Server `ConfigureServices` SQLite kullanabilmesi için eklenir.</span><span class="sxs-lookup"><span data-stu-id="935f2-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="935f2-254">Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="935f2-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="935f2-255">Bu öğreticide daha önce, bir denetleyicinin `ViewData` sözlüğünü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz.</span><span class="sxs-lookup"><span data-stu-id="935f2-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="935f2-256">`ViewData` sözlüğü bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="935f2-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="935f2-257">MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="935f2-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="935f2-258">Bu kesin türü belirtilmiş yaklaşım derleme zamanı kodu denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="935f2-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="935f2-259">Yapı iskelesi mekanizması, `MoviesController` sınıfı ve görünümleriyle bu yaklaşımı (türü kesin belirlenmiş bir modeli geçirerek) kullandı.</span><span class="sxs-lookup"><span data-stu-id="935f2-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="935f2-260">*Controllers/MoviesController. cs* dosyasında oluşturulan `Details` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="935f2-261">`id` parametresi genellikle rota verileri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="935f2-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="935f2-262">Örneğin `https://localhost:5001/movies/details/1` kümeler:</span><span class="sxs-lookup"><span data-stu-id="935f2-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="935f2-263">`movies` denetleyicisine denetleyici (ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="935f2-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="935f2-264">`details` eylemi (ikinci URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="935f2-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="935f2-265">Kimliği 1 ' e (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="935f2-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="935f2-266">Aşağıdaki gibi bir sorgu dizesiyle `id` de geçirebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="935f2-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="935f2-267">KIMLIK değeri sağlanmazsa `id` parametresi [null yapılabilir bir tür](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="935f2-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="935f2-268">Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) , rota verileriyle veya sorgu dizesi değeriyle eşleşen film varlıklarını seçmek üzere `FirstOrDefaultAsync` ' A geçirilir.</span><span class="sxs-lookup"><span data-stu-id="935f2-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="935f2-269">Bir film bulunursa, `Movie` modelinin bir örneği `Details` görünümüne geçirilir:</span><span class="sxs-lookup"><span data-stu-id="935f2-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="935f2-270">*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="935f2-271">Görünüm dosyasının en üstündeki `@model` ifade, görünümün beklediği nesne türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="935f2-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="935f2-272">Film denetleyicisi oluşturulduğunda, aşağıdaki `@model` deyimleri eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="935f2-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="935f2-273">Bu `@model` yönergesi, denetleyicinin görünüme geçirildiği filme erişimine izin verir.</span><span class="sxs-lookup"><span data-stu-id="935f2-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="935f2-274">`Model` nesne kesin olarak belirlenmiş.</span><span class="sxs-lookup"><span data-stu-id="935f2-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="935f2-275">Örneğin, *details. cshtml* görünümünde, kod her bir film alanını `DisplayNameFor` `DisplayFor` ve HTML yardımcılarını türü kesin belirlenmiş `Model` nesnesiyle geçirir.</span><span class="sxs-lookup"><span data-stu-id="935f2-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="935f2-276">`Create` ve `Edit` yöntemleri ve görünümleri bir `Movie` model nesnesi de iletir.</span><span class="sxs-lookup"><span data-stu-id="935f2-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="935f2-277">*Dizin. cshtml* görünümünü ve film denetleyicisindeki `Index` yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="935f2-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="935f2-278">`List`, `View` yöntemini çağırdığında kodun bir nesne nasıl oluşturduğunu fark edin.</span><span class="sxs-lookup"><span data-stu-id="935f2-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="935f2-279">Kod, `Index` eylem yönteminden bu `Movies` listesini görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="935f2-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="935f2-280">Film denetleyicisi oluşturulduğunda, yapı iskelesi *Index. cshtml* dosyasının en üstüne aşağıdaki `@model` ifadesini içeriyordu:</span><span class="sxs-lookup"><span data-stu-id="935f2-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="935f2-281">`@model` yönergesi, kesin olarak belirlenmiş bir `Model` nesnesi kullanarak, denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="935f2-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="935f2-282">Örneğin, *Index. cshtml* görünümünde, kod kesin türü belirtilmiş `Model` nesnesi üzerinde `foreach` bir ifadesiyle filmlerle döngü yapılır:</span><span class="sxs-lookup"><span data-stu-id="935f2-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="935f2-283">`Model` nesne kesin olarak yazıldığı için (bir `IEnumerable<Movie>` nesnesi olarak), döngüdeki her öğe `Movie`olarak yazılır.</span><span class="sxs-lookup"><span data-stu-id="935f2-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="935f2-284">Diğer avantajların yanı sıra, kodu derleme zaman denetimini alacağınız anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="935f2-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="935f2-285">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="935f2-285">Additional resources</span></span>

* [<span data-ttu-id="935f2-286">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="935f2-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="935f2-287">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="935f2-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="935f2-288">Daha [önce bir görünüm ekleme](adding-view.md)
> [daha sonra SQL ile çalışma](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="935f2-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="935f2-289">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="935f2-289">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="935f2-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="935f2-291"> > **sınıf** **eklemek** > *modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="935f2-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="935f2-292">Sınıf adı **film**.</span><span class="sxs-lookup"><span data-stu-id="935f2-292">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="935f2-293">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-293">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="935f2-294">Bir sınıfa eklemek *modelleri* adlı klasöre *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="935f2-294">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="935f2-295">Film modeli iskelesini</span><span class="sxs-lookup"><span data-stu-id="935f2-295">Scaffold the movie model</span></span>

<span data-ttu-id="935f2-296">Bu bölümde, film modeli iskele kurulmuş.</span><span class="sxs-lookup"><span data-stu-id="935f2-296">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="935f2-297">Diğer bir deyişle, yapı iskelesi aracı sayfaları için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerine yönelik film modeli oluşturur.</span><span class="sxs-lookup"><span data-stu-id="935f2-297">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="935f2-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-298">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="935f2-299">**Çözüm Gezgini**, *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi > öğesi ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="935f2-299">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="935f2-301">**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="935f2-301">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="935f2-303">**Denetleyici Ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="935f2-303">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="935f2-304">**Model sınıfı:** *Film (mvcmovie. modeller)*</span><span class="sxs-lookup"><span data-stu-id="935f2-304">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="935f2-305">**Veri bağlamı sınıfı:** **+** simgesini seçin ve varsayılan **Mvcmovie. modeller. MvcMovieContext** öğesini ekleyin</span><span class="sxs-lookup"><span data-stu-id="935f2-305">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Veri bağlamı Ekle](adding-model/_static/dc.png)

* <span data-ttu-id="935f2-307">**Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut</span><span class="sxs-lookup"><span data-stu-id="935f2-307">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="935f2-308">**Denetleyici adı:** Varsayılan *MoviesController* tut</span><span class="sxs-lookup"><span data-stu-id="935f2-308">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="935f2-309">**Ekle** 'yi seçin</span><span class="sxs-lookup"><span data-stu-id="935f2-309">Select **Add**</span></span>

![Denetleyici Ekle iletişim kutusu](adding-model/_static/add_controller2.png)

<span data-ttu-id="935f2-311">Visual Studio şunları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="935f2-311">Visual Studio creates:</span></span>

* <span data-ttu-id="935f2-312">Entity Framework Core [veritabanı bağlam sınıfı](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext. cs*)</span><span class="sxs-lookup"><span data-stu-id="935f2-312">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="935f2-313">Bir filmler denetleyicisi (*denetleyiciler/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="935f2-313">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="935f2-314">Razor oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme (*Görünümler/filmler/\*. cshtml*)</span><span class="sxs-lookup"><span data-stu-id="935f2-314">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="935f2-315">Veritabanı bağlamı ve [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (oluşturma, okuma, güncelleştirme ve silme) eylem yöntemlerinin ve görünümlerinin otomatik olarak oluşturulması, *Yapı iskelesi*olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="935f2-315">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="935f2-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="935f2-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="935f2-317">Proje dizininde bir komut penceresi açın (içeren dizine *Program.cs*, *Startup.cs*, ve *.csproj* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="935f2-317">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="935f2-318">Yapı iskelesi Aracı'nı yükleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-318">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="935f2-319">Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="935f2-319">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="935f2-320">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="935f2-320">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="935f2-321">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-321">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="935f2-322">Proje dizininde bir komut penceresi açın (içeren dizine *Program.cs*, *Startup.cs*, ve *.csproj* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="935f2-322">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="935f2-323">Yapı iskelesi Aracı'nı yükleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-323">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="935f2-324">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="935f2-324">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="935f2-325">Uygulamayı çalıştırır ve **MVC filmi** bağlantısına tıklarsanız aşağıdakine benzer bir hata alırsınız:</span><span class="sxs-lookup"><span data-stu-id="935f2-325">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="935f2-326">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-326">Visual Studio</span></span>](#tab/visual-studio)

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="935f2-327">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-327">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

``` error
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="935f2-328">Veritabanını oluşturmanız ve bunu yapmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="935f2-328">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="935f2-329">Geçişler veri modelinize uyan bir veritabanı oluşturmanıza ve veri modeliniz değiştiğinde veritabanı şemasını güncelleştirmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="935f2-329">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="935f2-330">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="935f2-330">Initial migration</span></span>

<span data-ttu-id="935f2-331">Bu bölümde, aşağıdaki görevler tamamlanır:</span><span class="sxs-lookup"><span data-stu-id="935f2-331">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="935f2-332">Bir başlangıç geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="935f2-332">Add an initial migration.</span></span>
* <span data-ttu-id="935f2-333">Veritabanı, ilk geçiş ile güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="935f2-333">Update the database with the initial migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="935f2-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-334">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="935f2-335">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="935f2-335">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="935f2-337">PMC'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="935f2-337">In the PMC, enter the following commands:</span></span>

   ```PMC
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="935f2-338">`Add-Migration` Komut, ilk veritabanı şeması oluşturmak için kod oluşturur.</span><span class="sxs-lookup"><span data-stu-id="935f2-338">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="935f2-339">Veritabanı şeması, `MvcMovieContext` sınıfında belirtilen modeli temel alır.</span><span class="sxs-lookup"><span data-stu-id="935f2-339">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="935f2-340">`Initial` bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="935f2-340">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="935f2-341">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="935f2-341">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="935f2-342">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="935f2-342">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="935f2-343">`Update-Database` Komutu çalıştırmaları `Up` yöntemi *geçişleri / {zaman damgası} _InitialCreate.cs* dosyasını veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="935f2-343">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="935f2-344">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="935f2-345">`ef migrations add InitialCreate` Komut, ilk veritabanı şeması oluşturmak için kod oluşturur.</span><span class="sxs-lookup"><span data-stu-id="935f2-345">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="935f2-346">Veritabanı şeması, `MvcMovieContext` sınıfında belirtilen modeli temel alır ( *Data/MvcMovieContext. cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="935f2-346">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="935f2-347">`InitialCreate` bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="935f2-347">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="935f2-348">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="935f2-348">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="935f2-349">Bağımlılık ekleme ile kayıtlı bağlamını İnceleme</span><span class="sxs-lookup"><span data-stu-id="935f2-349">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="935f2-350">ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="935f2-350">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="935f2-351">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="935f2-351">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="935f2-352">Bu hizmetler (örneğin, Razor sayfaları) gerektiren bileşenler bu hizmetler Oluşturucu parametresi üzerinden sağlanır.</span><span class="sxs-lookup"><span data-stu-id="935f2-352">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="935f2-353">Bir DB bağlamı örneği alır Oluşturucu kodu öğreticinin ilerleyen bölümlerinde gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="935f2-353">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="935f2-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="935f2-355">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup dı kapsayıcısına kaydetti.</span><span class="sxs-lookup"><span data-stu-id="935f2-355">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="935f2-356">Aşağıdaki `Startup.ConfigureServices` yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="935f2-356">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="935f2-357">Vurgulanan satırı iskele kurucu tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="935f2-357">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="935f2-358">`MvcMovieContext` EF Core işlevleri (oluşturma, okuma, güncelleştirme, silme, vb.) için koordinatları `Movie` modeli.</span><span class="sxs-lookup"><span data-stu-id="935f2-358">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="935f2-359">Veri bağlamı (`MvcMovieContext`) türetilir [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="935f2-359">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="935f2-360">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir:</span><span class="sxs-lookup"><span data-stu-id="935f2-360">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="935f2-361">Önceki kod, varlık kümesi için bir [Dbset\<filmi >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="935f2-361">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="935f2-362">Entity Framework terminolojisinde, bir varlık kümesini genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="935f2-362">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="935f2-363">Bir varlık tablosunda bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="935f2-363">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="935f2-364">Bağlantı dizesi adı için bağlam üzerinde bir yöntemi çağırarak geçirilen bir [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesne.</span><span class="sxs-lookup"><span data-stu-id="935f2-364">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="935f2-365">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) bağlantı dizesinden okur *appsettings.json* dosya.</span><span class="sxs-lookup"><span data-stu-id="935f2-365">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="935f2-366">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="935f2-366">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="935f2-367">Bir DB bağlamı oluşturdunuz ve bunu DI kapsayıcısı ile kaydettiniz.</span><span class="sxs-lookup"><span data-stu-id="935f2-367">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="935f2-368">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="935f2-368">Test the app</span></span>

* <span data-ttu-id="935f2-369">Uygulamayı çalıştırın ve ekleme `/Movies` tarayıcıda URL'sine (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="935f2-369">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="935f2-370">Aşağıdakine benzer bir veritabanı özel durumu alırsanız:</span><span class="sxs-lookup"><span data-stu-id="935f2-370">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="935f2-371">Eksik [geçişler adım](#pmc).</span><span class="sxs-lookup"><span data-stu-id="935f2-371">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="935f2-372">Test **Oluştur** bağlantı.</span><span class="sxs-lookup"><span data-stu-id="935f2-372">Test the **Create** link.</span></span> <span data-ttu-id="935f2-373">Veri girin ve gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="935f2-373">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="935f2-374">Ondalık virgül kullanımı girmeniz mümkün olmayabilir `Price` alan.</span><span class="sxs-lookup"><span data-stu-id="935f2-374">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="935f2-375">Desteklemek için [jQuery doğrulama](https://jqueryvalidation.org/) virgül İngilizce olmayan yerel (",") bir ondalık noktasının ve ABD İngilizce olmayan tarih biçimleri, uygulamayı Eğer gerekir.</span><span class="sxs-lookup"><span data-stu-id="935f2-375">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="935f2-376">Genelleştirme hakkında yönergeler için bkz. [bu GitHub sorunu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="935f2-376">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="935f2-377">Test **Düzenle**, **ayrıntıları**, ve **Sil** bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="935f2-377">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="935f2-378">`Startup` sınıfını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-378">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="935f2-379">Önceki vurgulanan kod, [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına eklenen film veritabanı bağlamını gösterir:</span><span class="sxs-lookup"><span data-stu-id="935f2-379">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="935f2-380">`services.AddDbContext<MvcMovieContext>(options =>` kullanılacak veritabanını ve bağlantı dizesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="935f2-380">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="935f2-381">`=>` [lambda operatörü](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="935f2-381">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="935f2-382">*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-382">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="935f2-383">Oluşturucu, veritabanı bağlamını (`MvcMovieContext`) denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="935f2-383">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="935f2-384">Her bir veritabanı bağlamı kullanılan [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) denetleyici yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="935f2-384">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="935f2-385">Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="935f2-385">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="935f2-386">Bu öğreticide daha önce, bir denetleyicinin `ViewData` sözlüğünü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz.</span><span class="sxs-lookup"><span data-stu-id="935f2-386">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="935f2-387">`ViewData` sözlüğü bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="935f2-387">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="935f2-388">MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="935f2-388">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="935f2-389">Bu kesin türü belirtilmiş yaklaşım, kodunuzun daha iyi derleme zaman denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="935f2-389">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="935f2-390">Yapı iskelesi mekanizması, yöntem ve görünümleri oluştururken `MoviesController` sınıfı ve görünümleriyle bu yaklaşımı (türü kesin belirlenmiş bir model geçirme) kullanır.</span><span class="sxs-lookup"><span data-stu-id="935f2-390">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="935f2-391">*Controllers/MoviesController. cs* dosyasında oluşturulan `Details` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-391">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="935f2-392">`id` parametresi genellikle rota verileri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="935f2-392">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="935f2-393">Örneğin `https://localhost:5001/movies/details/1` kümeler:</span><span class="sxs-lookup"><span data-stu-id="935f2-393">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="935f2-394">`movies` denetleyicisine denetleyici (ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="935f2-394">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="935f2-395">`details` eylemi (ikinci URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="935f2-395">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="935f2-396">Kimliği 1 ' e (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="935f2-396">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="935f2-397">Aşağıdaki gibi bir sorgu dizesiyle `id` de geçirebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="935f2-397">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="935f2-398">KIMLIK değeri sağlanmazsa `id` parametresi [null yapılabilir bir tür](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="935f2-398">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="935f2-399">Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) , rota verileriyle veya sorgu dizesi değeriyle eşleşen film varlıklarını seçmek üzere `FirstOrDefaultAsync` ' A geçirilir.</span><span class="sxs-lookup"><span data-stu-id="935f2-399">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="935f2-400">Bir film bulunursa, `Movie` modelinin bir örneği `Details` görünümüne geçirilir:</span><span class="sxs-lookup"><span data-stu-id="935f2-400">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="935f2-401">*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="935f2-401">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="935f2-402">Görünüm dosyasının üst kısmına bir `@model` ifadesini ekleyerek, görünümün beklediği nesne türünü belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="935f2-402">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="935f2-403">Film denetleyicisini oluştururken, *Ayrıntılar. cshtml* dosyasının en üstüne aşağıdaki `@model` deyimleri otomatik olarak eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="935f2-403">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="935f2-404">Bu `@model` yönergesi, kesin olarak belirlenmiş bir `Model` nesnesi kullanarak denetleyicinin görünüme geçirildiği filme erişmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="935f2-404">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="935f2-405">Örneğin, *details. cshtml* görünümünde, kod her bir film alanını `DisplayNameFor` `DisplayFor` ve HTML yardımcılarını türü kesin belirlenmiş `Model` nesnesiyle geçirir.</span><span class="sxs-lookup"><span data-stu-id="935f2-405">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="935f2-406">`Create` ve `Edit` yöntemleri ve görünümleri bir `Movie` model nesnesi de iletir.</span><span class="sxs-lookup"><span data-stu-id="935f2-406">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="935f2-407">*Dizin. cshtml* görünümünü ve film denetleyicisindeki `Index` yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="935f2-407">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="935f2-408">`List`, `View` yöntemini çağırdığında kodun bir nesne nasıl oluşturduğunu fark edin.</span><span class="sxs-lookup"><span data-stu-id="935f2-408">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="935f2-409">Kod, `Index` eylem yönteminden bu `Movies` listesini görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="935f2-409">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="935f2-410">Film denetleyicisini oluştururken, yapı iskelesi *Index. cshtml* dosyasının en üstüne aşağıdaki `@model` ifadesini otomatik olarak dahil edin:</span><span class="sxs-lookup"><span data-stu-id="935f2-410">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="935f2-411">`@model` yönergesi, kesin olarak belirlenmiş bir `Model` nesnesi kullanarak, denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="935f2-411">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="935f2-412">Örneğin, *Index. cshtml* görünümünde, kod kesin türü belirtilmiş `Model` nesnesi üzerinde `foreach` bir ifadesiyle filmlerle döngü yapılır:</span><span class="sxs-lookup"><span data-stu-id="935f2-412">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="935f2-413">`Model` nesne kesin olarak yazıldığı için (bir `IEnumerable<Movie>` nesnesi olarak), döngüdeki her öğe `Movie`olarak yazılır.</span><span class="sxs-lookup"><span data-stu-id="935f2-413">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="935f2-414">Diğer avantajların yanı sıra, kodun derleme zaman denetimini alacağınız anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="935f2-414">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="935f2-415">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="935f2-415">Additional resources</span></span>

* [<span data-ttu-id="935f2-416">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="935f2-416">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="935f2-417">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="935f2-417">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="935f2-418">Daha önce bir [veritabanı Ile çalışmaya](working-with-sql.md)
> [bir görünüm ekleme](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="935f2-418">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
