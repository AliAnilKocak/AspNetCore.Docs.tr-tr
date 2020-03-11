---
title: 'Öğretici: EF Core ile geçiş özelliğini kullanma-ASP.NET MVC'
description: Bu öğreticide, ASP.NET Core MVC uygulamasındaki veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliğini kullanmaya başlayabilirsiniz.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 8b3417205457a5ce5fa16994701a06e2a4d7d350
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665727"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="a08db-103">Öğretici: EF Core ile geçiş özelliğini kullanma-ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="a08db-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="a08db-104">Bu öğreticide, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliğini kullanmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a08db-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="a08db-105">Sonraki öğreticilerde, veri modelini değiştirirken daha fazla geçiş ekleyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="a08db-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="a08db-106">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="a08db-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a08db-107">Geçişler hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="a08db-107">Learn about migrations</span></span>
> * <span data-ttu-id="a08db-108">Bağlantı dizesini değiştirme</span><span class="sxs-lookup"><span data-stu-id="a08db-108">Change the connection string</span></span>
> * <span data-ttu-id="a08db-109">İlk geçiş oluşturma</span><span class="sxs-lookup"><span data-stu-id="a08db-109">Create an initial migration</span></span>
> * <span data-ttu-id="a08db-110">Yukarı ve aşağı yöntemleri inceleyin</span><span class="sxs-lookup"><span data-stu-id="a08db-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="a08db-111">Veri modeli anlık görüntüsü hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="a08db-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="a08db-112">Geçişi Uygula</span><span class="sxs-lookup"><span data-stu-id="a08db-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a08db-113">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="a08db-113">Prerequisites</span></span>

* [<span data-ttu-id="a08db-114">Sıralama, filtreleme ve sayfalama</span><span class="sxs-lookup"><span data-stu-id="a08db-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="a08db-115">Geçişler hakkında</span><span class="sxs-lookup"><span data-stu-id="a08db-115">About migrations</span></span>

<span data-ttu-id="a08db-116">Yeni bir uygulama geliştirirken, veri modeliniz sıklıkla değişir ve model her değiştiğinde veritabanıyla eşitlenmemiş olur.</span><span class="sxs-lookup"><span data-stu-id="a08db-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="a08db-117">Mevcut değilse veritabanını oluşturmak için Entity Framework yapılandırarak bu öğreticileri başlatmış olursunuz.</span><span class="sxs-lookup"><span data-stu-id="a08db-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="a08db-118">Veri modelini her değiştirdiğinizde (varlık sınıfları ekleyin, kaldırın veya değiştirin ya da DbContext sınıfınızı değiştirin); veritabanını silebilir ve EF, modelle eşleşen yeni bir tane oluşturur ve test verileriyle birlikte olur.</span><span class="sxs-lookup"><span data-stu-id="a08db-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="a08db-119">Veritabanını veri modeliyle eşitlenmiş halde tutma yöntemi, uygulamayı üretime dağıtana kadar iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="a08db-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="a08db-120">Uygulama üretimde çalışırken, genellikle tutmak istediğiniz verileri saklar ve yeni sütun ekleme gibi her değişiklik yaptığınızda her şeyi kaybetmek istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="a08db-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="a08db-121">EF Core geçişleri özelliği, yeni bir veritabanı oluşturmak yerine EF 'in veritabanı şemasını güncelleştirmesine olanak sağlayarak bu sorunu çözer.</span><span class="sxs-lookup"><span data-stu-id="a08db-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="a08db-122">Geçişlerle çalışmak için **Paket Yöneticisi konsolu 'nu** (PMC) veya CLI 'yi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a08db-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="a08db-123">Bu öğreticiler CLı komutlarının nasıl kullanılacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="a08db-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="a08db-124">PMC hakkındaki bilgiler [Bu öğreticinin sonunda](#pmc).</span><span class="sxs-lookup"><span data-stu-id="a08db-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="a08db-125">Bağlantı dizesini değiştirme</span><span class="sxs-lookup"><span data-stu-id="a08db-125">Change the connection string</span></span>

<span data-ttu-id="a08db-126">*AppSettings. JSON* dosyasında, bağlantı dizesindeki veritabanının adını ContosoUniversity2 veya kullandığınız bilgisayarda kullanmadığınız başka bir ad olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="a08db-126">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="a08db-127">Bu değişiklik projeyi ilk geçişin yeni bir veritabanı oluşturacak şekilde ayarlar.</span><span class="sxs-lookup"><span data-stu-id="a08db-127">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="a08db-128">Bu, geçişleri kullanmaya başlamak için gerekli değildir, ancak daha sonra iyi bir fikir olduğunu göreceksiniz.</span><span class="sxs-lookup"><span data-stu-id="a08db-128">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="a08db-129">Veritabanı adını değiştirmeye alternatif olarak, veritabanını silebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a08db-129">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="a08db-130">**SQL Server Nesne Gezgini** (ssox) veya `database drop` CLI komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="a08db-130">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> <span data-ttu-id="a08db-131">Aşağıdaki bölümde CLı komutlarının nasıl çalıştırılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="a08db-131">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="a08db-132">İlk geçiş oluşturma</span><span class="sxs-lookup"><span data-stu-id="a08db-132">Create an initial migration</span></span>

<span data-ttu-id="a08db-133">Değişikliklerinizi kaydedin ve projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="a08db-133">Save your changes and build the project.</span></span> <span data-ttu-id="a08db-134">Sonra bir komut penceresi açın ve proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="a08db-134">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="a08db-135">Bunu yapmanın hızlı bir yolu aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="a08db-135">Here's a quick way to do that:</span></span>

* <span data-ttu-id="a08db-136">**Çözüm Gezgini**' de projeye sağ tıklayın ve bağlam menüsünden **klasörü dosya Gezgini 'nde aç** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="a08db-136">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Dosya Gezgini menü öğesinde aç](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="a08db-138">Adres çubuğuna "cmd" yazın ve ENTER tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="a08db-138">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Komut penceresini aç](migrations/_static/open-command-window.png)

<span data-ttu-id="a08db-140">Komut penceresine aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="a08db-140">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="a08db-141">Komut penceresinde aşağıdakine benzer bir çıktı görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="a08db-141">You see output like the following in the command window:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="a08db-142">*"DotNet-EF" komutuyla eşleşen yürütülebilir dosya bulunamadı*hata iletisi görürseniz, sorun giderme konusunda yardım için [Bu blog gönderisine](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) bakın.</span><span class="sxs-lookup"><span data-stu-id="a08db-142">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="a08db-143">Bir hata iletisi görürseniz "*dosyaya erişilemiyor... ContosoUniversity. dll başka bir işlem tarafından kullanıldığı için.* ", Windows sistem tepsisinde IIS Express simgesini bulun ve sağ tıklayın, sonra da **contosouniversity > siteyi durdur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="a08db-143">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="a08db-144">Yukarı ve aşağı yöntemleri inceleyin</span><span class="sxs-lookup"><span data-stu-id="a08db-144">Examine Up and Down methods</span></span>

<span data-ttu-id="a08db-145">`migrations add` komutunu çalıştırdığınızda EF, veritabanını sıfırdan oluşturacak kodu oluşturdu.</span><span class="sxs-lookup"><span data-stu-id="a08db-145">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="a08db-146">Bu kod, *_InitialCreate. cs >\<zaman damgası*adlı dosyada *geçişler* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="a08db-146">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="a08db-147">`InitialCreate` sınıfının `Up` yöntemi, veri modeli varlık kümelerine karşılık gelen veritabanı tablolarını oluşturur ve aşağıdaki örnekte gösterildiği gibi `Down` yöntemi onları siler.</span><span class="sxs-lookup"><span data-stu-id="a08db-147">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="a08db-148">Geçişler, bir geçiş için veri modeli değişikliklerini uygulamak üzere `Up` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="a08db-148">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="a08db-149">Güncelleştirmeyi geri almak için bir komut girdiğinizde, geçişler `Down` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="a08db-149">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="a08db-150">Bu kod, `migrations add InitialCreate` komutunu girdiğinizde oluşturulan ilk geçişe yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="a08db-150">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="a08db-151">Geçiş adı parametresi (örnekteki "ınitialcreate") dosya adı için kullanılır ve istediğiniz her şey olabilir.</span><span class="sxs-lookup"><span data-stu-id="a08db-151">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="a08db-152">Geçiş sırasında nelerin yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir.</span><span class="sxs-lookup"><span data-stu-id="a08db-152">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="a08db-153">Örneğin, "AddDepartmentTable" adlı bir geçişe daha sonra ad yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a08db-153">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="a08db-154">Veritabanı zaten mevcut olduğunda ilk geçişi oluşturduysanız veritabanı oluşturma kodu oluşturulur, ancak veritabanı veri modeliyle zaten eşleştiğinden çalıştırması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="a08db-154">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="a08db-155">Uygulamayı, veritabanının mevcut olmadığı başka bir ortama dağıttığınızda, bu kod veritabanınızı oluşturmak için çalışır, bu nedenle ilk önce test etmek iyi bir fikirdir.</span><span class="sxs-lookup"><span data-stu-id="a08db-155">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="a08db-156">Bu nedenle, bağlantı dizesinde veritabanının adını daha önce değiştirmiş olursunuz. böylece geçişler sıfırdan yeni bir tane oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="a08db-156">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="a08db-157">Veri modeli anlık görüntüsü</span><span class="sxs-lookup"><span data-stu-id="a08db-157">The data model snapshot</span></span>

<span data-ttu-id="a08db-158">Geçişler, *geçiş/SchoolContextModelSnapshot. cs*içinde geçerli veritabanı şemasının bir *anlık görüntüsünü* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a08db-158">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="a08db-159">Bir geçiş eklediğinizde EF, veri modeli Snapshot dosyası ile karşılaştırılarak nelerin değiştirildiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="a08db-159">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="a08db-160">Bir geçişi kaldırmak için [DotNet EF geçişleri kaldır](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="a08db-160">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="a08db-161">`dotnet ef migrations remove` geçişi siler ve anlık görüntünün doğru şekilde sıfırlanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="a08db-161">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="a08db-162">`dotnet ef migrations remove` başarısız olursa, hata hakkında daha fazla bilgi edinmek için `dotnet ef migrations remove -v` kullanın.</span><span class="sxs-lookup"><span data-stu-id="a08db-162">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="a08db-163">Anlık görüntü dosyasının nasıl kullanıldığı hakkında daha fazla bilgi için bkz. [Takım ortamlarında EF Core geçişleri](/ef/core/managing-schemas/migrations/teams) .</span><span class="sxs-lookup"><span data-stu-id="a08db-163">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="a08db-164">Geçişi Uygula</span><span class="sxs-lookup"><span data-stu-id="a08db-164">Apply the migration</span></span>

<span data-ttu-id="a08db-165">Komut penceresinde, veritabanı ve tabloları oluşturmak için aşağıdaki komutu girin.</span><span class="sxs-lookup"><span data-stu-id="a08db-165">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="a08db-166">Komutun çıktısı, veritabanının ayarlandığı SQL komutlarının günlüklerini görmeniz dışında `migrations add` komutuna benzerdir.</span><span class="sxs-lookup"><span data-stu-id="a08db-166">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="a08db-167">Günlüklerin çoğu aşağıdaki örnek çıktıda atlanır.</span><span class="sxs-lookup"><span data-stu-id="a08db-167">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="a08db-168">Günlük iletilerinde bu ayrıntı düzeyini görmemeyi tercih ediyorsanız, appSettings 'de günlük düzeyini değiştirebilirsiniz *. Development. JSON* dosyası.</span><span class="sxs-lookup"><span data-stu-id="a08db-168">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="a08db-169">Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="a08db-169">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

<span data-ttu-id="a08db-170">İlk öğreticide yaptığınız gibi veritabanını incelemek için **SQL Server Nesne Gezgini** kullanın.</span><span class="sxs-lookup"><span data-stu-id="a08db-170">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="a08db-171">Hangi geçişlerin veritabanına uygulandığını izleyen bir \_\_EFMigrationsHistory tablosunun eklenmesini fark edeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="a08db-171">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="a08db-172">Bu tablodaki verileri görüntüleyin ve ilk geçiş için bir satır görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="a08db-172">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="a08db-173">(Önceki CLı çıkış örneğinde son oturum, bu satırı oluşturan INSERT ifadesini gösterir.)</span><span class="sxs-lookup"><span data-stu-id="a08db-173">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="a08db-174">Her şeyin daha önce olduğu gibi çalıştığını doğrulamak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a08db-174">Run the application to verify that everything still works the same as before.</span></span>

![Öğrenciler dizin sayfası](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="a08db-176">CLı ve PMC karşılaştırması</span><span class="sxs-lookup"><span data-stu-id="a08db-176">Compare CLI and PMC</span></span>

<span data-ttu-id="a08db-177">Geçişleri yönetmek için EF araçları, .NET Core CLI komutlardan veya Visual Studio **Paket Yöneticisi konsolu** (PMC) penceresindeki PowerShell cmdlet 'lerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="a08db-177">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="a08db-178">Bu öğreticide, CLı 'nın nasıl kullanılacağı gösterilmektedir, ancak isterseniz PMC 'yi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a08db-178">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="a08db-179">PMC komutlarına yönelik EF komutları [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) paketidir.</span><span class="sxs-lookup"><span data-stu-id="a08db-179">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="a08db-180">Bu paket [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur, bu nedenle uygulamanızın `Microsoft.AspNetCore.App`için bir paket başvurusu varsa bir paket başvurusu eklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="a08db-180">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="a08db-181">**Önemli:** Bu, *. csproj* dosyasını düzenleyerek CLI için yüklediğiniz paket ile aynı değildir.</span><span class="sxs-lookup"><span data-stu-id="a08db-181">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="a08db-182">Bu adın adı, `Tools.DotNet`biten CLı paketi adından farklı olarak `Tools`biter.</span><span class="sxs-lookup"><span data-stu-id="a08db-182">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="a08db-183">CLı komutları hakkında daha fazla bilgi için bkz. [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="a08db-183">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="a08db-184">PMC komutları hakkında daha fazla bilgi için bkz. [Paket Yöneticisi Konsolu (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="a08db-184">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="a08db-185">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="a08db-185">Get the code</span></span>

[<span data-ttu-id="a08db-186">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="a08db-186">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="a08db-187">Sonraki adım</span><span class="sxs-lookup"><span data-stu-id="a08db-187">Next step</span></span>

<span data-ttu-id="a08db-188">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="a08db-188">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a08db-189">Geçişler hakkında öğrenildi</span><span class="sxs-lookup"><span data-stu-id="a08db-189">Learned about migrations</span></span>
> * <span data-ttu-id="a08db-190">NuGet geçiş paketleri hakkında bilgi edinildi</span><span class="sxs-lookup"><span data-stu-id="a08db-190">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="a08db-191">Bağlantı dizesi değiştirildi</span><span class="sxs-lookup"><span data-stu-id="a08db-191">Changed the connection string</span></span>
> * <span data-ttu-id="a08db-192">İlk geçiş oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="a08db-192">Created an initial migration</span></span>
> * <span data-ttu-id="a08db-193">Yukarı ve aşağı yöntemleri İnceleme</span><span class="sxs-lookup"><span data-stu-id="a08db-193">Examined Up and Down methods</span></span>
> * <span data-ttu-id="a08db-194">Veri modeli anlık görüntüsü hakkında bilgi edinildi</span><span class="sxs-lookup"><span data-stu-id="a08db-194">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="a08db-195">Geçiş uygulandı</span><span class="sxs-lookup"><span data-stu-id="a08db-195">Applied the migration</span></span>

<span data-ttu-id="a08db-196">Veri modelini genişletme hakkında daha gelişmiş konulara bakmak için sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="a08db-196">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="a08db-197">Ek geçişler oluşturma ve uygulama gibi.</span><span class="sxs-lookup"><span data-stu-id="a08db-197">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="a08db-198">Ek geçişler oluşturma ve uygulama</span><span class="sxs-lookup"><span data-stu-id="a08db-198">Create and apply additional migrations</span></span>](complex-data-model.md)
