---
title: "Model bir ASP.NET Core MVC uygulamasına ekleme"
author: rick-anderson
description: "Bir model için basit bir ASP.NET Core uygulama ekleyin."
keywords: ASP.NET Core, MVC, iskele, iskele kurma
ms.author: riande
manager: wpickett
ms.devlang: csharp
ms.date: 09/22/2017
ms.topic: get-started-article
ms.assetid: 8dc28498-eeee-1638-b903-b593059e9f39
ms.technology: aspnet
ms.prod: .net-core
uid: tutorials/first-mvc-app-mac/adding-model
ms.openlocfilehash: 15fef14ab754b48935d17e754daa011acdd90b42
ms.sourcegitcommit: a73fb5161fbdbee1d2e8474205e9840a134ee2ad
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/12/2017
---
[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model1.md)]

* Sağ *modelleri* klasörünü ve ardından **Ekle** > **yeni dosya**. 
* İçinde **yeni dosya** iletişim:

  * Seçin **genel** sol bölmede.
  * Seçin **boş sınıfı** center sorun içinde.
  * Sınıf adını **film** seçip **yeni**.

Aşağıdaki özellikleri ekleyin `Movie` sınıfı:

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]

`ID` Alan veritabanı için birincil anahtarı gerekli.

Herhangi bir hata yoksa doğrulamak için projeyi oluşturun. Artık elinizde bir **M**odeldeki, **M**VC uygulama.

## <a name="prepare-the-project-for-scaffolding"></a>Proje için askılamayı hazırlama

- Proje dosyasını sağ tıklatın ve ardından **Araçlar > Düzenle dosya**.

  ![Yukarıdaki adımı görünümü](adding-model/_static/1.png)

- Aşağıdaki vurgulanmış şekilde NuGet paketlerini eklemek *MvcMovie.csproj* dosyası:
             
  [!code-csharp[Main](../first-mvc-app-xplat/start-mvc/sample/MvcMovie/MvcMovie.csproj?highlight=7,10)]

- Dosyayı kaydedin.

- Oluşturma bir *Models/MvcMovieContext.cs* dosya ve aşağıdakileri ekleyin `MvcMovieContext` sınıfı:[!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Models/MvcMovieContext.cs)]
   
- Açık *haline* dosya ve iki kullanımları ekleyin:[!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet1&highlight=1,2)]

- Veritabanı bağlamı Ekle *haline* dosyası:

   [!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-7)]

  Bu, hangi modeli sınıfları veri modelinde bulunan Entity Framework bildirir. Bir tanımladığınız *varlık kümesini* veritabanında bir filmi tablo olarak temsil edilir film nesnelerin.

- Bir hata doğrulamak için projeyi oluşturun.

## <a name="scaffold-the-moviecontroller"></a>İskele MovieController

Proje klasöründe bir terminal penceresi açın ve aşağıdaki komutları çalıştırın:

```
dotnet restore
dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries 
```
Hata alırsanız `No executable found matching command "dotnet-aspnet-codegenerator", verify`:

 * Proje dizininde var. Proje dizinine sahip *Program.cs*, *haline* ve *.csproj* dosyaları.
 * Dotnet sürüm 1.1 veya daha üstü olduğunda. Çalıştırma `dotnet` sürümü almak için.
 * Eklediğiniz `<DotNetCliToolReference>` için elment [MvcMovie.csproj dosya](#prepare-the-project-for-scaffolding).
 
<!--
> [!NOTE]
> If you get an error when the scaffolding command runs, see [issue 444 in the scaffolding repository](https://github.com/aspnet/scaffolding/issues/444) for a workaround.
-->

Yapı iskelesi altyapısı aşağıdaki oluşturur:

* Film denetleyicisi (*Controllers/MoviesController.cs*)
* Razor Görünüm Oluştur, Sil, ayrıntıları, düzenleme ve dizin sayfalar için dosyaları (*görünümler/filmler/\*.cshtml*)

Otomatik olarak oluşturulmasını [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (oluşturma, okuma, güncelleştirme ve silme) eylem yöntemleri ve görünümler olarak bilinir *iskele*. En kısa sürede bir filmi veritabanı yönetmenizi sağlayan bir tam olarak işlevsel bir web uygulaması gerekir.

### <a name="add-the-files-to-visual-studio"></a>Visual Studio için dosyaları Ekle

* Ekleme *MovieController.cs* Visual Studio projesi dosyasına:

  * Sağ tıklayın *denetleyicileri* klasörü ve select **Ekle > dosyaları Ekle**.
  * Seçin *MovieController.cs* dosya.

* Ekleme *filmler* klasör ve görünümler:

  * Sağ tıklayın *görünümleri* klasörü ve select **Ekle > varolan klasörü Ekle**.
  * Gidin *görünümleri* klasöründe seçin *Views\Movies*ve ardından **açık**.
  * İçinde **film eklenecek dosyaları seçin** iletişim kutusunda **dahil tüm**ve ardından **Tamam**.

[!INCLUDE[adding-model 2x](../../includes/mvc-intro/adding-model2xp.md)]

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model3.md)]

Bir veritabanı ve görüntüleme, düzenleme, güncelleştirme ve verileri silmek için sayfaları artık sahipsiniz. Sonraki öğreticide biz veritabanı ile çalışması.

## <a name="additional-resources"></a>Ek kaynaklar

* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)
* [Genelleştirme ve yerelleştirme](xref:fundamentals/localization)

>[!div class="step-by-step"]
[Önceki bir görünümü ekleme](adding-view.md)
[sonraki SQL ile çalışma](working-with-sql.md)  
