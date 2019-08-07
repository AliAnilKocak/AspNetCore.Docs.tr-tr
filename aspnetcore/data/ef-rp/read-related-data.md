---
title: ASP.NET Core EF Core ile Razor Pages-Ilgili verileri oku-6/8
author: rick-anderson
description: Bu öğreticide ilgili verileri okuyabilir ve görüntüleriz, yani Entity Framework gezinti özelliklerine yüklediği veriler.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 93fbb4741f476368d75d23162d6e2597de7b263e
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68819909"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a>ASP.NET Core EF Core ile Razor Pages-Ilgili verileri oku-6/8

, [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)ve [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

Bu öğreticide ilgili veriler okundu ve görüntülenir. İlgili veriler, EF Core gezinti özelliklerine yüklediği veri.

Olamaz çözmenize, sorunlarla karşılaşırsanız, [indirin veya tamamlanmış uygulamayı görüntüleyin.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Yükleme yönergeleri](xref:index#how-to-download-a-sample).

Aşağıdaki çizimler, Bu öğreticinin tamamlanan sayfalarını göstermektedir:

![Kurslar Dizin sayfası](read-related-data/_static/courses-index.png)

![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>İlgili verilerin Eager, açık ve geç yüklemesi

EF Core bir varlığın gezinti özelliklerine ilgili verileri yükleyebilmenin birkaç yolu vardır:

* [Eager yükleniyor](/ef/core/querying/related-data#eager-loading). Ekip yükleme, bir varlık türü için bir sorgu aynı zamanda ilgili varlıkları de yüklediğinde oluşur. Varlık okunmadığınızda ilgili veriler alınır. Bu, genellikle gereken tüm verileri alan tek bir JOIN sorgusuna neden olur. EF Core, bazı Eager yükleme türleri için birden çok sorgu yayımlayacak. Birden çok sorgu verilmesi, EF6 içindeki bazı sorgular için tek bir sorgunun bulunduğu durumda daha verimli olabilir. Eager yüklemesi `Include` ve `ThenInclude` yöntemleriyle birlikte belirtilir.

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)
 
  Bir koleksiyon gezintisi eklendiğinde Eager yüklemesi birden çok sorgu gönderir:

  * Ana sorgu için bir sorgu 
  * Yükleme ağacındaki her koleksiyon "Edge" için bir sorgu.

* Sorguları ile `Load`ayır: Veriler ayrı sorgularda alınabilir ve gezinti özellikleri ' EF Core "düzeltir". "düzeltmeler", EF Core gezinti özelliklerini otomatik olarak dolduran anlamına gelir. İle `Load` ayrı sorgular, yükleme sonrasında yükleme öncesi yükleme gibidir.

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

  Not: EF Core, daha önce bağlam örneğine yüklenmiş olan diğer varlıklara gezinti özelliklerini otomatik olarak düzeltir. Bir gezinti özelliği için veriler açıkça dahil edilmese bile, ilgili varlıkların bazıları veya tümü daha önce yüklenmişse Özellik yine de doldurulabilir.

* [Açık yükleme](/ef/core/querying/related-data#explicit-loading). Varlık ilk kez okunmadıysa ilgili veriler alınmadı. Gerektiğinde ilgili verileri almak için kodun yazılması gerekir. Ayrı sorgularla açık yükleme, VERITABANıNA birden çok sorgu gönderilmesine neden olur. Açık yükleme ile kod, yüklenecek gezinti özelliklerini belirtir. Açık yükleme yapmak için yönteminikullanın.`Load` Örneğin:

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* [Yavaş yükleme](/ef/core/querying/related-data#lazy-loading). [Sürüm 2,1 ' de EF Core geç yükleme eklendi](/ef/core/querying/related-data#lazy-loading). Varlık ilk kez okunmadıysa ilgili veriler alınmadı. Gezinti özelliğine ilk kez erişildiğinde, bu gezinti özelliği için gereken veriler otomatik olarak alınır. Bir gezinti özelliğine ilk kez erişildiğinde bir sorgu VERITABANıNA gönderilir.

* `Select` İşleci yalnızca gerekli ilgili verileri yükler.

## <a name="create-a-course-page-that-displays-department-name"></a>Bölüm adını görüntüleyen bir kurs sayfası oluşturma

Kurs varlığı, `Department` varlığı içeren bir gezinti özelliği içerir. `Department` Varlık, kursun atandığı departmanı içerir.

Bir kurs listesinde atanan departmanın adını göstermek için:

* `Department` Varlıktaki `Name` özelliği alın.
* Varlık, `Course.Department` gezinti özelliğinden gelir. `Department`

![Kurs. Departmanı](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>Kurs modelini temklesi

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Bölümündeki yönergeleri [Öğrenci modeli iskelesini](xref:data/ef-rp/intro#scaffold-the-student-model) ve `Course` model sınıfı için.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

 Şu komutu çalıştırın:

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

Önceki komut iskelesini kurar `Course` modeli. Projeyi Visual Studio'da açın.

*Pages/kurslar/Index. cshtml. cs* dosyasını açın ve `OnGetAsync` metodunu inceleyin. Yapı iskelesi altyapısı, `Department` gezinti özelliği için belirtilen Eager 'yi belirtti. `Include` Yöntemi Eager yüklemeyi belirtir.

Uygulamayı çalıştırın ve **Kurslar** bağlantısını seçin. Departman sütunu, yararlı olmayan `DepartmentID`öğesini görüntüler.

`OnGetAsync` Yöntemi aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

Yukarıdaki kod ekler `AsNoTracking`. `AsNoTracking`Döndürülen varlıklar izlenmediğinden performansı geliştirir. Geçerli bağlamda güncelleştirilmemiş oldukları için varlıklar izlenmiyor.

*Pages/kurslar/Index. cshtml* 'yi aşağıdaki vurgulanmış işaretlerle güncelleştirin:

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Yapı iskelesi kodunda aşağıdaki değişiklikler yapılmıştır:

* Başlık dizinden kurslar olarak değiştirildi.
* `CourseID` Özellik değerini gösteren bir **sayı** sütunu eklendi. Birincil anahtarlar, genellikle son kullanıcılara anlamlı olduklarından, varsayılan olarak yapı iskelesi göstermemektedir. Ancak, bu durumda birincil anahtar anlamlı olur.
* Departman adını göstermek için **Departman** sütunu değiştirildi. Kod, `Department` gezinti özelliğine `Name` yüklenen `Department` varlığın özelliğini görüntüler:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Uygulamayı çalıştırın ve bölüm adlarıyla listeyi görmek için **Kurslar** sekmesini seçin.

![Kurslar Dizin sayfası](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Select ile ilgili verileri yükleme

`OnGetAsync` Yöntemi ,`Include` yöntemiyle ilgili verileri yükler:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`Select` İşleci yalnızca gerekli ilgili verileri yükler. Tek öğeler için, `Department.Name` örneğin bir SQL iç birleşimi kullanır. Koleksiyonlar için başka bir veritabanı erişimi kullanır, ancak koleksiyonlar üzerindeki `Include` işleci bu şekilde yapar.

Aşağıdaki kod, `Select` yöntemiyle ilgili verileri yükler:

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`CourseViewModel`Şunları yapın:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Tüm örnek için bkz. [ındexselect. cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) ve [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) .

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Kurslar ve kayıtları gösteren bir eğitmenler sayfası oluşturun

Bu bölümde, Eğitmenler sayfası oluşturulur.

<a name="IP"></a>
![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index.png)

Bu sayfa aşağıdaki yollarla ilgili verileri okur ve görüntüler:

* Eğitmenler listesi, `OfficeAssignment` varlıktaki ilgili verileri (önceki görüntüde Office) görüntüler. `Instructor` Ve`OfficeAssignment` varlıkları bire sıfır veya-bir ilişkidir. `OfficeAssignment` Varlıklar için Eager yüklemesi kullanılır. Eager yüklemesi, ilgili verilerin görüntülenmesi gerektiğinde genellikle daha etkilidir. Bu durumda, Eğitmenler için Office atamaları görüntülenir.
* Kullanıcı bir eğitmen (önceki görüntüde haruı) seçtiğinde ilgili `Course` varlıklar görüntülenir. `Instructor` Ve`Course` varlıkları çoktan çoğa bir ilişkidir. Eager yüklemesi, `Course` varlıklar ve ilgili `Department` varlıklar için kullanılır. Bu durumda, yalnızca seçili eğitmen için kurslar gerektiğinden ayrı sorgular daha verimli olabilir. Bu örnek, gezinti özelliklerinde olan varlıklarda gezinti özellikleri için Eager yükleme 'nin nasıl kullanılacağını gösterir.
* Kullanıcı bir kurs seçtiğinde (önceki görüntüde Chemistry), `Enrollments` varlıktaki ilgili veriler görüntülenir. Önceki görüntüde öğrenci adı ve sınıf görüntülenir. `Course` Ve`Enrollment` varlıkları bire çok ilişkidir.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Eğitmen dizini görünümü için bir görünüm modeli oluşturun

Eğitmenler sayfasında, üç farklı tablodan alınan veriler gösterilir. Üç tabloyu temsil eden üç varlığı içeren bir görünüm modeli oluşturulur.

*SchoolViewModels* klasöründe, aşağıdaki kodla *InstructorIndexData.cs* oluşturun:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Eğitmen modelini scafkatlama

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Bölümündeki yönergeleri [Öğrenci modeli iskelesini](xref:data/ef-rp/intro#scaffold-the-student-model) ve `Instructor` model sınıfı için.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

 Şu komutu çalıştırın:

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

Önceki komut iskelesini kurar `Instructor` modeli. Uygulamayı çalıştırın ve eğitmenler sayfasına gidin.

*Pages/eğitmenler/Index. cshtml. cs* öğesini şu kodla değiştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

`OnGetAsync` Yöntemi, seçilen eğitmenin kimliği için isteğe bağlı rota verilerini kabul eder.

*Pages/eğitmenler/Index. cshtml. cs* dosyasındaki sorguyu inceleyin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

Sorgunun iki içerme vardır:

* `OfficeAssignment`: [Eğitmenler görünümünde](#IP)görüntülenir.
* `CourseAssignments`: Bu, kurs dünyasını sağlar.

### <a name="update-the-instructors-index-page"></a>Eğitmenler Dizin sayfasını Güncelleştir

*Sayfaları/eğitmenler/Index. cshtml* 'yi şu biçimlendirmeyle güncelleştirin:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

Önceki biçimlendirme, aşağıdaki değişiklikleri yapar:

* Güncelleştirmeleri `page` gelen yönerge `@page` için `@page "{id:int?}"`. `"{id:int?}"`bir yol şablonudur. Yol şablonu, verileri yönlendirmek için URL 'deki tamsayı Sorgu dizelerini değiştirir. Örneğin, yalnızca `@page` yönergeyle bir eğitmenin **Select** bağlantısına tıklanması aşağıdakine benzer bir URL oluşturur:

  `http://localhost:1234/Instructors?id=2`

  Sayfa yönergesi `@page "{id:int?}"`olduğunda, önceki URL şu şekilde olur:

  `http://localhost:1234/Instructors/2`

* Sayfa başlığı **eğitmenler**' dir.
* Yalnızca null olmaması halinde `item.OfficeAssignment` görüntülenen bir **Office** sütunu eklendi. `item.OfficeAssignment.Location` Bu bire sıfır veya-bir ilişki olduğundan ilgili bir OfficeAssignment varlığı bulunmayabilir.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Her bir eğitmen tarafından taders kurslarını görüntüleyen bir **Kurslar** sütunu eklendi. Bu Razor sözdizimi hakkında daha fazla bilgi için bkz. [açık hat geçişi `@:` ](xref:mvc/views/razor#explicit-line-transition-with-) .

* Seçilen eğitmenin `tr` öğesine dinamik olarak `class="success"` ekleyen kod eklendi. Bu, bir önyükleme sınıfı kullanarak seçili satır için bir arka plan rengi ayarlar.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* **Select**etiketli yeni bir köprü eklendi. Bu bağlantı, seçilen eğitmenin kimliğini `Index` yönteme gönderir ve bir arka plan rengi ayarlar.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Uygulamayı çalıştırın ve **eğitmenler** sekmesini seçin. Sayfa, `Location` ilgili `OfficeAssignment` varlıktaki (Office) öğesini görüntüler. Eğer OfficeAssignment ' null ise boş bir tablo hücresi görüntülenir.

![Eğitmenler Dizin sayfası seçili öğe yok](read-related-data/_static/instructors-index-no-selection.png)

**Seç** bağlantısına tıklayın. Satır stili değişir.

### <a name="add-courses-taught-by-selected-instructor"></a>Seçili eğitmen 'e göre kurslar ekleyin

*Pages/eğitmenler/Index. cshtml. cs* dosyasındaki yöntemiaşağıdakikodlagüncelleştirin:`OnGetAsync`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

Ekleyemiyorum`public int CourseID { get; set; }`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

Güncelleştirilmiş sorguyu inceleyin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

Önceki sorgu `Department` varlıkları ekler.

Aşağıdaki kod, bir eğitmen seçildiğinde (`id != null`) yürütülür. Seçilen eğitmen, görünüm modelindeki eğitmenler listesinden alınır. Görünüm modelinin `Courses` özelliği, bu eğitmenin `CourseAssignments` gezinti özelliğinden alınan `Course` varlıklarla birlikte yüklenir.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

`Where` Yöntemi bir koleksiyon döndürür. Önceki `Where` yöntemde yalnızca tek `Instructor` bir varlık döndürülür. Yöntemi, koleksiyonu tek `Instructor` bir varlığa dönüştürür. `Single` Varlık, `CourseAssignments` özelliğine erişim sağlar. `Instructor` `CourseAssignments`ilgili `Course` varlıklara erişim sağlar.

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

Koleksiyonda yalnızca bir öğe olduğunda Yöntembirkoleksiyondakullanılır.`Single` Koleksiyon boşsa veya birden fazla öğe varsa Yöntembirözeldurumoluşturur.`Single` Koleksiyon boşsa varsayılan `SingleOrDefault`bir değer (Bu durumda null) döndüren alternatif bir alternatiftir. Boş `SingleOrDefault` bir koleksiyonda kullanma:

* Bir özel durum sonucu oluşur (null başvuru üzerinde bir `Courses` Özellik bulmaya çalışırken).
* Özel durum iletisi sorunun nedenini daha az gösterir.

Aşağıdaki kod, bir kurs seçildiğinde görünüm modelinin `Enrollments` özelliğini doldurur:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

*Pages/eğitmenler/Index. cshtml* Razor sayfasının sonuna aşağıdaki biçimlendirmeyi ekleyin:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

Yukarıdaki biçimlendirme, bir eğitmen seçildiğinde bir eğitmenin ilgili kursların bir listesini görüntüler.

Uygulamayı test edin. Eğitmenler sayfasında bir **seçme** bağlantısına tıklayın.

![Eğitmenler Dizin sayfası eğitmeni seçildi](read-related-data/_static/instructors-index-instructor-selected.png)

### <a name="show-student-data"></a>Öğrenci verilerini göster

Bu bölümde, uygulama seçili bir kurs için öğrenci verilerini gösterecek şekilde güncelleştirilir.

`OnGetAsync` *Pages/eğitmenler/Index. cshtml. cs* içindeki yöntemdeki sorguyu aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Güncelleştirme *sayfaları/eğitmenler/Index. cshtml*. Aşağıdaki biçimlendirmeyi dosyanın sonuna ekleyin:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

Yukarıdaki biçimlendirme, seçili kursa kayıtlı öğrencilerin bir listesini görüntüler.

Sayfayı yenileyin ve bir eğitmen seçin. Kayıtlı öğrenciler ve bunların onların listesini görmek için bir kurs seçin.

![Eğitmenler Dizin sayfası eğitmeni ve kursu seçildi](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Tek kullanımı

Yöntemi yöntemi ayrı çağırmak yerine `Where` koşulu geçirebilir: `Where` `Single`

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Yukarıdaki `Single` yaklaşım, kullanarak `Where`herhangi bir avantaj sağlamaz. Bazı geliştiriciler `Single` yaklaşım stilini tercih eder.

## <a name="explicit-loading"></a>Açık yükleme

Geçerli kod ve `Enrollments` `Students`için Eager yüklemeyi belirtir:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Kullanıcıların bir kursa kayıtları nadiren görmek istediğini varsayalım. Bu durumda, bir iyileştirme yalnızca isteniyorsa kayıt verilerini yüklemek olacaktır. Bu bölümde `OnGetAsync` , ve `Students`' nin `Enrollments` açık yüklemesini kullanacak şekilde güncelleştirilir.

`OnGetAsync` Aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

Yukarıdaki kod, kayıt ve öğrenci verileri için *Thenınclude* Yöntem çağrılarını bırakır. Bir kurs seçilirse vurgulanan kod şunu alır:

* Seçili kurs için varlıklar. `Enrollment`
* Her biri`Enrollment`için varlıklar. `Student`

Yukarıdaki kod açıklamalarının `.AsNoTracking()`olduğunu fark edin. Gezinti özellikleri yalnızca izlenen varlıklar için açık bir şekilde yüklenebilir.

Uygulamayı test edin. Bir kullanıcının perspektifinden, uygulama önceki sürümle aynı şekilde davranır.

Sonraki öğreticide ilgili verileri güncelleştirme gösterilmektedir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü (part1)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [Bu öğreticinin YouTube sürümü (part2)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[Önceki](xref:data/ef-rp/complex-data-model)İleri
>[](xref:data/ef-rp/update-related-data)
