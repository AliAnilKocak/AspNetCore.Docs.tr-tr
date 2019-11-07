---
title: ASP.NET Core bir Razor Pages uygulamasına model ekleme
author: rick-anderson
description: Entity Framework Core (EF Core) kullanarak bir veritabanında film yönetmeye yönelik sınıfların nasıl ekleneceğini öğrenin.
ms.author: riande
ms.date: 11/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: f2c9c2fc8112ef8a1a5afdbe448de6319c43521d
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73761231"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>ASP.NET Core bir Razor Pages uygulamasına model ekleme

[Rick Anderson](https://twitter.com/RickAndMSFT) tarafından

::: moniker range=">= aspnetcore-3.0"

Bu bölümde, platformlar arası bir [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetimi için sınıflar eklenir. Bir ASP.NET Core şablondan oluşturulan uygulamalar bir SQLite veritabanı kullanır. Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core veritabanı sağlayıcısı](/ef/core/providers/sqlite)) ile kullanılır. EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.

Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir. Veritabanında depolanan verilerin özelliklerini tanımlar.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Veri modeli ekleme

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

**Yeni > klasör** **eklemek** > **RazorPagesMovie** projesine sağ tıklayın. Klasör *modellerini*adlandırın.

*Modeller* klasörüne sağ tıklayın.  > **sınıfı** **Ekle** ' yi seçin. Sınıf **filmi**olarak adlandırın.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Modeller*adlı bir klasör ekleyin.
* *Movie.cs*adlı *modeller* klasörüne bir sınıf ekleyin.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Çözüm Gezgini, **RazorPagesMovie** projesine sağ tıklayın ve ardından  > **Yeni klasör** **Ekle**' yi seçin. Klasör *modellerini*adlandırın.
* *Modeller* klasörüne sağ tıklayın ve ardından > **yeni dosya** **Ekle** ' yi seçin.
* **Yeni dosya** iletişim kutusunda:

  * Sol bölmedeki **genel** ' i seçin.
  * Orta bölmede **boş sınıf** ' ı seçin.
  * Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

Derleme hatası olmadığını doğrulamak için projeyi derleyin.

## <a name="scaffold-the-movie-model"></a>Film modelini dolandırın

Bu bölümde, film modeli scafkatdır. Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

*Sayfalar/filmler* klasörü oluştur:

* **Yeni > klasör** **eklemek** > *Sayfalar* klasörüne sağ tıklayın.
* Klasör *filmlerini* adlandırın

*Sayfalar/filmler* klasörüne sağ tıklayın > > **yeni yapı Iskelesi öğesi** **ekleyin** .

![Önceki yönergelerden görüntü.](model/_static/sca.png)

**Yapı Iskelesi Ekle** iletişim kutusunda, **Entity Framework (crud)** > **Ekle**' yi kullanarak Razor Pages seçin.

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

**Entity Framework kullanarak Razor Pages Ekle (CRUD)** iletişim kutusunu doldurun:

* **Model sınıfı** açılan kutusunda **Film (RazorPagesMovie. modeller)** öğesini seçin.
* **Veri bağlamı sınıfı** satırında **+** (artı) işaretini seçin ve oluşturulan adı RazorPagesMovie ' dan değiştirin. **Modeller**. RazorPagesMovieContext to RazorPagesMovie. **Veri**. RazorPagesMovieContext. [Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir. Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.
* **Ekle**' yi seçin.

![Önceki yönergelerden görüntü.](model/_static/3/arp.png)

*AppSettings. JSON* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).
* Scafkatlama aracını yükler:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Windows için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **MacOS ve Linux için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).
* Scafkatlama aracını yükler:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Şu komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

---

### <a name="files-created"></a>Oluşturulan dosyalar

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.
* *Data/RazorPagesMovieContext. cs*

### <a name="updated"></a>Güncellendi

* *Startup.cs*

Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur:

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.

Oluşturulan dosyalar sonraki bölümde açıklanmaktadır.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>İlk geçiş

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:

* İlk geçiş ekleyin.
* Veritabanını ilk geçişle güncelleştirin.

**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

PMC 'de aşağıdaki komutları girin:

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi. Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur. ' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "

Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.

Geçişler komutu, ilk veritabanı şemasını oluşturmak için kod üretir. Şema `DbContext` ' da belirtilen modeli temel alır. `InitialCreate` bağımsız değişkeni, geçişleri adlandırmak için kullanılır. Herhangi bir ad kullanılabilir, ancak geçiş işlemini açıklayan bir ad seçilir.

`update` komutu uygulanmamış geçişlerde `Up` yöntemini çalıştırır. Bu durumda `update`, veritabanını oluşturan *geçişler/\<zaman damgası > _ınitialcreate. cs* dosyasında `Up` yöntemini çalıştırır.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Bağımlılık ekleme ile kaydedilen bağlamı inceleyin

ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur. Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir. Bu hizmetleri gerektiren bileşenler (örneğin Razor Pages), bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.

Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.

`Startup.ConfigureServices` metodunu inceleyin. Vurgulanan satır, scaffolder tarafından eklendi:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext`, `Movie` modeli için EF Core işlevselliğini (oluşturma, okuma, güncelleştirme, silme, vb.) koordine eder. Veri bağlamı (`RazorPagesMovieContext`) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir. Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Önceki kod, varlık kümesi için bir [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir. Bir varlık, tablodaki bir satıra karşılık gelir.

Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir. Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) , *appSettings. JSON* dosyasından bağlantı dizesini okur.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`Up` metodunu inceleyin.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

`Up` metodunu inceleyin.

---

<a name="test"></a>

### <a name="test-the-app"></a>Uygulamayı test etme

* Uygulamayı çalıştırın ve tarayıcıdaki URL 'ye `/Movies` ekleyin (`http://localhost:port/movies`).

Şu hatayı alırsanız:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

[Geçişler adımını](#pmc)kaçırdınız.

* **Oluştur** bağlantısını test edin.

  ![Sayfa oluştur](model/_static/conan.png)

  > [!NOTE]
  > `Price` alanına ondalık virgül giremeyebilirsiniz. Ondalık bir nokta ve ABD Ingilizcesi olmayan tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir. Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.

* **Düzenleme**, **Ayrıntılar**ve **silme** bağlantılarını test edin.

Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: başlangıç](xref:tutorials/razor-pages/razor-pages-start)
> [Sonraki: Scafkatal Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

Bu bölümde, platformlar arası bir [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetimi için sınıflar eklenir. Bir ASP.NET Core şablondan oluşturulan uygulamalar bir SQLite veritabanı kullanır. Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core veritabanı sağlayıcısı](/ef/core/providers/sqlite)) ile kullanılır. EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.

Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir. Veritabanında depolanan verilerin özelliklerini tanımlar.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Veri modeli ekleme

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

**Yeni > klasör** **eklemek** > **RazorPagesMovie** projesine sağ tıklayın. Klasör *modellerini*adlandırın.

*Modeller* klasörüne sağ tıklayın.  > **sınıfı** **Ekle** ' yi seçin. Sınıf **filmi**olarak adlandırın.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Modeller*adlı bir klasör ekleyin.
* *Movie.cs*adlı *modeller* klasörüne bir sınıf ekleyin.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Çözüm Gezgini, **RazorPagesMovie** projesine sağ tıklayın ve ardından  > **Yeni klasör** **Ekle**' yi seçin. Klasör *modellerini*adlandırın.
* *Modeller* klasörüne sağ tıklayın ve ardından > **yeni dosya** **Ekle** ' yi seçin.
* **Yeni dosya** iletişim kutusunda:

  * Sol bölmedeki **genel** ' i seçin.
  * Orta bölmede **boş sınıf** ' ı seçin.
  * Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

Derleme hatası olmadığını doğrulamak için projeyi derleyin.

## <a name="scaffold-the-movie-model"></a>Film modelini dolandırın

Bu bölümde, film modeli scafkatdır. Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

*Sayfalar/filmler* klasörü oluştur:

* **Yeni > klasör** **eklemek** > *Sayfalar* klasörüne sağ tıklayın.
* Klasör *filmlerini* adlandırın

*Sayfalar/filmler* klasörüne sağ tıklayın > > **yeni yapı Iskelesi öğesi** **ekleyin** .

![Önceki yönergelerden görüntü.](model/_static/sca.png)

**Yapı Iskelesi Ekle** iletişim kutusunda, **Entity Framework (crud)** > **Ekle**' yi kullanarak Razor Pages seçin.

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

**Entity Framework kullanarak Razor Pages Ekle (CRUD)** iletişim kutusunu doldurun:
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* **Model sınıfı** açılan kutusunda **Film (RazorPagesMovie. modeller)** öğesini seçin.
* **Veri bağlamı sınıfı** satırında **+** (artı) Işaretini seçin ve oluşturulan **RazorPagesMovie. modeller. RazorPagesMovieContext**adını kabul edin.
* **Ekle**' yi seçin.

![Önceki yönergelerden görüntü.](model/_static/arp.png)

*AppSettings. JSON* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).
* Scafkatlama aracını yükler:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Windows için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **MacOS ve Linux için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).
* Scafkatlama aracını yükler:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Şu komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:

### <a name="files-created"></a>Oluşturulan dosyalar

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.
* *Data/RazorPagesMovieContext. cs*

### <a name="file-updated"></a>Dosya güncelleştirildi

* *Startup.cs*

Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.

<a name="pmc"></a>

## <a name="initial-migration"></a>İlk geçiş

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:

* İlk geçiş ekleyin.
* Veritabanını ilk geçişle güncelleştirin.

**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

PMC 'de aşağıdaki komutları girin:

```Powershell
Add-Migration Initial
Update-Database
```

`Add-Migration` komutu, ilk veritabanı şemasını oluşturmak için kod üretir. Şema, `DbContext` ( *RazorPagesMovieContext.cs* dosyasında) içinde belirtilen modeli temel alır. `InitialCreate` bağımsız değişkeni, geçişi adlandırmak için kullanılır. Herhangi bir ad kullanılabilir, ancak kurala göre geçiş işlemini açıklayan bir ad kullanılır. Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.

`Update-Database` komutu *geçişler/\<zaman damgası > _ınitialcreate. cs* dosyasında `Up` yöntemini çalıştırır. `Up` yöntemi veritabanını oluşturur.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> Yukarıdaki komutlar şu uyarıyı oluşturur: " *' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi. Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur. ' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin.* " Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Bağımlılık ekleme ile kaydedilen bağlamı inceleyin

ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur. Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir. Bu hizmetleri gerektiren bileşenler (örneğin Razor Pages), bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.

Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.

`Startup.ConfigureServices` metodunu inceleyin. Vurgulanan satır, scaffolder tarafından eklendi:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext`, `Movie` modeli için EF Core işlevselliğini (oluşturma, okuma, güncelleştirme, silme, vb.) koordine eder. Veri bağlamı (`RazorPagesMovieContext`) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir. Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Önceki kod, varlık kümesi için bir [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir. Bir varlık, tablodaki bir satıra karşılık gelir.

Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir. Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) , *appSettings. JSON* dosyasından bağlantı dizesini okur.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`Up` metodunu inceleyin.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

`Up` metodunu inceleyin.

---

<a name="test"></a>

### <a name="test-the-app"></a>Uygulamayı test etme

* Uygulamayı çalıştırın ve tarayıcıdaki URL 'ye `/Movies` ekleyin (`http://localhost:port/movies`).

Şu hatayı alırsanız:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

[Geçişler adımını](#pmc)kaçırdınız.

* **Oluştur** bağlantısını test edin.

  ![Sayfa oluştur](model/_static/conan.png)

  > [!NOTE]
  > `Price` alanına ondalık virgül giremeyebilirsiniz. Ondalık bir nokta ve ABD Ingilizcesi olmayan tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir. Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.

* **Düzenleme**, **Ayrıntılar**ve **silme** bağlantılarını test edin.

Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: başlangıç](xref:tutorials/razor-pages/razor-pages-start)
> [Sonraki: Scafkatal Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end
