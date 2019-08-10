---
title: ASP.NET Core ile EF Core Razor Pages-Ilgili verileri güncelleştirme-7/8
author: rick-anderson
description: Bu öğreticide, yabancı anahtar alanlarını ve gezinti özelliklerini güncelleştirerek ilgili verileri güncelleyerek.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 72fb9165010f6852e2ad577b36efbeee55c76def
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914143"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a>ASP.NET Core ile EF Core Razor Pages-Ilgili verileri güncelleştirme-7/8

, [Tom Dykstra](https://github.com/tdykstra)ve [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Bu öğreticide ilgili verileri güncelleştirme gösterilmektedir. Aşağıdaki çizimler tamamlanan sayfalardan bazılarını göstermektedir.

![Kurs düzenleme sayfası](update-related-data/_static/course-edit30.png)
![eğitmeni düzenleme sayfası](update-related-data/_static/instructor-edit-courses30.png)

## <a name="update-the-course-create-and-edit-pages"></a>Kurs oluşturma ve düzenleme sayfalarını güncelleştirme

Kurs oluşturma ve düzenleme sayfaları için yapı iskelesi kodu, departman KIMLIĞI (tamsayı) gösteren bir departman açılan listesi içerir. Açılan listede bölüm adı gösterilmeli, bu nedenle her iki sayfanın da bir departman adları listesi olması gerekir. Bu listeyi sağlamak için, oluşturma ve düzenleme sayfaları için bir temel sınıf kullanın.

### <a name="create-a-base-class-for-course-create-and-edit"></a>Kurs oluşturma ve düzenleme için bir temel sınıf oluşturun

Aşağıdaki kodla bir *Pages/kurslar/DepartmentNamePageModel. cs* dosyası oluşturun:

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

Yukarıdaki kod, bölüm adlarının listesini içeren bir [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) oluşturur. Belirtilmişse, bu departman `SelectList`öğesinde seçilir. `selectedDepartment`

Oluşturma ve düzenleme sayfa modeli sınıfları öğesinden `DepartmentNamePageModel`türetilir.

### <a name="update-the-course-create-page-model"></a>Kursu güncelleştirme sayfa modeli oluşturma

Bir kurs bir departmana atanır. Oluşturma ve düzenleme sayfaları için temel sınıf, departmanı seçmek için `SelectList` bir sağlar. Yabancı anahtar (FK) özelliğini kullanan `SelectList` açılan liste. `Course.DepartmentID` EF Core, `Course.DepartmentID` `Department` gezinti özelliğini yüklemek için FK kullanır.

![Kurs oluştur](update-related-data/_static/ddl30.png)

*Sayfaları/kursları/oluşturma. cshtml. cs* dosyasını şu kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

Yukarıdaki kod:

* Türetiliyor `DepartmentNamePageModel`.
* `TryUpdateModelAsync` [Aşırı nakletmeyi](xref:data/ef-rp/crud#overposting)engellemek için kullanır.
* Kaldırır `ViewData["DepartmentID"]`. `DepartmentNameSL`temel sınıftan türü kesin belirlenmiş bir modeldir ve Razor sayfası tarafından kullanılır. Kesin olarak belirlenmiş modeller, kesin olarak yazılan zayıf bir şekilde tercih edilir. Daha fazla bilgi için bkz. [zayıf yazılmış veriler (ViewData ve ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-course-create-razor-page"></a>Kursu güncelleştirme Razor oluşturma sayfası

*Sayfaları/kursları/Create. cshtml* 'yi aşağıdaki kodla güncelleştirin:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

Yukarıdaki kod aşağıdaki değişiklikleri yapar:

* **DepartmentID** etiketini **departmana**dönüştürür.
* `"ViewBag.DepartmentID"` İle`DepartmentNameSL` değiştirir (taban sınıfından).
* "Departmanı Seç" seçeneğini ekler. Bu değişiklik, ilk departman yerine henüz bir departman seçilmedikçe açılan kutuda "departmanı Seç" i işler.
* Departman seçili olmadığında bir doğrulama iletisi ekler.

Razor sayfası [seçme etiketi yardımcısını](xref:mvc/views/working-with-forms#the-select-tag-helper)kullanır:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Oluştur sayfasını test edin. Oluştur sayfası departman KIMLIĞI yerine departman adını görüntüler.

### <a name="update-the-course-edit-page-model"></a>Kurs düzenleme sayfası modelini güncelleştirme

*Pages/kurslar/Edit. cshtml. cs* dosyasını aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

Değişiklikler, oluşturma sayfası modelinde yapılanlarla benzerdir. Yukarıdaki kodda, `PopulateDepartmentsDropDownList` bu departmanı açılan listede seçen departman kimliği ' nde geçirilir.

### <a name="update-the-course-edit-razor-page"></a>Kurs düzenleme Razor sayfasını güncelleştirme

*Pages/kurslar/Edit. cshtml* dosyasını aşağıdaki kodla güncelleştirin:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Yukarıdaki kod aşağıdaki değişiklikleri yapar:

* Kurs KIMLIĞINI görüntüler. Genellikle bir varlığın birincil anahtarı (PK) gösterilmez. PKs 'ler genellikle kullanıcılara daha az anlamlı olur. Bu durumda, PK kurs numarasıdır.
* Bölüm açılan başlığını **DepartmentID** ' dan **departmana**dönüştürür.
* `"ViewBag.DepartmentID"` İle`DepartmentNameSL` değiştirir (taban sınıfından).

Sayfa, kurs numarası için gizli bir`<input type="hidden">`alan () içerir. `<label>` Etiket`asp-for="Course.CourseID"` Yardımcısı ekleme, gizli alan gereksinimini ortadan kaldırmaz. `<input type="hidden">`Kullanıcı **Kaydet**' e tıkladığında, gönderilen veriler için kurs numarasının dahil olması gerekir.

## <a name="update-the-course-details-and-delete-pages"></a>Kurs ayrıntılarını güncelleştirme ve sayfaları silme

[Anotracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) , izleme gerekli olmadığında performansı iyileştirebilir.

### <a name="update-the-course-page-models"></a>Kurs sayfası modellerini güncelleştirme

*Sayfa/kurslar/delete. cshtml. cs* öğesini aşağıdaki kodla `AsNoTracking`güncelleştirin:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

*Sayfalar/kurslar/details. cshtml. cs* dosyasında aynı değişikliği yapın:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a>Kurs Razor sayfalarını güncelleştirme

*Pages/kurslar/delete. cshtml* dosyasını aşağıdaki kodla güncelleştirin:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

Ayrıntılar sayfasında aynı değişiklikleri yapın.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a>Kurs sayfalarını test etme

Oluşturma, düzenleme, ayrıntıları ve silme sayfalarını test edin.

## <a name="update-the-instructor-create-and-edit-pages"></a>Eğitmen oluşturma ve düzenleme sayfalarını güncelleştirme

Eğitmenler, istediğiniz sayıda kurs öğretebilir. Aşağıdaki görüntüde, bir dizi kurs onay kutusu ile eğitmen düzenleme sayfası gösterilmektedir.

![Kurslar ile eğitmen düzenleme sayfası](update-related-data/_static/instructor-edit-courses30.png)

Onay kutuları, bir eğitmenin atandığı kurslara değişiklikler sağlar. Veritabanındaki her kurs için bir onay kutusu görüntülenir. Eğitmenin atandığı kurslar seçilidir. Kullanıcı kurs atamalarını değiştirmek için onay kutularını seçebilir veya temizleyebilir. Kurs sayısı çok büyükse, farklı bir kullanıcı arabirimi daha iyi çalışabilir. Ancak burada gösterilen çoktan çoğa ilişkiyi yönetme yöntemi değişmez. İlişki oluşturmak veya silmek için bir JOIN varlığını işlersiniz.

### <a name="create-a-class-for-assigned-courses-data"></a>Atanan kurslar verileri için bir sınıf oluşturma

Aşağıdaki kodla *SchoolViewModels/AssignedCourseData. cs* oluşturun:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

Sınıfı `AssignedCourseData` , bir eğitmene atanan kurslar için onay kutularını oluşturmak üzere verileri içerir.

### <a name="create-an-instructor-page-model-base-class"></a>Bir eğitmen sayfa modeli temel sınıfı oluşturma

*Pages/eğitmenler/Komutctorcoursespagemodel. cs* temel sınıfını oluşturun:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

, `InstructorCoursesPageModel` Düzenleme ve oluşturma sayfa modelleri için kullanacağınız temel sınıftır. `PopulateAssignedCourseData``Course` doldurmak`AssignedCourseDataList`için tüm varlıkları okur. Her kurs için kod, başlığı ve eğitmenin `CourseID`kursa atanıp atanmadığını belirler. Bir [diyez kümesi](/dotnet/api/system.collections.generic.hashset-1) etkili aramalar için kullanılır.

Razor sayfasında bir kurs varlıkları koleksiyonu olmadığından, model Bağlayıcısı `CourseAssignments` gezinti özelliğini otomatik olarak güncelleştiremez. `CourseAssignments` Gezinti özelliğini güncelleştirmek için model cildi kullanmak yerine, bunu yeni `UpdateInstructorCourses` yöntemde yapmanız gerekir. Bu nedenle, `CourseAssignments` özelliği model bağlamadan hariç bırakmanız gerekir. Bu, beyaz liste aşırı yüklemesini kullandığınız ve `TryUpdateModel` `CourseAssignments` içerme listesinde olmadığı için çağıran kodda herhangi bir değişiklik yapılmasını gerektirmez.

Hiçbir onay kutusu seçili değilse, içindeki `UpdateInstructorCourses` kod `CourseAssignments` gezinti özelliğini boş bir koleksiyonla başlatır ve döndürür:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

Kod daha sonra, veritabanındaki tüm kurslardan geçer ve bu her kursu, sayfada seçili olanlar ile ilgili olarak, eğitmene atanmış olanlara karşı denetler. Etkili aramaları kolaylaştırmak için, ikinci iki koleksiyon `HashSet` nesnelerde depolanır.

Kurs onay kutusu seçilmişse ancak kurs, `Instructor.CourseAssignments` gezinti özelliğinde değilse kurs, Gezinti özelliğindeki koleksiyona eklenir.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

Kurs onay kutusu seçili değilse, ancak kurs `Instructor.CourseAssignments` gezinti özelliği ise, kurs, gezinti özelliğinden kaldırılır.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a>Office konumunu işle

Düzenleme sayfasının işlemesi gereken başka bir ilişki ise, eğitmen varlığının `OfficeAssignment` varlığa sahip olduğu bire sıfır veya-bir ilişkidir. Eğitmen düzenleme kodu aşağıdaki senaryoları işlemelidir: 

* Kullanıcı Office atamasını temizlediğinde `OfficeAssignment` varlığı silin.
* Kullanıcı bir Office ataması girerse ve boşsa, yeni `OfficeAssignment` bir varlık oluşturun.
* Kullanıcı Office atamasını değiştirirse `OfficeAssignment` varlığı güncelleştirin.

### <a name="update-the-instructor-edit-page-model"></a>Eğitmen düzenleme sayfası modelini güncelleştirme

*Pages/eğitmenler/Edit. cshtml. cs* dosyasını aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

Yukarıdaki kod:

* `OfficeAssignment`, `Instructor` ,Ve`CourseAssignment.Course` gezinme özellikleri için Eager yükleme kullanarak geçerli varlığı veritabanından alır. `CourseAssignment`
* Alınan `Instructor` varlığı model Ciltçideki değerlerle güncelleştirir. `TryUpdateModel`[fazla nakletmeyi](xref:data/ef-rp/crud#overposting)önler.
* Office konumu boşsa, null olarak ayarlar `Instructor.OfficeAssignment` . Null `Instructor.OfficeAssignment` olduğunda, `OfficeAssignment` tablodaki ilgili satır silinir.
* Görüntüleme modeli `OnGetAsync` sınıfını kullanarak onay kutuları `PopulateAssignedCourseData` için bilgi sağlamak üzere ' de çağırır. `AssignedCourseData`
* Onay `UpdateInstructorCourses` kutularından `OnPostAsync` düzenlenmekte olan eğitmen varlığına bilgi uygulamak için ' de çağırır.
* Başarısız olursa `UpdateInstructorCourses` , `OnPostAsync` ve ' de `PopulateAssignedCourseData` çağırır. `TryUpdateModel` Bu yöntem çağrıları, bir hata iletisiyle yeniden görüntülendiğinde sayfaya girilen atanan kurs verilerini geri yükler.

### <a name="update-the-instructor-edit-razor-page"></a>Eğitmen düzenleme Razor sayfasını güncelleştirme

*Pages/eğitmenler/Edit. cshtml* dosyasını aşağıdaki kodla güncelleştirin:

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

Yukarıdaki kod, üç sütun içeren bir HTML tablosu oluşturur. Her sütunda, kurs numarasını ve başlığını içeren bir CheckBox ve bir açıklamalı alt yazı vardır. Onay kutularının hepsi aynı ada ("Selectedkurslar") sahiptir. Aynı adı kullanmak model cildi bir grup olarak kabul etmek üzere bilgilendirir. Her onay kutusunun değer özniteliği olarak `CourseID`ayarlanır. Sayfa gönderildiğinde, model Ciltçi yalnızca seçili onay kutularının `CourseID` değerlerinden oluşan bir dizi geçirir.

Onay kutuları başlangıçta işlendiğinde, eğitmen 'e atanan kurslar seçilir.

Not: Eğitim kursu verilerini düzenlemek için buradaki yaklaşım, sınırlı sayıda kurs olduğunda iyi bir şekilde gerçekleştirilir. Çok daha büyük olan koleksiyonlar için, farklı bir kullanıcı arabirimi ve farklı bir güncelleştirme yöntemi daha erişilebilir ve verimli olacaktır.

Uygulamayı çalıştırın ve güncelleştirilmiş eğitmenler düzenleme sayfasını test edin. Bazı kurs atamalarını değiştirin. Değişiklikler Dizin sayfasında yansıtılır.

### <a name="update-the-instructor-create-page"></a>Eğitmen oluştur sayfasını güncelleştirme

, Düzenleme sayfasına benzer kodla, bir sayfa modeli ve Razor sayfası oluşturma adlı eğitmeni güncelleştirin:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

Eğitmen oluşturma sayfasını test edin.

## <a name="update-the-instructor-delete-page"></a>Eğitmen silme sayfasını Güncelleştir

*Sayfaları/eğitmenler/delete. cshtml. cs* dosyasını şu kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

Yukarıdaki kod aşağıdaki değişiklikleri yapar:

* `CourseAssignments` Gezinti özelliği için Eager yüklemesi kullanır. `CourseAssignments`eğitmen silindiğinde, dahil edilmiş veya silinmemelidir. Bunları okumaktan kaçınmak için, veritabanında basamaklı silme 'yı yapılandırın.

* Silinecek eğitmen herhangi bir departmanların Yöneticisi olarak atanırsa, bu departmanlardan eğitmen atamasını kaldırır.

Uygulamayı çalıştırın ve silme sayfasını test edin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="step-by-step"]
> [Önceki öğretici](xref:data/ef-rp/read-related-data)
> [sonraki öğretici](xref:data/ef-rp/concurrency)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğreticide ilgili verilerin güncelleştirilmesi gösterilmektedir. Olamaz çözmenize, sorunlarla karşılaşırsanız, [indirin veya tamamlanmış uygulamayı görüntüleyin.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Yükleme yönergeleri](xref:index#how-to-download-a-sample).

Aşağıdaki çizimler tamamlanan sayfalardan bazılarını göstermektedir.

![Kurs düzenleme sayfası](update-related-data/_static/course-edit.png)
![eğitmeni düzenleme sayfası](update-related-data/_static/instructor-edit-courses.png)

Kurs oluşturma ve düzenleme sayfalarını inceleyin ve test edin. Yeni bir kurs oluşturun. Departman, adı değil, birincil anahtarı (bir tamsayı) tarafından seçilir. Yeni kursu düzenleyin. Sınamayı bitirdiğinizde yeni kursu silin.

## <a name="create-a-base-class-to-share-common-code"></a>Ortak kod paylaşmak için bir temel sınıf oluşturun

Kurslar/oluştur ve kurslar/Düzenle sayfaları, her birinin departman adları listesine ihtiyacı vardır. Oluşturma ve düzenleme sayfaları için *Pages/kurslar/DepartmentNamePageModel. cshtml. cs* temel sınıfını oluşturun:

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Yukarıdaki kod, bölüm adlarının listesini içeren bir [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) oluşturur. Belirtilmişse, bu departman `SelectList`öğesinde seçilir. `selectedDepartment`

Oluşturma ve düzenleme sayfa modeli sınıfları öğesinden `DepartmentNamePageModel`türetilir.

## <a name="customize-the-courses-pages"></a>Kurslar sayfalarını özelleştirme

Yeni bir kurs varlığı oluşturulduğunda, mevcut bir departmanla bir ilişkisi olmalıdır. Bir kurs oluştururken bir departman eklemek için, oluşturma ve düzenleme için temel sınıf, departmanı seçmeye yönelik bir açılan liste içerir. Açılan liste, `Course.DepartmentID` yabancı anahtar (FK) özelliğini ayarlar. EF Core, `Course.DepartmentID` `Department` gezinti özelliğini yüklemek için FK kullanır.

![Kurs oluştur](update-related-data/_static/ddl.png)

Oluşturma sayfası modelini aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

Yukarıdaki kod:

* Türetiliyor `DepartmentNamePageModel`.
* `TryUpdateModelAsync` [Aşırı nakletmeyi](xref:data/ef-rp/crud#overposting)engellemek için kullanır.
* `ViewData["DepartmentID"]` İle`DepartmentNameSL` değiştirir (taban sınıfından).

`ViewData["DepartmentID"]`, türü kesin belirlenmiş `DepartmentNameSL`olan ile değiştirilmiştir. Kesin olarak belirlenmiş modeller, kesin olarak yazılan zayıf bir şekilde tercih edilir. Daha fazla bilgi için bkz. [zayıf yazılmış veriler (ViewData ve ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Kurslar oluşturma sayfasını güncelleştirme

*Sayfaları/kursları/Create. cshtml* 'yi aşağıdaki kodla güncelleştirin:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Önceki biçimlendirme, aşağıdaki değişiklikleri yapar:

* **DepartmentID** etiketini **departmana**dönüştürür.
* `"ViewBag.DepartmentID"` İle`DepartmentNameSL` değiştirir (taban sınıfından).
* "Departmanı Seç" seçeneğini ekler. Bu değişiklik, ilk departman yerine "departmanı Seç" i işler.
* Departman seçili olmadığında bir doğrulama iletisi ekler.

Razor sayfası [seçme etiketi yardımcısını](xref:mvc/views/working-with-forms#the-select-tag-helper)kullanır:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Oluştur sayfasını test edin. Oluştur sayfası departman KIMLIĞI yerine departman adını görüntüler.

### <a name="update-the-courses-edit-page"></a>Kurslar düzenleme sayfasını güncelleştirin.

*Pages/kurslar/Edit. cshtml. cs* dosyasındaki kodu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

Değişiklikler, oluşturma sayfası modelinde yapılanlarla benzerdir. Yukarıdaki kodda `PopulateDepartmentsDropDownList` , açılan listede belirtilen departmanı belirleyen departman kimliği ' nde geçirilir.

*Pages/kurslar/Edit. cshtml* 'yi şu biçimlendirmeyle güncelleştirin:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Önceki biçimlendirme, aşağıdaki değişiklikleri yapar:

* Kurs KIMLIĞINI görüntüler. Genellikle bir varlığın birincil anahtarı (PK) gösterilmez. PKs 'ler genellikle kullanıcılara daha az anlamlı olur. Bu durumda, PK kurs numarasıdır.
* **DepartmentID** etiketini **departmana**dönüştürür.
* `"ViewBag.DepartmentID"` İle`DepartmentNameSL` değiştirir (taban sınıfından).

Sayfa, kurs numarası için gizli bir`<input type="hidden">`alan () içerir. `<label>` Etiket`asp-for="Course.CourseID"` Yardımcısı ekleme, gizli alan gereksinimini ortadan kaldırmaz. `<input type="hidden">`Kullanıcı **Kaydet**' e tıkladığında, gönderilen veriler için kurs numarasının dahil olması gerekir.

Güncelleştirilmiş kodu test edin. Kurs oluşturun, düzenleyin ve silin.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Ayrıntılara AsNoTracking ekleme ve sayfa modellerini silme

[Anotracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) , izleme gerekli olmadığında performansı iyileştirebilir. Sil `AsNoTracking` ve Ayrıntılar sayfa modeline ekleyin. Aşağıdaki kod, güncelleştirilmiş silme sayfası modelini göstermektedir:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

*Pages/kurslar/details. cshtml. cs* dosyasındaki yöntemigüncelleştirin:`OnGetAsync`

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Silme ve Ayrıntılar sayfalarını değiştirme

Razor Sil sayfasını aşağıdaki biçimlendirmeyle güncelleştirin:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Ayrıntılar sayfasında aynı değişiklikleri yapın.

### <a name="test-the-course-pages"></a>Kurs sayfalarını test etme

Test oluşturma, düzenleme, Ayrıntılar ve silme.

## <a name="update-the-instructor-pages"></a>Eğitmen sayfalarını güncelleştirme

Aşağıdaki bölümlerde, eğitmen sayfaları günceldir.

### <a name="add-office-location"></a>Office konumu Ekle

Bir eğitmen kaydını düzenlediğinizde, eğitmenin Office atamasını güncelleştirmek isteyebilirsiniz. `Instructor` Varlığın `OfficeAssignment` varlıkla bire sıfır veya-bir ilişkisi vardır. Eğitmen kodu şu şekilde olmalıdır:

* Kullanıcı Office atamasını temizlediğinde `OfficeAssignment` varlığı silin.
* Kullanıcı bir Office ataması girerse ve boşsa, yeni `OfficeAssignment` bir varlık oluşturun.
* Kullanıcı Office atamasını değiştirirse `OfficeAssignment` varlığı güncelleştirin.

Eğitmenler düzenleme sayfası modelini aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

Yukarıdaki kod:

* Gezinti`OfficeAssignment` özelliği için `Instructor` Eager yüklemesini kullanarak geçerli varlığı veritabanından alır.
* Alınan `Instructor` varlığı model Ciltçideki değerlerle güncelleştirir. `TryUpdateModel`[fazla nakletmeyi](xref:data/ef-rp/crud#overposting)önler.
* Office konumu boşsa, null olarak ayarlar `Instructor.OfficeAssignment` . Null `Instructor.OfficeAssignment` olduğunda, `OfficeAssignment` tablodaki ilgili satır silinir.

### <a name="update-the-instructor-edit-page"></a>Eğitmen düzenleme sayfasını güncelleştirme

*Sayfaları/eğitmenler/Edit. cshtml* dosyasını Office konumuyla güncelleştirin:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Bir eğitmenler ofis konumunu değiştirebildiğinizi doğrulayın.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Eğitmen düzenleme sayfasına kurs atamaları ekleme

Eğitmenler, istediğiniz sayıda kurs öğretebilir. Bu bölümde kurs atamalarını değiştirme olanağını eklersiniz. Aşağıdaki görüntüde güncelleştirilmiş eğitmen düzenleme sayfası gösterilmektedir:

![Kurslar ile eğitmen düzenleme sayfası](update-related-data/_static/instructor-edit-courses.png)

`Course`ve `Instructor` çoktan çoğa ilişkisine sahiptir. İlişki eklemek ve kaldırmak için, `CourseAssignments` JOIN varlık kümesinden varlık ekleyin ve kaldırın.

Onay kutuları, bir eğitmenin atandığı kurslara değişiklikler sağlar. Veritabanındaki her kurs için bir onay kutusu görüntülenir. Eğitmenin atandığı kurslar denetlenir. Kullanıcı kurs atamalarını değiştirmek için onay kutularını seçebilir veya temizleyebilir. Kurs sayısı çok fazlaysa:

* Kursları göstermek için büyük olasılıkla farklı bir kullanıcı arabirimi kullanıyorsunuz.
* İlişki oluşturmak veya silmek için bir JOIN varlığını düzenleme yöntemi değişmez.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Eğitmen sayfaları oluşturma ve düzenleme desteğini desteklemek için sınıflar ekleme

Aşağıdaki kodla *SchoolViewModels/AssignedCourseData. cs* oluşturun:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

Sınıfı `AssignedCourseData` , bir eğitmen tarafından atanan kurslar için onay kutularını oluşturacak verileri içerir.

*Pages/eğitmenler/Komutctorcoursespagemodel. cshtml. cs* temel sınıfını oluşturun:

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

, `InstructorCoursesPageModel` Düzenleme ve oluşturma sayfa modelleri için kullanacağınız temel sınıftır. `PopulateAssignedCourseData``Course` doldurmak`AssignedCourseDataList`için tüm varlıkları okur. Her kurs için kod, başlığı ve eğitmenin `CourseID`kursa atanıp atanmadığını belirler. Bir [diyez kümesi](/dotnet/api/system.collections.generic.hashset-1) , verimli aramalar oluşturmak için kullanılır.

### <a name="instructors-edit-page-model"></a>Eğitmenler sayfa modelini Düzenle

Eğitmen düzenleme sayfası modelini aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

Yukarıdaki kod, Office atama değişikliklerini işler.

Eğitmen Razor görünümünü güncelleştirin:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Kodu Visual Studio 'Ya yapıştırdığınızda, satır sonları kodu kesen bir şekilde değiştirilir. Otomatik biçimlendirmeyi geri almak için CTRL + Z bir kez tuşuna basın. CTRL + Z, burada gördüğünüz gibi görünmeleri için satır sonlarını düzeltir. Girintide kusursuz `@</tr><tr>`olması gerekmez, ancak `@:<td>` `@:</td>`,, ve `@:</tr>` çizgilerinin her biri gösterildiği gibi tek bir satırda olması gerekir. Yeni kod bloğu seçiliyken, yeni kodu mevcut kodla hizalamak için üç kez Tab tuşuna basın. Bu [bağlantı ile](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html)bu hatanın durumunu oylayın veya gözden geçirin.

Yukarıdaki kod, üç sütun içeren bir HTML tablosu oluşturur. Her sütunda bir onay kutusu ve kurs numarasını ve başlığını içeren bir açıklamalı alt yazı vardır. Onay kutularının hepsi aynı ada ("Selectedkurslar") sahiptir. Aynı adı kullanmak model cildi bir grup olarak kabul etmek üzere bilgilendirir. Her onay kutusunun değer özniteliği olarak `CourseID`ayarlanır. Sayfa gönderildiğinde, model Ciltçi yalnızca seçili onay kutularının `CourseID` değerlerinden oluşan bir dizi geçirir.

Onay kutuları başlangıçta işlendiğinde, eğitmenin atandığı kursların denetlenen öznitelikleri vardır.

Uygulamayı çalıştırın ve güncelleştirilmiş eğitmenler düzenleme sayfasını test edin. Bazı kurs atamalarını değiştirin. Değişiklikler Dizin sayfasında yansıtılır.

Not: Eğitim kursu verilerini düzenlemek için buradaki yaklaşım, sınırlı sayıda kurs olduğunda iyi bir şekilde gerçekleştirilir. Çok daha büyük olan koleksiyonlar için, farklı bir kullanıcı arabirimi ve farklı bir güncelleştirme yöntemi daha erişilebilir ve verimli olacaktır.

### <a name="update-the-instructors-create-page"></a>Eğitmenler oluştur sayfasını güncelleştirme

Aşağıdaki kodla, eğitmen sayfa modeli oluştur sayfasını güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

Yukarıdaki kod, *Pages/eğitmenler/Edit. cshtml. cs* koduna benzerdir.

Eğitmen oluşturma Razor sayfasını aşağıdaki biçimlendirmeyle güncelleştirin:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Eğitmen oluşturma sayfasını test edin.

## <a name="update-the-delete-page"></a>Silme sayfası

Delete sayfa modeli aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

Yukarıdaki kod aşağıdaki değişiklikleri yapar:

* `CourseAssignments` Gezinti özelliği için Eager yüklemesi kullanır. `CourseAssignments`eğitmen silindiğinde, dahil edilmiş veya silinmemelidir. Bunları okumaktan kaçınmak için, veritabanında basamaklı silme 'yı yapılandırın.

* Silinecek eğitmen herhangi bir departmanların Yöneticisi olarak atanırsa, bu departmanlardan eğitmen atamasını kaldırır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü (Bölüm 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [Bu öğreticinin YouTube sürümü (Bölüm 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Önceki](xref:data/ef-rp/read-related-data)İleri
> [](xref:data/ef-rp/concurrency)

::: moniker-end