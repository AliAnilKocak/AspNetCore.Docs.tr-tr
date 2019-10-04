---
title: ASP.NET Core MVC uygulamasında SQL ile çalışma
author: rick-anderson
description: ASP.NET Core MVC uygulamasında SQL Server LocalDB veya SQLite kullanma hakkında bilgi edinin.
ms.author: riande
ms.date: 8/16/2019
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: cb356bca50540d7c471cf625a26bfe2dd155b627
ms.sourcegitcommit: 3ffcd8cbff8b49128733842f72270bc58279de70
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955913"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="acd15-103">ASP.NET Core 'de SQL ile çalışma</span><span class="sxs-lookup"><span data-stu-id="acd15-103">Work with SQL in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="acd15-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="acd15-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="acd15-105">@No__t-0 nesnesi veritabanına bağlanma ve `Movie` nesnelerini veritabanı kayıtlarına eşleme görevini işler.</span><span class="sxs-lookup"><span data-stu-id="acd15-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="acd15-106">Veritabanı bağlamı, *Startup.cs* dosyasındaki `ConfigureServices` yönteminde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="acd15-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="acd15-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acd15-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

<span data-ttu-id="acd15-108">ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi, `ConnectionString` ' i okur.</span><span class="sxs-lookup"><span data-stu-id="acd15-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="acd15-109">Yerel geliştirme için, *appSettings. JSON* dosyasından bağlantı dizesini alır:</span><span class="sxs-lookup"><span data-stu-id="acd15-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="acd15-110">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acd15-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="acd15-111">ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi, `ConnectionString` ' i okur.</span><span class="sxs-lookup"><span data-stu-id="acd15-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="acd15-112">Yerel geliştirme için, *appSettings. JSON* dosyasından bağlantı dizesini alır:</span><span class="sxs-lookup"><span data-stu-id="acd15-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="acd15-113">Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini bir üretim SQL Server ayarlamak için bir ortam değişkeni kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="acd15-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="acd15-114">Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="acd15-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="acd15-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acd15-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="acd15-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="acd15-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="acd15-117">LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="acd15-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="acd15-118">LocalDB, isteğe bağlı olarak başlar ve karmaşık yapılandırma olduğundan kullanıcı modunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="acd15-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="acd15-119">Varsayılan olarak, LocalDB veritabanı *C:/Users/{User}* dizininde *. mdf* dosyaları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="acd15-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="acd15-120">**Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="acd15-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Görünüm menüsü](working-with-sql/_static/ssox.png)

* <span data-ttu-id="acd15-122">@No__t-0 tablosuna sağ tıklayıp **Görünüm tasarımcısı >**</span><span class="sxs-lookup"><span data-stu-id="acd15-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Film tablosunda bağlam menüsü açık](working-with-sql/_static/design.png)

  ![Tasarımcıda film tablosu aç](working-with-sql/_static/dv.png)

<span data-ttu-id="acd15-125">@No__t-0 ' ın yanındaki anahtar simgesine göz önünde edin.</span><span class="sxs-lookup"><span data-stu-id="acd15-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="acd15-126">Varsayılan olarak, EF, birincil anahtar `ID` adlı bir özellik oluşturacak.</span><span class="sxs-lookup"><span data-stu-id="acd15-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="acd15-127">@No__t-0 tablosuna sağ tıklayın **> verileri görüntüleyin**</span><span class="sxs-lookup"><span data-stu-id="acd15-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Film tablosunda bağlam menüsü açık](working-with-sql/_static/ssox2.png)

  ![Tablo verilerini gösteren film tablosu açma](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="acd15-130">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acd15-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="acd15-131">Veritabanının çekirdeğini oluşturma</span><span class="sxs-lookup"><span data-stu-id="acd15-131">Seed the database</span></span>

<span data-ttu-id="acd15-132">*Modeller* klasöründe `SeedData` adlı yeni bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="acd15-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="acd15-133">Oluşturulan kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="acd15-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="acd15-134">VERITABANıNDA herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.</span><span class="sxs-lookup"><span data-stu-id="acd15-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="acd15-135">Tohum başlatıcısı ekleme</span><span class="sxs-lookup"><span data-stu-id="acd15-135">Add the seed initializer</span></span>

<span data-ttu-id="acd15-136">*Program.cs* içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="acd15-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="acd15-137">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="acd15-137">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="acd15-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acd15-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="acd15-139">VERITABANıNDAKI tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="acd15-139">Delete all the records in the DB.</span></span> <span data-ttu-id="acd15-140">Bunu, tarayıcıda veya SSOX 'ten silme bağlantılarıyla yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="acd15-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="acd15-141">Çekirdek yöntemin çalışması için uygulamayı başlamaya zorlayın (`Startup` sınıfındaki Yöntemleri çağırın).</span><span class="sxs-lookup"><span data-stu-id="acd15-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="acd15-142">Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="acd15-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="acd15-143">Bunu aşağıdaki yaklaşımlardan biriyle yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="acd15-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="acd15-144">Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur** ' a dokunun</span><span class="sxs-lookup"><span data-stu-id="acd15-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express sistem tepsisi simgesi](working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="acd15-147">VS hata ayıklama modunda çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 'e basın</span><span class="sxs-lookup"><span data-stu-id="acd15-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="acd15-148">VS hata ayıklama modunda çalıştırıyorsanız, hata ayıklayıcıyı durdurun ve F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="acd15-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="acd15-149">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acd15-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="acd15-150">VERITABANıNDAKI tüm kayıtları silin (Bu nedenle çekirdek yöntemi çalışacaktır).</span><span class="sxs-lookup"><span data-stu-id="acd15-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="acd15-151">Veritabanını temel alarak uygulamayı durdurup başlatın.</span><span class="sxs-lookup"><span data-stu-id="acd15-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="acd15-152">Uygulama, sağlanan verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="acd15-152">The app shows the seeded data.</span></span>

![Microsoft Edge 'de film verilerini gösteren MVC film uygulaması açık](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="acd15-154">[Önceki](adding-model.md)
> [İleri](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="acd15-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="acd15-155">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="acd15-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="acd15-156">@No__t-0 nesnesi veritabanına bağlanma ve `Movie` nesnelerini veritabanı kayıtlarına eşleme görevini işler.</span><span class="sxs-lookup"><span data-stu-id="acd15-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="acd15-157">Veritabanı bağlamı, *Startup.cs* dosyasındaki `ConfigureServices` yönteminde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="acd15-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="acd15-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acd15-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="acd15-159">ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi, `ConnectionString` ' i okur.</span><span class="sxs-lookup"><span data-stu-id="acd15-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="acd15-160">Yerel geliştirme için, *appSettings. JSON* dosyasından bağlantı dizesini alır:</span><span class="sxs-lookup"><span data-stu-id="acd15-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="acd15-161">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acd15-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="acd15-162">ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi, `ConnectionString` ' i okur.</span><span class="sxs-lookup"><span data-stu-id="acd15-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="acd15-163">Yerel geliştirme için, *appSettings. JSON* dosyasından bağlantı dizesini alır:</span><span class="sxs-lookup"><span data-stu-id="acd15-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="acd15-164">Uygulamayı bir test veya üretim sunucusuna dağıtırken, bağlantı dizesini gerçek bir SQL Server ayarlamak için bir ortam değişkeni veya başka bir yaklaşım kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="acd15-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="acd15-165">Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="acd15-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="acd15-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acd15-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="acd15-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="acd15-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="acd15-168">LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="acd15-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="acd15-169">LocalDB, isteğe bağlı olarak başlar ve karmaşık yapılandırma olduğundan kullanıcı modunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="acd15-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="acd15-170">Varsayılan olarak, LocalDB veritabanı *C:/Users/{User}* dizininde *. mdf* dosyaları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="acd15-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="acd15-171">**Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="acd15-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Görünüm menüsü](working-with-sql/_static/ssox.png)

* <span data-ttu-id="acd15-173">@No__t-0 tablosuna sağ tıklayıp **Görünüm tasarımcısı >**</span><span class="sxs-lookup"><span data-stu-id="acd15-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Film tablosunda bağlam menüsü açık](working-with-sql/_static/design.png)

  ![Tasarımcıda film tablosu aç](working-with-sql/_static/dv.png)

<span data-ttu-id="acd15-176">@No__t-0 ' ın yanındaki anahtar simgesine göz önünde edin.</span><span class="sxs-lookup"><span data-stu-id="acd15-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="acd15-177">Varsayılan olarak, EF, birincil anahtar `ID` adlı bir özellik oluşturacak.</span><span class="sxs-lookup"><span data-stu-id="acd15-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="acd15-178">@No__t-0 tablosuna sağ tıklayın **> verileri görüntüleyin**</span><span class="sxs-lookup"><span data-stu-id="acd15-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Film tablosunda bağlam menüsü açık](working-with-sql/_static/ssox2.png)

  ![Tablo verilerini gösteren film tablosu açma](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="acd15-181">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acd15-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="acd15-182">Veritabanının çekirdeğini oluşturma</span><span class="sxs-lookup"><span data-stu-id="acd15-182">Seed the database</span></span>

<span data-ttu-id="acd15-183">*Modeller* klasöründe `SeedData` adlı yeni bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="acd15-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="acd15-184">Oluşturulan kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="acd15-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="acd15-185">VERITABANıNDA herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.</span><span class="sxs-lookup"><span data-stu-id="acd15-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="acd15-186">Tohum başlatıcısı ekleme</span><span class="sxs-lookup"><span data-stu-id="acd15-186">Add the seed initializer</span></span>

<span data-ttu-id="acd15-187">*Program.cs* içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="acd15-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="acd15-188">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="acd15-188">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="acd15-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acd15-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="acd15-190">VERITABANıNDAKI tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="acd15-190">Delete all the records in the DB.</span></span> <span data-ttu-id="acd15-191">Bunu, tarayıcıda veya SSOX 'ten silme bağlantılarıyla yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="acd15-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="acd15-192">Çekirdek yöntemin çalışması için uygulamayı başlamaya zorlayın (`Startup` sınıfındaki Yöntemleri çağırın).</span><span class="sxs-lookup"><span data-stu-id="acd15-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="acd15-193">Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="acd15-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="acd15-194">Bunu aşağıdaki yaklaşımlardan biriyle yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="acd15-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="acd15-195">Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur** ' a dokunun</span><span class="sxs-lookup"><span data-stu-id="acd15-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express sistem tepsisi simgesi](working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="acd15-198">VS hata ayıklama modunda çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 'e basın</span><span class="sxs-lookup"><span data-stu-id="acd15-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="acd15-199">VS hata ayıklama modunda çalıştırıyorsanız, hata ayıklayıcıyı durdurun ve F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="acd15-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="acd15-200">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acd15-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="acd15-201">VERITABANıNDAKI tüm kayıtları silin (Bu nedenle çekirdek yöntemi çalışacaktır).</span><span class="sxs-lookup"><span data-stu-id="acd15-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="acd15-202">Veritabanını temel alarak uygulamayı durdurup başlatın.</span><span class="sxs-lookup"><span data-stu-id="acd15-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="acd15-203">Uygulama, sağlanan verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="acd15-203">The app shows the seeded data.</span></span>

![Microsoft Edge 'de film verilerini gösteren MVC film uygulaması açık](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="acd15-205">[Önceki](adding-model.md)
> [İleri](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="acd15-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
