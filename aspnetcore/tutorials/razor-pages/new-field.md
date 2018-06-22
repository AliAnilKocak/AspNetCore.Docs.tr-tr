---
title: ASP.NET Core Razor sayfasında yeni bir alan ekleyin
author: rick-anderson
description: Razor sayfasını Entity Framework Çekirdek ile yeni bir alan eklemek nasıl gösterir
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 05/30/2018
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: d9bf8c7cea20bf38aacf432465d7b33514bcd64d
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36277299"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a>ASP.NET Core Razor sayfasında yeni bir alan ekleyin

tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu bölümde kullandığınız [Entity Framework](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) yeni bir alan modele eklemek ve bu geçirmek için Code First geçişleri veritabanına değiştirin.

EF Code First otomatik olarak Code First bir veritabanı oluşturmak için kullanırken:

* Bir tablo veritabanı şeması öğesinden oluşturulduğu modeli sınıfları ile eşit olup olmadığını izlemek için veritabanına ekler.
* EF modeli sınıfları DB ile eşitlenmiş durumda değilse, bir özel durum oluşturur. 

Şema/model eşitlenmiş otomatik doğrulanması tutarsız veritabanı/kod sorunları bulmak kolaylaştırır.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Film modeline derecelendirme özellik ekleme

Açık *Models/Movie.cs* dosya ve ekleme bir `Rating` özelliği:
::: moniker range="= aspnetcore-2.0"
[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"
[!code-csharp[](razor-pages-start/sample/RazorPagesMovie21/Models/MovieDateRating.cs?highlight=13&name=snippet)]

::: moniker-end

(Ctrl + Shift + B) uygulaması oluşturma.

Düzen *Pages/Movies/Index.cshtml*ve ekleme bir `Rating` alan:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=40-42,61-63)]

Ekleme `Rating` silme ve ayrıntıları sayfaları alanı.

Güncelleştirme *Create.cshtml* ile bir `Rating` alan. Kopyalayıp önceki yapıştırın `<div>` alanları güncelleştir öğesi ve let IntelliSense Yardım. IntelliSense çalışır [etiket Yardımcıları](xref:mvc/views/tag-helpers/intro).

![Geliştirici asp öznitelik değerini Harf R yazdı-için görünümün ikinci etiket öğesindeki. IntelliSense bağlam menüsü listesinde otomatik olarak vurgulanmış derecelendirme dahil olmak üzere kullanılabilir alanlar gösteren görüntülendi. Geliştirici alanını tıklattığında veya klavyedeki Enter tuşuna bastığında, değer derecelendirme için ayarlanır.](new-field/_static/cr.png)

Aşağıdaki kodda gösterildiği *Create.cshtml* ile bir `Rating` alan:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?highlight=36-40)]

Ekleme `Rating` sayfasını Düzenle alanı.

Uygulama DB yeni alanı içerecek şekilde güncelleştirilene kadar çalışmaz. Şimdi, uygulama atar çalıştırırsanız bir `SqlException`:

```
SqlException: Invalid column name 'Rating'.
```

Bu hata veritabanının film tablonun şeması farklı güncelleştirilmiş film model sınıfı kaynaklanır. (Var. hiçbir `Rating` veritabanı tablosundaki sütun.)

Hatayı çözümlemek için birkaç yaklaşım vardır:

1. Otomatik olarak bırakın ve yeni model sınıfı şeması kullanarak veritabanını yeniden oluşturma Entity Framework vardır. Bu yaklaşım, erken geliştirme döngüsü uygundur; hızlı bir şekilde modeli ve veritabanı şeması birlikte gelişmesi sağlar. Dezavantajı, veritabanında var olan veri kaybı ' dir. Bir üretim veritabanında bu yaklaşımı kullanmak istemiyorsanız! Şema değişiklikleri DB bırakarak ve otomatik olarak test verileri ile veritabanı oluşturmak için bir başlatıcı kullanarak bir uygulama geliştirmek için verimli bir şekilde görülür.

2. Açıkça modeli sınıfları eşleşecek şekilde var olan veritabanının şema değiştirin. Bu yaklaşımın avantajı, verilerinizi tutmak ' dir. Bu değişikliği yapmak ya da el ile veya komut dosyası bir veritabanı oluşturarak değiştirin.

3. Veritabanı şeması güncelleştirmek için Code First Migrations kullanın.

Bu öğretici için Code First Migrations kullanın.

Güncelleştirme `SeedData` böylece yeni bir sütun için bir değer sağlayan sınıf. Bir örnek değişiklik aşağıda gösterilen, ancak her biri için bu değişikliği yapmak istersiniz `new Movie` bloğu.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

::: moniker range="= aspnetcore-2.0"
Bkz: [SeedData.cs dosya tamamlandı](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/SeedDataRating.cs).
::: moniker-end

::: moniker range=">= aspnetcore-2.1"
Bkz: [SeedData.cs dosya tamamlandı](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21/Models/SeedDataRating.cs).
::: moniker-end

Çözümü oluşturun.

<a name="pmc"></a> Gelen **Araçları** menüsünde, select **NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu**.
PMC aşağıdaki komutları girin:

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` Komutu framework bildirir:

* Karşılaştırma `Movie` ile model `Movie` DB şeması.
* Yeni modele DB şeması geçirmek için kod oluşturun.

Adı "Derecelendirme" rastgeledir ve geçiş dosya adı için kullanılır. Geçiş dosya için anlamlı bir ad kullanmak yararlıdır.

<a name="ssox"></a> Veritabanındaki tüm kayıtları silerseniz, başlatıcı DB Çekirdek ve dahil `Rating` alan. Tarayıcıda veya gelen silme bağlantılarla bunu yapabilirsiniz [Sql Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (SSOX). SSOX veritabanını silmek için:

* Veritabanı içinde SSOX seçin.
* Veritabanını sağ tıklatın ve seçin *silmek*.
* Denetleme **var olan bağlantıları kapatın**.
* Seçin **Tamam**.
* İçinde [PMC](xref:tutorials/razor-pages/new-field#pmc), veritabanını güncelleştirin:

  ```powershell
  Update-Database
  ```

Uygulamayı çalıştırın ve oluşturma/düzenleme/görüntüleme filmler ile yapabilecekleriniz doğrulayın bir `Rating` alan. Veritabanı dağıtılan değil, IIS Express durdurun ve ardından uygulamayı çalıştırın.

> [!div class="step-by-step"]
> [Önceki: Arama ekleme](xref:tutorials/razor-pages/search)
> [sonraki: doğrulama ekleme](xref:tutorials/razor-pages/validation)
