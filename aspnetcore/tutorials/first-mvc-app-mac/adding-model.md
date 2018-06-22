---
title: Mac için Visual Studio ile ASP.NET Core MVC uygulama için model ekleme
author: rick-anderson
description: Bir model için basit bir ASP.NET Core uygulama ekleyin.
ms.author: riande
ms.date: 09/22/2017
uid: tutorials/first-mvc-app-mac/adding-model
ms.openlocfilehash: 53d63cd554f6a3ec958f27ed35b0a30b1833f84c
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36276119"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app-with-visual-studio-for-mac"></a><span data-ttu-id="9895b-103">Mac için Visual Studio ile ASP.NET Core MVC uygulama için model ekleme</span><span class="sxs-lookup"><span data-stu-id="9895b-103">Add a model to an ASP.NET Core MVC app with Visual Studio for Mac</span></span>

[!INCLUDE [adding-model](../../includes/mvc-intro/adding-model1.md)]

* <span data-ttu-id="9895b-104">Sağ *modelleri* klasörünü ve ardından **Ekle** > **yeni dosya**.</span><span class="sxs-lookup"><span data-stu-id="9895b-104">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span> 
* <span data-ttu-id="9895b-105">İçinde **yeni dosya** iletişim:</span><span class="sxs-lookup"><span data-stu-id="9895b-105">In the **New File** dialog:</span></span>

  * <span data-ttu-id="9895b-106">Seçin **genel** sol bölmede.</span><span class="sxs-lookup"><span data-stu-id="9895b-106">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="9895b-107">Seçin **boş sınıfı** center sorun içinde.</span><span class="sxs-lookup"><span data-stu-id="9895b-107">Select **Empty Class** in the center pain.</span></span>
  * <span data-ttu-id="9895b-108">Sınıf adını **film** seçip **yeni**.</span><span class="sxs-lookup"><span data-stu-id="9895b-108">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="9895b-109">Aşağıdaki özellikleri ekleyin `Movie` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="9895b-109">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]

<span data-ttu-id="9895b-110">`ID` Alan veritabanı için birincil anahtarı gerekli.</span><span class="sxs-lookup"><span data-stu-id="9895b-110">The `ID` field is required by the database for the primary key.</span></span>

<span data-ttu-id="9895b-111">Herhangi bir hata yoksa doğrulamak için projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9895b-111">Build the project to verify you don't have any errors.</span></span> <span data-ttu-id="9895b-112">Artık elinizde bir **M**odeldeki, **M**VC uygulama.</span><span class="sxs-lookup"><span data-stu-id="9895b-112">You now have a **M**odel in your **M**VC app.</span></span>

## <a name="prepare-the-project-for-scaffolding"></a><span data-ttu-id="9895b-113">Proje için askılamayı hazırlama</span><span class="sxs-lookup"><span data-stu-id="9895b-113">Prepare the project for scaffolding</span></span>

- <span data-ttu-id="9895b-114">Proje dosyasını sağ tıklatın ve ardından **Araçlar > Düzenle dosya**.</span><span class="sxs-lookup"><span data-stu-id="9895b-114">Right click on the project file, and then select **Tools > Edit File**.</span></span>

  ![Yukarıdaki adımı görünümü](adding-model/_static/1.png)

- <span data-ttu-id="9895b-116">Aşağıdaki vurgulanmış şekilde NuGet paketlerini eklemek *MvcMovie.csproj* dosyası:</span><span class="sxs-lookup"><span data-stu-id="9895b-116">Add the following highlighted NuGet packages to the *MvcMovie.csproj* file:</span></span>
             
  [!code-csharp[](../first-mvc-app-xplat/start-mvc/sample/MvcMovie/MvcMovie.csproj?highlight=7,10)]

- <span data-ttu-id="9895b-117">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="9895b-117">Save the file.</span></span>

- <span data-ttu-id="9895b-118">Oluşturma bir *Models/MvcMovieContext.cs* dosya ve aşağıdakileri ekleyin `MvcMovieContext` sınıfı:  [!code-csharp[](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Models/MvcMovieContext.cs)]</span><span class="sxs-lookup"><span data-stu-id="9895b-118">Create a *Models/MvcMovieContext.cs* file and add the following `MvcMovieContext` class:  [!code-csharp[](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Models/MvcMovieContext.cs)]</span></span>
   
- <span data-ttu-id="9895b-119">Açık *haline* dosya ve iki kullanımları ekleyin:  [!code-csharp[](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet1&highlight=1,2)]</span><span class="sxs-lookup"><span data-stu-id="9895b-119">Open the *Startup.cs* file and add two usings:  [!code-csharp[](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet1&highlight=1,2)]</span></span>

- <span data-ttu-id="9895b-120">Veritabanı bağlamı Ekle *haline* dosyası:</span><span class="sxs-lookup"><span data-stu-id="9895b-120">Add the database context to the *Startup.cs* file:</span></span>

   [!code-csharp[](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-7)]

  <span data-ttu-id="9895b-121">Bu, hangi modeli sınıfları veri modelinde bulunan Entity Framework bildirir.</span><span class="sxs-lookup"><span data-stu-id="9895b-121">This tells Entity Framework which model classes are included in the data model.</span></span> <span data-ttu-id="9895b-122">Bir tanımladığınız *varlık kümesini* veritabanında bir filmi tablo olarak temsil edilir film nesnelerin.</span><span class="sxs-lookup"><span data-stu-id="9895b-122">You're defining one *entity set* of Movie objects, which will be represented in the database as a Movie table.</span></span>

- <span data-ttu-id="9895b-123">Bir hata doğrulamak için projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9895b-123">Build the project to verify there are no errors.</span></span>

## <a name="scaffold-the-moviecontroller"></a><span data-ttu-id="9895b-124">İskele MovieController</span><span class="sxs-lookup"><span data-stu-id="9895b-124">Scaffold the MovieController</span></span>

<span data-ttu-id="9895b-125">Proje klasöründe bir terminal penceresi açın ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9895b-125">Open a terminal window in the project folder and run the following commands:</span></span>

```
dotnet restore
dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries 
```
<span data-ttu-id="9895b-126">Hata alırsanız `No executable found matching command "dotnet-aspnet-codegenerator", verify`:</span><span class="sxs-lookup"><span data-stu-id="9895b-126">If you get the error `No executable found matching command "dotnet-aspnet-codegenerator", verify`:</span></span>

 * <span data-ttu-id="9895b-127">Proje dizininde var.</span><span class="sxs-lookup"><span data-stu-id="9895b-127">You are in the project directory.</span></span> <span data-ttu-id="9895b-128">Proje dizinine sahip *Program.cs*, *haline* ve *.csproj* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="9895b-128">The project directory has the *Program.cs*, *Startup.cs* and *.csproj* files.</span></span>
 * <span data-ttu-id="9895b-129">Dotnet sürüm 1.1 veya daha üstü olduğunda.</span><span class="sxs-lookup"><span data-stu-id="9895b-129">Your dotnet version is 1.1 or higher.</span></span> <span data-ttu-id="9895b-130">Çalıştırma `dotnet` sürümü almak için.</span><span class="sxs-lookup"><span data-stu-id="9895b-130">Run `dotnet` to get the version.</span></span>
 * <span data-ttu-id="9895b-131">Eklediğiniz `<DotNetCliToolReference>` öğesine [MvcMovie.csproj dosya](#prepare-the-project-for-scaffolding).</span><span class="sxs-lookup"><span data-stu-id="9895b-131">You have added the `<DotNetCliToolReference>` element to the [MvcMovie.csproj file](#prepare-the-project-for-scaffolding).</span></span>
 
<!--
> [!NOTE]
> If you get an error when the scaffolding command runs, see [issue 444 in the scaffolding repository](https://github.com/aspnet/scaffolding/issues/444) for a workaround.
-->

<span data-ttu-id="9895b-132">Yapı iskelesi altyapısı aşağıdaki oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9895b-132">The scaffolding engine creates the following:</span></span>

* <span data-ttu-id="9895b-133">Film denetleyicisi (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="9895b-133">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="9895b-134">Razor Görünüm Oluştur, Sil, ayrıntıları, düzenleme ve dizin sayfalar için dosyaları (*görünümler/filmler/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="9895b-134">Razor view files for Create, Delete, Details, Edit and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="9895b-135">Otomatik olarak oluşturulmasını [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (oluşturma, okuma, güncelleştirme ve silme) eylem yöntemleri ve görünümler olarak bilinir *iskele*.</span><span class="sxs-lookup"><span data-stu-id="9895b-135">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span> <span data-ttu-id="9895b-136">En kısa sürede bir filmi veritabanı yönetmenizi sağlayan bir tam olarak işlevsel bir web uygulaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9895b-136">You'll soon have a fully functional web application that lets you manage a movie database.</span></span>

### <a name="add-the-files-to-visual-studio"></a><span data-ttu-id="9895b-137">Visual Studio için dosyaları Ekle</span><span class="sxs-lookup"><span data-stu-id="9895b-137">Add the files to Visual Studio</span></span>

* <span data-ttu-id="9895b-138">Ekleme *MovieController.cs* Visual Studio projesi dosyasına:</span><span class="sxs-lookup"><span data-stu-id="9895b-138">Add the *MovieController.cs* file to the Visual Studio project:</span></span>

  * <span data-ttu-id="9895b-139">Sağ tıklayın *denetleyicileri* klasörü ve select **Ekle > dosyaları Ekle**.</span><span class="sxs-lookup"><span data-stu-id="9895b-139">Right-click on the *Controllers* folder and select **Add > Add Files**.</span></span>
  * <span data-ttu-id="9895b-140">Seçin *MovieController.cs* dosya.</span><span class="sxs-lookup"><span data-stu-id="9895b-140">Select the *MovieController.cs* file.</span></span>

* <span data-ttu-id="9895b-141">Ekleme *filmler* klasör ve görünümler:</span><span class="sxs-lookup"><span data-stu-id="9895b-141">Add the *Movies* folder and views:</span></span>

  * <span data-ttu-id="9895b-142">Sağ tıklayın *görünümleri* klasörü ve select **Ekle > varolan klasörü Ekle**.</span><span class="sxs-lookup"><span data-stu-id="9895b-142">Right-click on the *Views* folder and select **Add > Add Existing Folder**.</span></span>
  * <span data-ttu-id="9895b-143">Gidin *görünümleri* klasöründe seçin *Views\Movies*ve ardından **açık**.</span><span class="sxs-lookup"><span data-stu-id="9895b-143">Navigate to the *Views* folder, select *Views\Movies*, and then select **Open**.</span></span>
  * <span data-ttu-id="9895b-144">İçinde **film eklenecek dosyaları seçin** iletişim kutusunda **dahil tüm**ve ardından **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="9895b-144">In the **Select files to add from Movies** dialog, select **Include All**, and then **OK**.</span></span>

[!INCLUDE [adding-model 2x](../../includes/mvc-intro/adding-model2xp.md)]

[!INCLUDE [adding-model](../../includes/mvc-intro/adding-model3.md)]

<span data-ttu-id="9895b-145">Bir veritabanı ve görüntüleme, düzenleme, güncelleştirme ve verileri silmek için sayfaları artık sahipsiniz.</span><span class="sxs-lookup"><span data-stu-id="9895b-145">You now have a database and pages to display, edit, update and delete data.</span></span> <span data-ttu-id="9895b-146">Sonraki öğreticide biz veritabanı ile çalışması.</span><span class="sxs-lookup"><span data-stu-id="9895b-146">In the next tutorial, we'll work with the database.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9895b-147">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9895b-147">Additional resources</span></span>

* [<span data-ttu-id="9895b-148">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="9895b-148">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="9895b-149">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="9895b-149">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="9895b-150">[Önceki bir görünümü ekleme](adding-view.md)
> [sonraki SQL ile çalışma](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="9895b-150">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>  
