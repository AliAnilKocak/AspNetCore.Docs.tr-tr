---
title: ASP.NET Core Razor sayfasına yeni bir alan ekleyin
author: rick-anderson
description: Entity Framework Core ile Razor sayfasına nasıl yeni bir alan ekleneceğini gösterir
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 1b08e1515afe656b95be9fb436caa00cd53ab9ad
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72334099"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a>ASP.NET Core Razor sayfasına yeni bir alan ekleyin

[Rick Anderson](https://twitter.com/RickAndMSFT) tarafından

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:

* Modele yeni bir alan ekleyin.
* Yeni alan şeması değişikliğini veritabanına geçirin.

Bir veritabanını otomatik olarak oluşturmak için EF Code First kullanırken Code First:

* Veritabanı şemasının oluşturulduğu model sınıflarıyla eşitlenmiş olup olmadığını izlemek için veritabanına bir `__EFMigrationsHistory` tablosu ekler.
* Model sınıfları DB ile eşitlenmiyorsa, EF bir özel durum oluşturur.

Şema/modelin eşitlemede otomatik olarak doğrulanması, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Film modeline bir derecelendirme özelliği ekleme

*Modeller/film. cs* dosyasını açın ve bir `Rating` özelliği ekleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Uygulamayı derleyin.

*Pages/filmlerini/Index. cshtml*dosyasını düzenleyin ve `Rating` alanı ekleyin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

Aşağıdaki sayfaları güncelleştirin:

* Sil ve Ayrıntılar sayfalarına `Rating` alanını ekleyin.
* [Create. cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) dosyasını `Rating` alanıyla güncelleştirin.
* Düzenleme sayfasına `Rating` alanını ekleyin.

VERITABANı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz. Veritabanını güncelleştirmeden uygulamayı çalıştırmak bir @no__t oluşturur-0:

`SqlException: Invalid column name 'Rating'.`

@No__t-0 özel durumu, güncelleştirilmiş film modeli sınıfının, veritabanının film tablosunun şemasından farklı olmasından kaynaklanır. (Veritabanı tablosunda `Rating` sütunu yoktur.)

Hatayı çözmek için birkaç yaklaşım vardır:

1. Yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak bırakıp yeniden oluşturmaya Entity Framework. Bu yaklaşım, geliştirme döngüsünün başlarında daha erken bir yoldur; modeli ve veritabanı şemasını birlikte hızla gelişmenize olanak tanır. Downsıde, veritabanında var olan verileri kaybetmeniz. Bu yaklaşımı bir üretim veritabanında kullanmayın! DB 'yi şema değişikliklerinde bırakıp bir başlatıcı kullanarak veritabanının test verileriyle otomatik olarak çekirdeğini oluşturmak, genellikle bir uygulama geliştirmeye yönelik üretken bir yoldur.

2. Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin. Bu yaklaşımın avantajı, verilerinizi tutmanızı kullanmaktır. Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapabilirsiniz.

3. Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.

Bu öğretici için Code First Migrations kullanın.

@No__t-0 sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin. Aşağıda örnek bir değişiklik gösterilmektedir, ancak her bir `new Movie` bloğu için bu değişikliği yapmak isteyeceksiniz.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

[Tamamlanan SeedData.cs dosyasına](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs)bakın.

Çözümü oluşturun.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Derecelendirme alanı için bir geçiş ekleyin

**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.
PMC 'de aşağıdaki komutları girin:

```powershell
Add-Migration Rating
Update-Database
```

@No__t-0 komutu, çerçeveye şunları belirtir:

* @No__t-0 modelini `Movie` DB şemasıyla karşılaştırın.
* DB şemasını yeni modele geçirmek için kod oluşturun.

"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır. Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.

@No__t-0 komutu, çerçeveye şema değişikliklerinin veritabanına uygulanmasını ve mevcut verilerin korunmasını söyler.

<a name="ssox"></a>

VERITABANıNDAKI tüm kayıtları silerseniz, başlatıcı DB 'yi temel alır ve `Rating` alanını içerir. Bunu, tarayıcıda veya [SQL Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (ssox) silme bağlantılarıyla yapabilirsiniz.

Başka bir seçenek de veritabanını silmek ve geçişleri kullanarak veritabanını yeniden oluşturmaktır. SSOX 'te veritabanını silmek için:

* SSOX 'te veritabanını seçin.
* Veritabanına sağ tıklayın ve *Sil*' i seçin.
* **Mevcut bağlantıları kapat**' a bakın.
* **Tamam ' ı**seçin.
* [PMC](xref:tutorials/razor-pages/new-field#pmc)'de veritabanını güncelleştirin:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Veritabanını bırakıp yeniden oluşturun

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Geçiş klasörünü silin.  Veritabanını yeniden oluşturmak için aşağıdaki komutları kullanın.

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Uygulamayı çalıştırın ve `Rating` alanı ile film oluşturabileceğiniz/düzenleyebileceğiniz/görüntüleydiğinizi doğrulayın. Veritabanı birlikte yoksa, `SeedData.Initialize` yönteminde bir kesme noktası ayarlayın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Previous: arama @no__t ekleme](xref:tutorials/razor-pages/search)-1[Next: doğrulama ekleme](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:

* Modele yeni bir alan ekleyin.
* Yeni alan şeması değişikliğini veritabanına geçirin.

Bir veritabanını otomatik olarak oluşturmak için EF Code First kullanırken Code First:

* Veritabanı şemasının oluşturulduğu model sınıflarıyla uyumlu olup olmadığını izlemek için veritabanına bir tablo ekler.
* Model sınıfları DB ile eşitlenmiyorsa, EF bir özel durum oluşturur.

Şema/modelin eşitlemede otomatik olarak doğrulanması, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Film modeline bir derecelendirme özelliği ekleme

*Modeller/film. cs* dosyasını açın ve bir `Rating` özelliği ekleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Uygulamayı derleyin.

*Pages/filmlerini/Index. cshtml*dosyasını düzenleyin ve `Rating` alanı ekleyin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

Aşağıdaki sayfaları güncelleştirin:

* Sil ve Ayrıntılar sayfalarına `Rating` alanını ekleyin.
* [Create. cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) dosyasını `Rating` alanıyla güncelleştirin.
* Düzenleme sayfasına `Rating` alanını ekleyin.

VERITABANı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz. Şimdi çalıştırırsanız, uygulama bir @no__t atar-0:

`SqlException: Invalid column name 'Rating'.`

Bu hata, güncelleştirilmiş film modeli sınıfının, veritabanının film tablosunun şemasından farklı olmasından kaynaklanır. (Veritabanı tablosunda `Rating` sütunu yoktur.)

Hatayı çözmek için birkaç yaklaşım vardır:

1. Yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak bırakıp yeniden oluşturmaya Entity Framework. Bu yaklaşım, geliştirme döngüsünün başlarında daha erken bir yoldur; modeli ve veritabanı şemasını birlikte hızla gelişmenize olanak tanır. Downsıde, veritabanında var olan verileri kaybetmeniz. Bu yaklaşımı bir üretim veritabanında kullanmayın! DB 'yi şema değişikliklerinde bırakıp bir başlatıcı kullanarak veritabanının test verileriyle otomatik olarak çekirdeğini oluşturmak, genellikle bir uygulama geliştirmeye yönelik üretken bir yoldur.

2. Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin. Bu yaklaşımın avantajı, verilerinizi tutmanızı kullanmaktır. Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapabilirsiniz.

3. Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.

Bu öğretici için Code First Migrations kullanın.

@No__t-0 sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin. Aşağıda örnek bir değişiklik gösterilmektedir, ancak her bir `new Movie` bloğu için bu değişikliği yapmak isteyeceksiniz.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

[Tamamlanan SeedData.cs dosyasına](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)bakın.

Çözümü oluşturun.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Derecelendirme alanı için bir geçiş ekleyin

**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.
PMC 'de aşağıdaki komutları girin:

```powershell
Add-Migration Rating
Update-Database
```

@No__t-0 komutu, çerçeveye şunları belirtir:

* @No__t-0 modelini `Movie` DB şemasıyla karşılaştırın.
* DB şemasını yeni modele geçirmek için kod oluşturun.

"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır. Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.

@No__t-0 komutu, çerçeveye şema değişikliklerini veritabanına uygulamasını söyler.

<a name="ssox"></a>

VERITABANıNDAKI tüm kayıtları silerseniz, başlatıcı DB 'yi temel alır ve `Rating` alanını içerir. Bunu, tarayıcıda veya [SQL Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (ssox) silme bağlantılarıyla yapabilirsiniz.

Başka bir seçenek de veritabanını silmek ve geçişleri kullanarak veritabanını yeniden oluşturmaktır. SSOX 'te veritabanını silmek için:

* SSOX 'te veritabanını seçin.
* Veritabanına sağ tıklayın ve *Sil*' i seçin.
* **Mevcut bağlantıları kapat**' a bakın.
* **Tamam ' ı**seçin.
* [PMC](xref:tutorials/razor-pages/new-field#pmc)'de veritabanını güncelleştirin:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Veritabanını bırakıp yeniden oluşturun

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Veritabanını silin ve geçişleri kullanarak veritabanını yeniden oluşturun. Veritabanını silmek için veritabanı dosyasını (*Mvcmovie. db*) silin. Sonra `ef database update` komutunu çalıştırın:

```dotnetcli
dotnet ef database update
```

---

Uygulamayı çalıştırın ve `Rating` alanı ile film oluşturabileceğiniz/düzenleyebileceğiniz/görüntüleydiğinizi doğrulayın. Veritabanı birlikte yoksa, `SeedData.Initialize` yönteminde bir kesme noktası ayarlayın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Previous: arama @no__t ekleme](xref:tutorials/razor-pages/search)-1[Next: doğrulama ekleme](xref:tutorials/razor-pages/validation)

::: moniker-end
