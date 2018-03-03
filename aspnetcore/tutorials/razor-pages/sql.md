---
title: "SQL Server yerel veritabanı ve ASP.NET Core ile çalışma"
author: rick-anderson
description: "SQL Server yerel veritabanı ve ASP.NET Core ile çalışmayı açıklar."
manager: wpickett
ms.author: riande
ms.date: 08/07/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 408072fbe83eadb0ae3c35c2789bda8ecaac58c0
ms.sourcegitcommit: 7ac15eaae20b6d70e65f3650af050a7880115cbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/02/2018
---
# <a name="working-with-sql-server-localdb-and-aspnet-core"></a>SQL Server yerel veritabanı ve ASP.NET Core ile çalışma

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [CAN Audette](https://twitter.com/joeaudette) 

`MovieContext` Nesnesini işleme veritabanına bağlanırken ve eşleme görevi `Movie` veritabanı kayıtlarını nesnelere. Veritabanı bağlamı kayıtlı [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısında `ConfigureServices` yönteminde *haline* dosyası:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=7-8)]

ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistem okuma `ConnectionString`. İsteğe bağlı olarak yerel geliştirme için bağlantı dizesinden alır *appsettings.json* dosyası:

[!code-json[](razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=2&range=8-10)]

Bir test veya üretim sunucusuna uygulama dağıtırken, bir ortam değişkeni veya başka bir kullanabilirsiniz yaklaşım gerçek bir SQL Server bağlantı dizesini ayarlayın. Bkz: [yapılandırma](xref:fundamentals/configuration/index) daha fazla bilgi için.

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Yerel veritabanı, SQL Server Express Veritabanı Altyapısı'nın hedeflenen program geliştirme için hafif bir sürümüdür. Yerel veritabanı isteğe bağlı olarak başlar ve bu yüzden karmaşık yapılandırma kullanıcı modunda çalışır. Varsayılan olarak, yerel veritabanı bir veritabanı oluşturur "\*.mdf" dosyalar *C:/Users/\<kullanıcı\>*  dizini.

<a name="ssox"></a>
* Gelen **Görünüm** menüsünde, açık **SQL Server Nesne Gezgini** (SSOX).

  ![Görünüm menüsü](sql/_static/ssox.png)

* Sağ tıklayın `Movie` tablo ve seçin **Görünüm Tasarımcısı**:

  ![Bağlamsal menü film tablosunda Aç](sql/_static/design.png)

  ![Film Tablo Tasarımcısı'nda Aç](sql/_static/dv.png)

Anahtar simgesine yanına Not `ID`. Varsayılan olarak, EF adlı bir özellik oluşturur `ID` birincil anahtar.

* Sağ tıklayın `Movie` tablo ve seçin **görünüm verilerini**:

  ![Tablo verisi gösteren açık film tablosu](sql/_static/vd22.png)

## <a name="seed-the-database"></a>Çekirdek veritabanı

Adlı yeni bir sınıf oluşturun `SeedData` içinde *modelleri* klasör. Oluşturulan kod aşağıdakiyle değiştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/SeedData.cs?name=snippet_1)]

Olup olmadığını herhangi filmler DB'de, çekirdek Başlatıcı döndürür ve hiçbir filmler eklenir.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```
<a name="si"></a>
### <a name="add-the-seed-initializer"></a>Çekirdek Başlatıcısı ekleme

Çekirdek Başlatıcı sonuna ekleyin `Main` yönteminde *Program.cs* dosyası:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Program.cs)]

Uygulamayı test etme

* DB tüm kayıtları silin. Tarayıcıda veya gelen silme bağlantılarla bunu yapabilirsiniz [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Uygulamayı başlatmak için zorlama (yöntemleri Çağır `Startup` sınıfı) seed yöntemi çalıştığında. Başlatma zorlamak için IIS Express durdurulup yeniden gerekir. Bu yaklaşımın şunlardan birini yapabilirsiniz:

  * IIS Express sistem tepsisi bildirim alanı simgesini sağ tıklatın ve dokunun **çıkış** veya **durdurmak Site**:

    ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Bağlam menüsü](sql/_static/stopIIS.png)

   * VS olmayan hata ayıklama modunda çalışıyormuş hata ayıklama modunda çalıştırmak için F5 tuşuna basın.
   * Hata ayıklama modunda VS çalışıyormuş hata ayıklayıcıyı durdurduktan ve F5 tuşuna basın.
   
Uygulama hazırlığı yapmış veriler gösterir:

![Film uygulaması film verileri gösteren Chrome'da Aç](sql/_static/m55.png)

Sonraki öğretici verilerin sunuyu temizler.

>[!div class="step-by-step"]
[Önceki: İskele kurulmuş Razor sayfalarının](xref:tutorials/razor-pages/page)
[sonraki: sayfalarını güncelleştirme](xref:tutorials/razor-pages/da1)
