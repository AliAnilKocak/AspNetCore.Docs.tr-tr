---
title: Veritabanı ve ASP.NET Core çalışma
author: rick-anderson
description: Bir veritabanı ve ASP.NET Core çalışmayı açıklar.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 197697f28e9faa45c1ac2b7f993bde15994957e5
ms.sourcegitcommit: 051f068c78931432e030b60094c38376d64d013e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440384"
---
# <a name="work-with-a-database-and-aspnet-core"></a>Veritabanı ve ASP.NET Core çalışma

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [ALi Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Nesnesi veritabanına bağlanma ve nesneleri veritabanı kayıtlarına eşleme `Movie` görevini işler. `RazorPagesMovieContext` Veritabanı bağlamı, `ConfigureServices` *Startup.cs*içindeki yöntemde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi okur `ConnectionString`. Yerel geliştirme için, *appSettings. JSON* dosyasından bağlantı dizesini alır.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Veritabanı (`Database={Database name}`) için ad değeri, üretilen kodunuz için farklı olacaktır. Ad değeri rastgele.

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini gerçek bir veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir. Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür. LocalDB, isteğe bağlı olarak başlar ve karmaşık yapılandırma olduğundan kullanıcı modunda çalışır. Varsayılan olarak, LocalDB veritabanı `*.mdf` `C:/Users/<user/>` dizinde dosya oluşturur.

<a name="ssox"></a>
* **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.

  ![Görünüm menüsü](sql/_static/ssox.png)

* `Movie` Tabloya sağ tıklayıp **Görünüm Tasarımcısı**' nı seçin:

  ![Film tablosunda açık bağlamsal menüler](sql/_static/design.png)

  ![Tasarımcı 'da açık film tabloları](sql/_static/dv.png)

Seçeneğinin yanında `ID`bulunan anahtar simgesine göz önünde edin. Varsayılan olarak, EF birincil anahtar için adlı `ID` bir özellik oluşturur.

* `Movie` Tabloya sağ tıklayın ve **verileri görüntüle**' yi seçin:

  ![Tablo verilerini gösteren film tablosu açma](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Veritabanının çekirdeğini oluşturma

Modeller klasöründe aşağıdaki kodla adlı `SeedData` yeni bir  sınıf oluşturun:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

VERITABANıNDA herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Tohum başlatıcısı ekleme

İçinde *Program.cs*, değişiklik `Main` yöntemi aşağıdakileri yapmak için:

* Bir DB bağlamı örneği bağımlılık ekleme kapsayıcısını alın.
* Temel yöntemi çağırın ve bu yönteme geçerek bağlamı geçer.
* Çekirdek yöntemi tamamlandığında bağlamı atın.

Aşağıdaki kod güncelleştirilmiş gösterir *Program.cs* dosya.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

Bir üretim uygulaması çağırmaz `Database.Migrate`. Çalıştırılmayan aşağıdaki özel durumu `Update-Database` engellemek için önceki koda eklenir:

SqlException Oturum açma tarafından istenen "RazorPagesMovieContext-21" veritabanı açılamıyor. Oturum açılamadı.
' Kullanıcı adı ' kullanıcısı için oturum açma başarısız.

### <a name="test-the-app"></a>Uygulamayı test etme

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* VERITABANıNDAKI tüm kayıtları silin. Bunu, tarayıcıda veya [Ssox](xref:tutorials/razor-pages/new-field#ssox) 'ten silme bağlantılarıyla yapabilirsiniz
* Çekirdek yöntemin çalışması için uygulamayı başlamaya zorlayın ( `Startup` sınıftaki yöntemleri çağırın). Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir. Bunu aşağıdaki yaklaşımlardan biriyle yapabilirsiniz:

  * Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur**' a dokunun:

    ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](sql/_static/stopIIS.png)

    * VS hata ayıklama modunda çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 tuşuna basın.
    * Ile hata ayıklama modunda çalıştırıyorsanız, hata ayıklayıcıyı durdurun ve F5 tuşuna basın.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

VERITABANıNDAKI tüm kayıtları silin (Bu nedenle çekirdek yöntemi çalışacaktır). Veritabanını temel alarak uygulamayı durdurup başlatın.

Uygulama, sağlanan verileri gösterir.

---

Sonraki öğreticide, verilerin sunumu gelişmeyecektir.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Öncekini Yapı iskelesi Razor Pages](xref:tutorials/razor-pages/page)
> [ileri: Sayfaları güncelleştirme](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Nesnesi veritabanına bağlanma ve nesneleri veritabanı kayıtlarına eşleme `Movie` görevini işler. `RazorPagesMovieContext` Veritabanı bağlamı, `ConfigureServices` *Startup.cs*içindeki yöntemde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

İçinde `ConfigureServices`kullanılan yöntemler hakkında daha fazla bilgi için bkz.:

* `CookiePolicyOptions` [ASP.NET Core IÇIN AB genel veri koruma yönetmeliği (GDPR) desteği](xref:security/gdpr) .
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi okur `ConnectionString`. Yerel geliştirme için, *appSettings. JSON* dosyasından bağlantı dizesini alır.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Veritabanı (`Database={Database name}`) için ad değeri, üretilen kodunuz için farklı olacaktır. Ad değeri rastgele.

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini gerçek bir veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir. Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür. LocalDB, isteğe bağlı olarak başlar ve karmaşık yapılandırma olduğundan kullanıcı modunda çalışır. Varsayılan olarak, LocalDB veritabanı `*.mdf` `C:/Users/<user/>` dizinde dosya oluşturur.

<a name="ssox"></a>
* **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.

  ![Görünüm menüsü](sql/_static/ssox.png)

* `Movie` Tabloya sağ tıklayıp **Görünüm Tasarımcısı**' nı seçin:

  ![Film tablosunda bağlam menüsü açık](sql/_static/design.png)

  ![Tasarımcıda film tablosu aç](sql/_static/dv.png)

Seçeneğinin yanında `ID`bulunan anahtar simgesine göz önünde edin. Varsayılan olarak, EF birincil anahtar için adlı `ID` bir özellik oluşturur.

* `Movie` Tabloya sağ tıklayın ve **verileri görüntüle**' yi seçin:

  ![Tablo verilerini gösteren film tablosu açma](sql/_static/vd22.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Veritabanının çekirdeğini oluşturma

Modeller klasöründe aşağıdaki kodla adlı `SeedData` yeni bir  sınıf oluşturun:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

VERITABANıNDA herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Tohum başlatıcısı ekleme

İçinde *Program.cs*, değişiklik `Main` yöntemi aşağıdakileri yapmak için:

* Bir DB bağlamı örneği bağımlılık ekleme kapsayıcısını alın.
* Temel yöntemi çağırın ve bu yönteme geçerek bağlamı geçer.
* Çekirdek yöntemi tamamlandığında bağlamı atın.

Aşağıdaki kod güncelleştirilmiş gösterir *Program.cs* dosya.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Bir üretim uygulaması çağırmaz `Database.Migrate`. Çalıştırılmayan aşağıdaki özel durumu `Update-Database` engellemek için önceki koda eklenir:

SqlException Oturum açma tarafından istenen "RazorPagesMovieContext-21" veritabanı açılamıyor. Oturum açılamadı.
' Kullanıcı adı ' kullanıcısı için oturum açma başarısız.

### <a name="test-the-app"></a>Uygulamayı test etme

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* VERITABANıNDAKI tüm kayıtları silin. Bunu, tarayıcıda veya [Ssox](xref:tutorials/razor-pages/new-field#ssox) 'ten silme bağlantılarıyla yapabilirsiniz
* Çekirdek yöntemin çalışması için uygulamayı başlamaya zorlayın ( `Startup` sınıftaki yöntemleri çağırın). Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir. Bunu aşağıdaki yaklaşımlardan biriyle yapabilirsiniz:

  * Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur**' a dokunun:

    ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](sql/_static/stopIIS.png)

    * VS hata ayıklama modunda çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 tuşuna basın.
    * Ile hata ayıklama modunda çalıştırıyorsanız, hata ayıklayıcıyı durdurun ve F5 tuşuna basın.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

VERITABANıNDAKI tüm kayıtları silin (Bu nedenle çekirdek yöntemi çalışacaktır). Veritabanını temel alarak uygulamayı durdurup başlatın.

Uygulama, sağlanan verileri gösterir.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

VERITABANıNDAKI tüm kayıtları silin (Bu nedenle çekirdek yöntemi çalışacaktır). Veritabanını temel alarak uygulamayı durdurup başlatın.

Uygulama, sağlanan verileri gösterir.

---

Uygulama, sağlanan verileri gösterir:

![Film verilerini gösteren Chrome 'da film uygulaması açık](sql/_static/m55.png)

Sonraki öğretici, verilerin sunumunu temizler.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Öncekini Yapı iskelesi Razor Pages](xref:tutorials/razor-pages/page)
> [ileri: Sayfaları güncelleştirme](xref:tutorials/razor-pages/da1)

::: moniker-end