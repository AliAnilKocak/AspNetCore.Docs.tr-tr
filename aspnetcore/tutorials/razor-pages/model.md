---
title: ASP.NET Core bir Razor sayfalarının uygulama için model ekleme
author: rick-anderson
description: Entity Framework Çekirdek (EF çekirdek) kullanarak bir veritabanındaki filmler yönetmek için sınıfları ekleme bulur.
manager: wpickett
ms.author: riande
ms.date: 07/27/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages/model
ms.openlocfilehash: 8542f4f3e516a62c19308d0c03e1ba4b155ed1ce
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="11c13-103">ASP.NET Core bir Razor sayfalarının uygulama için model ekleme</span><span class="sxs-lookup"><span data-stu-id="11c13-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

[!INCLUDE [model1](../../includes/RP/model1.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="11c13-104">Bir veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="11c13-104">Add a data model</span></span>

<span data-ttu-id="11c13-105">Çözüm Gezgini'nde sağ **RazorPagesMovie** Proje > **Ekle** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="11c13-105">In Solution Explorer, right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="11c13-106">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="11c13-106">Name the folder *Models*.</span></span>

<span data-ttu-id="11c13-107">Sağ tıklayın *modelleri* klasör.</span><span class="sxs-lookup"><span data-stu-id="11c13-107">Right click the *Models* folder.</span></span> <span data-ttu-id="11c13-108">Seçin **ekleme** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="11c13-108">Select **Add** > **Class**.</span></span> <span data-ttu-id="11c13-109">Sınıf adını **film** ve aşağıdaki özellikleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="11c13-109">Name the class **Movie** and add the following properties:</span></span>

[!INCLUDE [model 2](../../includes/RP/model2.md)]

<a name="cs"></a>
### <a name="add-a-database-connection-string"></a><span data-ttu-id="11c13-110">Bir veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="11c13-110">Add a database connection string</span></span>

<span data-ttu-id="11c13-111">Bir bağlantı dizesi eklemek *appsettings.json* dosya.</span><span class="sxs-lookup"><span data-stu-id="11c13-111">Add a connection string to the *appsettings.json* file.</span></span>

[!code-json[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=8-10)]

<a name="reg"></a>
###  <a name="register-the-database-context"></a><span data-ttu-id="11c13-112">Veritabanı bağlamı kaydetme</span><span class="sxs-lookup"><span data-stu-id="11c13-112">Register the database context</span></span>

<span data-ttu-id="11c13-113">Veritabanı bağlamı ile kayıt [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısında *haline* dosya.</span><span class="sxs-lookup"><span data-stu-id="11c13-113">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in the *Startup.cs* file.</span></span>

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=3-5,7-9)]

<span data-ttu-id="11c13-114">Herhangi bir hata yoksa doğrulamak için projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="11c13-114">Build the project to verify you don't have any errors.</span></span>

<a name="pmc"></a>
## <a name="add-scaffold-tooling-and-perform-initial-migration"></a><span data-ttu-id="11c13-115">İskele araç ekleyin ve başlangıç geçiş gerçekleştirme</span><span class="sxs-lookup"><span data-stu-id="11c13-115">Add scaffold tooling and perform initial migration</span></span>

<span data-ttu-id="11c13-116">Bu bölümde, Paket Yöneticisi Konsolu (PMC) için kullanın:</span><span class="sxs-lookup"><span data-stu-id="11c13-116">In this section, you use the Package Manager Console (PMC) to:</span></span>

* <span data-ttu-id="11c13-117">Visual Studio web kod oluşturma paketi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="11c13-117">Add the Visual Studio web code generation package.</span></span> <span data-ttu-id="11c13-118">Bu paket, yapı iskelesi altyapısı çalıştırmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="11c13-118">This package is required to run the scaffolding engine.</span></span>
* <span data-ttu-id="11c13-119">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="11c13-119">Add an initial migration.</span></span>
* <span data-ttu-id="11c13-120">Veritabanı ile ilk geçiş güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="11c13-120">Update the database with the initial migration.</span></span>

<span data-ttu-id="11c13-121">Gelen **Araçları** menüsünde, select **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu**.</span><span class="sxs-lookup"><span data-stu-id="11c13-121">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="11c13-123">PMC aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="11c13-123">In the PMC, enter the following commands:</span></span>

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
Add-Migration Initial
Update-Database
```

<span data-ttu-id="11c13-124">Alternatif olarak, aşağıdaki .NET Core CLI komutları kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="11c13-124">Alternatively, the following .NET Core CLI commands can be used:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet ef migrations add Initial
dotnet ef database update
```

<span data-ttu-id="11c13-125">`Install-Package` Komutu yapı iskelesi altyapısı çalıştırmak için gerekli araçları yükler.</span><span class="sxs-lookup"><span data-stu-id="11c13-125">The `Install-Package` command installs the tooling required to run the scaffolding engine.</span></span>

<span data-ttu-id="11c13-126">`Add-Migration` Komutu ilk veritabanı şeması oluşturmak için kod oluşturur.</span><span class="sxs-lookup"><span data-stu-id="11c13-126">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="11c13-127">Belirtilen model şeması dayalı `DbContext` (içinde *Models/MovieContext.cs* dosyası).</span><span class="sxs-lookup"><span data-stu-id="11c13-127">The schema is based on the model specified in the `DbContext` (In the *Models/MovieContext.cs* file).</span></span> <span data-ttu-id="11c13-128">`Initial` Bağımsız değişkeni geçişler adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="11c13-128">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="11c13-129">Herhangi bir ad kullanabilirsiniz, ancak kurala göre geçiş açıklayan bir ad seçin.</span><span class="sxs-lookup"><span data-stu-id="11c13-129">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="11c13-130">Bkz: [geçişler giriş](xref:data/ef-mvc/migrations#introduction-to-migrations) daha fazla bilgi için.</span><span class="sxs-lookup"><span data-stu-id="11c13-130">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="11c13-131">`Update-Database` Komutu çalıştırır `Up` yönteminde *geçişleri /\<zaman damgası > _InitialCreate.cs* dosyası bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="11c13-131">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [model 4windows](../../includes/RP/model4Win.md)]

[!INCLUDE [model 4](../../includes/RP/model4tbl.md)]

<a name="test"></a>
### <a name="test-the-app"></a><span data-ttu-id="11c13-132">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="11c13-132">Test the app</span></span>

* <span data-ttu-id="11c13-133">Uygulamayı çalıştırın ve append `/Movies` URL tarayıcıda (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="11c13-133">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>
* <span data-ttu-id="11c13-134">Test **oluşturma** bağlantı.</span><span class="sxs-lookup"><span data-stu-id="11c13-134">Test the **Create** link.</span></span>

  ![Sayfa oluşturma](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* <span data-ttu-id="11c13-136">Test **Düzenle**, **ayrıntıları**, ve **silmek** bağlantılar.</span><span class="sxs-lookup"><span data-stu-id="11c13-136">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="11c13-137">Bir SQL özel durumu alırsanız, geçişler çalıştırın ve veritabanı güncelleştirilmiş doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="11c13-137">If you get a SQL exception, verify you have run migrations and updated the database:</span></span>

<span data-ttu-id="11c13-138">Sonraki öğretici yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="11c13-138">The next tutorial explains the files created by scaffolding.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="11c13-139">[Önceki: Başlama](xref:tutorials/razor-pages/razor-pages-start)
> [sonraki: iskele kurulmuş Razor sayfaları](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="11c13-139">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>    
