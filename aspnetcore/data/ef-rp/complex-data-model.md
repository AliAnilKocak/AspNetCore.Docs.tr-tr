---
title: ASP.NET Core veri modelinde EF Core ile Razor Pages-5/8
author: rick-anderson
description: Bu öğreticide, daha fazla varlık ve ilişki ekleyin ve biçimlendirme, doğrulama ve eşleme kurallarını belirterek veri modelini özelleştirin.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 411c0874d2b2c6ecadd1da9aff7a093f1e8e525a
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213434"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a>ASP.NET Core veri modelinde EF Core ile Razor Pages-5/8

, [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Önceki öğreticiler, üç varlıktan oluşan temel bir veri modeliyle çalışmıştır. Bu öğreticide:

* Daha fazla varlık ve ilişki eklenmiştir.
* Veri modeli biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirtilerek özelleştirilir.

Tamamlanan veri modeli aşağıdaki çizimde gösterilmiştir:

![Varlık diyagramı](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a>Öğrenci varlık

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

*Modeller/öğrenci. cs* dosyasındaki kodu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

Yukarıdaki kod, bir `FullName` özelliği ekler ve var olan özelliklere aşağıdaki öznitelikleri ekler:

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a>FullName hesaplanmış özelliği

`FullName`, diğer iki özelliği birleştirerek oluşturulmuş bir değer döndüren hesaplanmış bir özelliktir. `FullName` ayarlanamaz, bu nedenle yalnızca bir get erişimcisi vardır. Veritabanında `FullName` sütunu oluşturulmaz.

### <a name="the-datatype-attribute"></a>DataType özniteliği

```csharp
[DataType(DataType.Date)]
```

Öğrenci kayıt tarihleri için, tüm sayfalar şu anda tarihle birlikte tarih ile görüntülenir, ancak yalnızca tarihin ilgili olması gerekir. Veri ek açıklaması özniteliklerini kullanarak, verileri gösteren her sayfada görüntü biçimini giderecek bir kod değişikliği yapabilirsiniz. 

[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) özniteliği, veritabanı iç türünden daha belirgin bir veri türünü belirtir. Bu durumda, tarih ve saat değil yalnızca tarih görüntülenmelidir. Veri [türü numaralandırması](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) , tarih, saat, PhoneNumber, para birimi, emaadresi vb. gibi birçok veri türü sağlar. `DataType` özniteliği Ayrıca uygulamanın türe özgü özellikleri otomatik olarak sağlamasını da sağlayabilir. Örneğin:

* `mailto:` bağlantısı `DataType.EmailAddress`için otomatik olarak oluşturulur.
* Tarih Seçici çoğu tarayıcıda `DataType.Date` için sağlanır.

`DataType` özniteliği HTML 5 `data-` (bir veri Dash) öznitelikleri yayar. `DataType` öznitelikleri doğrulama sağlamaz.

### <a name="the-displayformat-attribute"></a>DisplayFormat özniteliği

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`DataType.Date` görüntülenen tarihin biçimini belirtmiyor. Varsayılan olarak, Tarih alanı sunucunun [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)öğesine göre varsayılan biçimlere göre görüntülenir.

`DisplayFormat` özniteliği, açıkça tarih biçimini belirtmek için kullanılır. `ApplyFormatInEditMode` ayarı, biçimlendirmenin düzenleme kullanıcı arabirimine de uygulanacağını belirtir. Bazı alanlar `ApplyFormatInEditMode`kullanmamalıdır. Örneğin, para birimi simgesi genellikle bir düzenleme metin kutusunda gösterilmemelidir.

`DisplayFormat` özniteliği kendisi tarafından kullanılabilir. `DataType` özniteliğini `DisplayFormat` özniteliğiyle kullanmak genellikle iyi bir fikirdir. `DataType` özniteliği, verilerin semantiğini bir ekranda nasıl işleneceğini tersine alır. `DataType` özniteliği, `DisplayFormat`kullanılamayan aşağıdaki avantajları sağlar:

* Tarayıcı HTML5 özelliklerini etkinleştirebilir. Örneğin, bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını ve istemci tarafı giriş doğrulamasını gösterin.
* Varsayılan olarak tarayıcı, verileri yerel ayara göre doğru biçimi kullanarak işler.

Daha fazla bilgi için bkz. [giriş > etiketi Yardımcısı belgeleri\<](xref:mvc/views/working-with-forms#the-input-tag-helper).

### <a name="the-stringlength-attribute"></a>StringLength özniteliği

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

Veri doğrulama kuralları ve doğrulama hatası iletileri özniteliklerle belirtilebilir. [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) özniteliği, bir veri alanında izin verilen en düşük ve en fazla karakter uzunluğunu belirtir. Gösterilen kod, adları 50 karakterden fazla olmayacak şekilde sınırlar. En küçük dize uzunluğunu ayarlayan bir örnek [daha sonra](#the-required-attribute)gösterilmiştir.

`StringLength` özniteliği de istemci tarafı ve sunucu tarafı doğrulaması sağlar. En küçük değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.

`StringLength` özniteliği, kullanıcının bir ad için boşluk girmesini engellemez. [Cevap içerisinde RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) özniteliği, girişe kısıtlamalar uygulamak için kullanılabilir. Örneğin, aşağıdaki kod ilk karakterin büyük küçük harf olmasını ve geri kalan karakterlerin alfabetik olmasını gerektirir:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

**SQL Server Nesne Gezgini** (ssox) içinde **öğrenci** tablosuna çift tıklayarak öğrenci tablosu tasarımcısını açın.

![Geçişle önce SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-before-migration.png)

Önceki görüntüde `Student` tablo şeması gösterilmektedir. Ad alanlarında tür `nvarchar(MAX)`vardır. Bu öğreticide daha sonra bir geçiş oluşturulup uygulandığında, ad alanları dize uzunluğu özniteliklerinin bir sonucu olarak `nvarchar(50)` olur.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

SQLite aracında `Student` tablo için sütun tanımlarını inceleyin. Ad alanlarında tür `Text`vardır. İlk ad alanının `FirstMidName`çağrıldığından emin olun. Sonraki bölümde, bu sütunun adını `FirstName`olarak değiştirirsiniz.

---

### <a name="the-column-attribute"></a>Column özniteliği

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

Öznitelikler sınıfların ve özelliklerin veritabanına nasıl eşlenildiğini denetleyebilir. `Student` modelinde, `Column` özniteliği, `FirstMidName` özelliğinin adını veritabanında "FirstName" olarak eşlemek için kullanılır.

Veritabanı oluşturulduğunda, model üzerindeki özellik adları sütun adları için kullanılır (`Column` özniteliği kullanıldığı durumlar dışında). `Student` modeli ilk ad alanı için `FirstMidName` kullanır, çünkü alan de bir orta ad içerebilir.

`[Column]` özniteliğiyle, veri modelindeki `Student.FirstMidName` `Student` tablonun `FirstName` sütunuyla eşlenir. `Column` özniteliğinin eklenmesi modeli `SchoolContext`yedekleyen şekilde değiştirir. `SchoolContext` yedekleyen model artık veritabanıyla eşleşmez. Bu tutarsızlık, daha sonra bu öğreticide bir geçiş eklenerek çözümlenir.

### <a name="the-required-attribute"></a>Gerekli öznitelik

```csharp
[Required]
```

`Required` özniteliği, ad özellikleri gereken alanları sağlar. Değer türleri gibi null yapılamayan türler için `Required` özniteliği gerekmez (örneğin, `DateTime`, `int`ve `double`). Null olmayan türler otomatik olarak gerekli alanlar olarak değerlendirilir.

`MinimumLength` zorlanmak için `Required` özniteliği `MinimumLength` ile birlikte kullanılmalıdır.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

`MinimumLength` ve `Required`, doğrulamanın doğrulanmasını yerine getirmek için boşluk kullanılmasına izin verir. Dize üzerinde tam denetim için `RegularExpression` özniteliğini kullanın.

### <a name="the-display-attribute"></a>Display özniteliği

```csharp
[Display(Name = "Last Name")]
```

`Display` özniteliği, metin kutularının açıklamalı alt yazısının "ad", "soyadı", "tam ad" ve "kayıt tarihi" olması gerektiğini belirtir. Varsayılan açıklamalı alt yazıların sözcükleri bölen bir boşluk yoktu, örneğin "LastName".

### <a name="create-a-migration"></a>Geçiş oluşturma

Uygulamayı çalıştırın ve öğrenciler sayfasına gidin. Bir özel durum oluşturulur. `[Column]` özniteliği, EF 'in `FirstName`adlı bir sütun bulmasını beklemesine neden olur, ancak veritabanındaki sütun adı hala `FirstMidName`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Hata iletisi aşağıdaki örneğe benzer:

```
SqlException: Invalid column name 'FirstName'.
```

* PMC 'de yeni bir geçiş oluşturmak ve veritabanını güncelleştirmek için aşağıdaki komutları girin:

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  Bu komutlardan ilki aşağıdaki uyarı iletisini oluşturur:

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  Ad alanları artık 50 karakterle sınırlı olduğundan uyarı oluşturulur. Veritabanındaki bir ad 50 karakterden fazlasına sahipse, son karakter 51 ' i kaybedersiniz.

* Öğrenci tablosunu SSOX içinde açın:

  ![Geçişlerde SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-after-migration.png)

  Geçiş uygulanmadan önce ad sütunları [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)türünde idi. Ad sütunları artık `nvarchar(50)`. Sütun adı, `FirstMidName` `FirstName`olarak değiştirildi.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Hata iletisi aşağıdaki örneğe benzer:

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* Proje klasöründe bir komut penceresi açın. Yeni bir geçiş oluşturmak ve veritabanını güncelleştirmek için aşağıdaki komutları girin:

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  Veritabanı güncelleştirme komutu aşağıdaki örnekte olduğu gibi bir hata görüntüler:

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

Bu öğreticide, bu hatayı geçmenin yolu ilk geçişi silmek ve yeniden oluşturmaktır. Daha fazla bilgi için, [geçiş öğreticisinin](xref:data/ef-rp/migrations)en üstündeki SQLite uyarı notuna bakın.

* *Geçişler* klasörünü silin.
* Veritabanını bırakmak, yeni bir başlangıç geçişi oluşturmak ve geçişi uygulamak için aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* SQLite araclarınızın öğrenci tablosunu inceleyin. FirstMidName sütunu artık FirstName.

---

* Uygulamayı çalıştırın ve öğrenciler sayfasına gidin.
* Saatin giriş veya tarih ile birlikte görüntülenmediğine dikkat edin.
* **Yeni oluştur**' u seçin ve 50 karakterden daha uzun bir ad girmeyi deneyin.

> [!Note]
> Aşağıdaki bölümlerde, uygulamanın bazı aşamalardan oluşturulması derleyici hataları oluşturur. Yönergeler uygulamanın ne zaman derbir olduğunu belirtir.

## <a name="the-instructor-entity"></a>Eğitmen varlığı

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

Aşağıdaki kodla *modeller/eğitmen. cs* oluşturun:

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

Birden çok öznitelik tek bir satırda olabilir. `HireDate` öznitelikleri aşağıdaki gibi yazılabilir:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a>Gezinti özellikleri

`CourseAssignments` ve `OfficeAssignment` özellikleri gezinti özellikleridir.

Bir eğitmen herhangi bir sayıda kurs öğretebilir, bu nedenle `CourseAssignments` bir koleksiyon olarak tanımlanır.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Bir eğitmenin en fazla bir ofisi olabilir, bu nedenle `OfficeAssignment` özelliği tek bir `OfficeAssignment` varlık içerir. Office atanmamışsa `OfficeAssignment` null olur.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a>OfficeAssignment varlığı

![OfficeAssignment varlığı](complex-data-model/_static/officeassignment-entity.png)

Aşağıdaki kodla *modeller/OfficeAssignment. cs* oluşturun:

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Anahtar özniteliği

Özellik adı Classnameıd veya ID dışında bir şey olduğunda, bir özelliği birincil anahtar (PK) olarak tanımlamak için `[Key]` özniteliği kullanılır.

`Instructor` ve `OfficeAssignment` varlıkları arasında bire sıfır veya arasında bir ilişki vardır. Office ataması, atandığı eğitmenle ilişkili olarak yalnızca vardır. `OfficeAssignment` PK Ayrıca, `Instructor` varlığa ait yabancı anahtarıdır (FK).

EF Core, `InstructorID` ID veya Classnameıd adlandırma kuralını izlemediği için, `InstructorID` `OfficeAssignment` PK olarak otomatik olarak tanıyamaz. Bu nedenle, `Key` özniteliği, `InstructorID` PK olarak tanımlamak için kullanılır:

```csharp
[Key]
public int InstructorID { get; set; }
```

Varsayılan olarak, EF Core, sütun tanımlayıcı bir ilişki için olduğundan, anahtarı veritabanı olmayan bir şekilde değerlendirir.

### <a name="the-instructor-navigation-property"></a>Eğitmen gezintisi özelliği

`Instructor.OfficeAssignment` gezinti özelliği null olabilir çünkü belirli bir eğitmen için bir `OfficeAssignment` satırı olmayabilir. Bir eğitmenin Office ataması olmayabilir.

`OfficeAssignment.Instructor` gezinti özelliği her zaman bir eğitmen varlığına sahip olur çünkü yabancı anahtar `InstructorID` türü `int`, null olamayan bir değer türüdür. Bir Office ataması, bir eğitmen olmadan bulunamaz.

`Instructor` bir varlık ilişkili bir `OfficeAssignment` varlığına sahip olduğunda, her varlığın gezinti özelliğinde diğer birine bir başvurusu vardır.

## <a name="the-course-entity"></a>Kurs varlığı

![Kurs varlığı](complex-data-model/_static/course-entity.png)

*Modelleri/kursu. cs* aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

`Course` varlığın bir yabancı anahtar (FK) özelliği `DepartmentID`vardır. `DepartmentID` ilgili `Department` varlığına işaret eder. `Course` varlığın bir `Department` gezinti özelliği vardır.

EF Core, modelin ilgili bir varlık için gezinti özelliği olduğunda bir veri modeli için yabancı anahtar özelliği gerektirmez. EF Core, gerektiği yerde otomatik olarak veritabanında FKs 'ler oluşturur. EF Core otomatik olarak oluşturulan FKs 'ler için [gölge Özellikler](/ef/core/modeling/shadow-properties) oluşturur. Ancak, doğrudan veri modelinde FK dahil edilmesi, güncelleştirmelerin daha basit ve daha verimli olmasını sağlayabilir. Örneğin, FK özelliğinin `DepartmentID` dahil *olmadığı* bir model düşünün. Bir kurs varlığı düzenlemek üzere getirilirken:

* `Department` özelliği açıkça yüklenmediyse null olur.
* Kurs varlığını güncelleştirmek için öncelikle `Department` varlığın getirilmesi gerekir.

FK özelliği `DepartmentID` veri modeline dahil edildiğinde, güncelleştirmeden önce `Department` varlığını getirmeye gerek yoktur.

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated özniteliği

`[DatabaseGenerated(DatabaseGeneratedOption.None)]` özniteliği, PK 'nin veritabanı tarafından oluşturulması yerine uygulama tarafından sağlandığını belirtir.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Varsayılan olarak, EF Core PK değerlerinin veritabanı tarafından oluşturulduğunu varsayar. Veritabanı tarafından oluşturulan genellikle en iyi yaklaşım vardır. `Course` varlıklar için Kullanıcı PK 'yi belirtir. Örneğin, matematik departmanı için 1000 serisi, Ingilizce departmanı için 2000 serisi gibi bir kurs numarası.

`DatabaseGenerated` özniteliği varsayılan değerler oluşturmak için de kullanılabilir. Örneğin, veritabanı bir satırın oluşturulduğu veya güncelleştirildiği tarihi kaydetmek için otomatik olarak bir tarih alanı oluşturabilir. Daha fazla bilgi için bkz. [üretilen Özellikler](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

`Course` varlığındaki yabancı anahtar (FK) özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

Bir kurs bir departmana atanır, bu nedenle bir `DepartmentID` FK ve `Department` gezinti özelliği vardır.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir, bu nedenle `Enrollments` gezinti özelliği bir koleksiyondur:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Bir kurs birden fazla eğitmen tarafından tada olabilir, bu nedenle `CourseAssignments` gezinti özelliği bir koleksiyondur:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` [daha sonra](#many-to-many-relationships)açıklanmaktadır.

## <a name="the-department-entity"></a>Departman varlığı

![Bölüm varlığı](complex-data-model/_static/department-entity.png)

Aşağıdaki kodla *modeller/departman. cs* oluşturun:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a>Column özniteliği

Daha önce `Column` özniteliği sütun adı eşlemesini değiştirmek için kullanılmıştır. `Department` varlığın kodunda, SQL veri türü eşlemesini değiştirmek için `Column` özniteliği kullanılır. `Budget` sütunu, veritabanında SQL Server para türü kullanılarak tanımlanır:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Sütun eşlemesi genellikle gerekli değildir. EF Core, özelliğin CLR türüne göre uygun SQL Server veri türünü seçer. CLR `decimal` türü bir SQL Server `decimal` türüne eşlenir. `Budget` para birimi için ve para veri türü para birimi için daha uygundur.

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

FK ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

* Bir departman yönetici olabilir veya olmayabilir.
* Yönetici her zaman bir eğitmendir. Bu nedenle `InstructorID` özelliği, `Instructor` varlığa FK olarak eklenir.

Gezinti özelliği `Administrator` olarak adlandırılır ancak bir `Instructor` varlığı tutar:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Önceki koddaki soru işareti (?) özelliği null yapılabilir olduğunu belirtiyor.

Bir departmanın birçok kursu olabilir, bu nedenle bir kurs gezintisi özelliği vardır:

```csharp
public ICollection<Course> Courses { get; set; }
```

Kural gereği EF Core, null yapılamayan ve çok-çok ilişkiler için basamaklı silme imkanı sağlar. Bu varsayılan davranış, dairesel basamaklı silme kurallarına neden olabilir. Bir geçiş eklendiğinde dairesel basamaklı silme kuralları bir özel duruma neden olur.

Örneğin, `Department.InstructorID` özelliği null yapılamayan olarak tanımlanmışsa EF Core bir basamaklı silme kuralı yapılandırır. Bu durumda, yönetici olarak atanan eğitmen silindiğinde departman silinir. Bu senaryoda kısıtlama kuralı daha anlamlı hale getirir. Aşağıdaki [Fluent API](#fluent-api-alternative-to-attributes) bir kısıtlama kuralı ayarlar ve art arda silmeyi devre dışı bırakır.

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a>Kayıt varlık

Kayıt kaydı, tek bir öğrenci tarafından gerçekleştirilen bir kurs içindir.

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

*Modelleri/kaydı. cs* 'yi aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

FK özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

Kayıt kaydı tek bir kursa yöneliktir, bu nedenle bir `CourseID` FK özelliği ve `Course` gezinti özelliği vardır:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Kayıt kaydı bir öğrenci içindir, bu nedenle bir `StudentID` FK özelliği ve `Student` gezinti özelliği vardır:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Çoktan çoğa Ilişkiler

`Student` ve `Course` varlıkları arasında çok-çok ilişkisi vardır. `Enrollment` varlık, veritabanında *Yük ile* çoktan çoğa bir JOIN tablosu olarak çalışır. "Yükle", `Enrollment` tablosunun birleştirilmiş tablolar (Bu durumda, PK ve `Grade`) gibi ek verileri içerdiği anlamına gelir.

Aşağıdaki çizimde bu ilişkilerin bir varlık diyagramında nasıl göründüğünü gösterilmektedir. (Bu diyagram EF 6. x için [EF güç araçları](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) kullanılarak oluşturulmuştur. Diyagram oluşturmak öğreticinin bir parçası değildir.)

![Öğrenci-çok fazla ilişki](complex-data-model/_static/student-course.png)

Her ilişki ucu ve bir yıldız işareti (*) 1 diğer sırasında bir-çok ilişkisi belirten bulunur.

`Enrollment` tablo, sınıf bilgilerini içermiyorsa, yalnızca iki FKs (`CourseID` ve `StudentID`) içermesi gerekir. Yük olmadan çoktan çoğa bir JOIN tablosu bazen saf JOIN tablosu (PJT) olarak adlandırılır.

`Instructor` ve `Course` varlıkların, saf bir JOIN tablosu kullanılarak çoktan çoğa bir ilişkisi vardır.

Note: EF 6. x, çoktan çoğa ilişkiler için örtük birleştirmeyi destekler, ancak EF Core değildir. Daha fazla bilgi için [EF Core 2,0 ' de çoktan çoğa ilişkiler](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)bölümüne bakın.

## <a name="the-courseassignment-entity"></a>Courseatama varlığı

![Courseatama varlığı](complex-data-model/_static/courseassignment-entity.png)

Aşağıdaki kodla *modeller/Courseatama. cs* oluşturun:

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

Eğitmenden çok-çok ilişkisi için bir JOIN tablosu gerekir ve bu ekleme tablosu için varlık kurs atamasıdır.

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

`EntityName1EntityName2`bir JOIN varlığı adı yaygın olarak vardır. Örneğin, bu model kullanılarak eğitmen-kurslar JOIN tablosu `CourseInstructor`. Ancak, ilişkiyi açıklayan bir ad kullanmanızı öneririz.

Veri modelleri basit ve büyümeye başlar. Yük (PJTs) olmayan ekleme tabloları genellikle yükü içerecek şekilde gelişmektedir. Açıklayıcı bir varlık adıyla başlayarak, ekleme tablosu değiştiğinde adın değiştirilmesi gerekmez. İdeal olarak, JOIN varlığının iş etki alanında kendi doğal (muhtemelen tek bir kelime) adına sahip olması gerekir. Örneğin, kitaplar ve müşteriler, derecelendirmeler adlı bir JOIN varlığıyla bağlantı kurulabilir. Eğitmenin kursa çok-çok ilişkisi için `CourseInstructor``CourseAssignment` tercih edilir.

### <a name="composite-key"></a>Bileşik anahtar

`CourseAssignment` (`InstructorID` ve `CourseID`) içindeki iki FKs, `CourseAssignment` tablosunun her satırını benzersiz olarak tanımlar. `CourseAssignment` adanmış bir PK gerektirmez. `InstructorID` ve `CourseID` özellikleri bileşik bir PK olarak çalışır. EF Core bileşik PKs 'leri belirtmenin tek yolu *Fluent API*' dir. Sonraki bölümde, bileşik PK 'nin nasıl yapılandırılacağı gösterilmektedir.

Bileşik anahtar şunları sağlar:

* Tek bir kurs için birden çok satıra izin verilir.
* Birden çok satıra bir eğitmen için izin verilir.
* Aynı eğitmen ve kurs için birden çok satıra izin verilmez.

`Enrollment` JOIN varlığı kendi PK 'yi tanımlar, bu nedenle bu sıralamanın yinelemeleri mümkündür. Bu tür yinelemeleri engellemek için:

* FK alanlara benzersiz bir dizin ekleyin veya
* `CourseAssignment`benzer bir birincil bileşik anahtarla `Enrollment` yapılandırın. Daha fazla bilgi için bkz. [dizinler](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Veritabanı bağlamını güncelleştirme

*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

Yukarıdaki kod, yeni varlıkları ekler ve `CourseAssignment` varlığın bileşik PK öğesini yapılandırır.

## <a name="fluent-api-alternative-to-attributes"></a>Tutarlı API 'nin özniteliklere alternatif

Önceki koddaki `OnModelCreating` yöntemi EF Core davranışını yapılandırmak için *Fluent API* kullanır. Genellikle tek bir bildirimde bir dizi yöntem çağrısı olan dize olarak kullanıldığından, API "akıcı" olarak adlandırılır. [Aşağıdaki kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) Fluent API bir örneğidir:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

Bu öğreticide, Fluent API yalnızca özniteliklerle yapılamadığını veritabanı eşlemesi için kullanılır. Ancak Fluent API, özniteliklerle yapılabilecek biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtebilir.

`MinimumLength` gibi bazı öznitelikler Fluent API uygulanamaz. `MinimumLength` şemayı değiştirmez, yalnızca bir minimum uzunluk doğrulama kuralı uygular.

Bazı geliştiriciler, varlık sınıflarının "temiz" olmasını sağlamak için Fluent API özel olarak kullanmayı tercih eder. Öznitelikler ve Fluent API karışık olabilir. Yalnızca Fluent API (bileşik bir PK belirterek) yapılabilecek bazı konfigürasyonlar vardır. Yalnızca özniteliklerle (`MinimumLength`) yapılabilecek bazı konfigürasyonlar vardır. Fluent API veya özniteliklerini kullanmak için önerilen uygulama:

* Bu iki yaklaşımdan birini seçin.
* Seçilen yaklaşımı mümkün olduğunca düzenli olarak kullanın.

Bu öğreticide kullanılan özniteliklerin bazıları için kullanılır:

* Yalnızca doğrulama (örneğin, `MinimumLength`).
* Yalnızca yapılandırma EF Core (örneğin, `HasKey`).
* Doğrulama ve EF Core yapılandırma (örneğin, `[StringLength(50)]`).

Öznitelikler ile Fluent API hakkında daha fazla bilgi için bkz. [yapılandırma yöntemleri](/ef/core/modeling/).

## <a name="entity-diagram"></a>Varlık diyagramı

Aşağıdaki çizimde, tamamlanmış okul modeli için EF Power Tools 'un oluştura diyagramı gösterilmektedir.

![Varlık diyagramı](complex-data-model/_static/diagram.png)

Önceki diyagramda şunları gösterir:

* Birkaç bire çok ilişki satırı (\*1).
* `Instructor` ve `OfficeAssignment` varlıkları arasında bire sıfır veya-bir ilişki çizgisi (1 ila 0.. 1).
* `Instructor` ve `Department` varlıkları arasında sıfır veya-bire çok ilişki çizgisi (0.. 1-*).

## <a name="seed-the-database"></a>Veritabanının çekirdeğini oluşturma

*Data/Dbınizer. cs*dosyasındaki kodu güncelleştirin:

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

Yukarıdaki kod, yeni varlıklar için tohum verileri sağlar. Bu kodun çoğu yeni varlık nesneleri oluşturur ve örnek verileri yükler. Örnek veriler test için kullanılır. Çoktan çoğa ekleme tablolarının nasıl çalıştırılabilir olduğunu gösteren örnekler için `Enrollments` ve `CourseAssignments` bakın.

## <a name="add-a-migration"></a>Geçiş Ekle

Projeyi derleyin.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

PMC 'de aşağıdaki komutu çalıştırın.

```powershell
Add-Migration ComplexDataModel
```

Yukarıdaki komut, olası veri kaybı hakkında bir uyarı görüntüler.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

`database update` komutu çalıştırıldıysanız aşağıdaki hata oluşturulur:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

Sonraki bölümde, bu hatayla ilgili ne yapılacağını görürsünüz.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Bir geçiş ekler ve `database update` komutunu çalıştırırsanız, aşağıdaki hata üretilir:

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

Sonraki bölümde, bu hatanın nasıl önleneceğini görürsünüz.

---

## <a name="apply-the-migration-or-drop-and-re-create"></a>Geçişi uygulayın veya bırakıp yeniden oluşturun

Artık var olan bir veritabanınız olduğuna göre, değişikliklere nasıl uygulanacağını düşünmeniz gerekir. Bu öğreticide iki alternatif gösterilmektedir:

* [Veritabanını bırakıp yeniden oluşturun](#drop). SQLite kullanıyorsanız bu bölümü seçin.
* [Geçişi mevcut veritabanına uygulayın](#applyexisting). Bu bölümdeki yönergeler yalnızca SQL Server için geçerlidir, **SQLite için değildir**. 

Her iki seçenek de SQL Server için geçerlidir. Apply-Migration yöntemi daha karmaşıktır ve zaman alabilir. Bu, gerçek dünyada üretim ortamları için tercih edilen yaklaşımdır. 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a>Veritabanını bırakıp yeniden oluşturun

SQL Server kullanıyorsanız ve aşağıdaki bölümde uygulanacak geçiş yaklaşımını yapmak istiyorsanız [Bu bölümü atlayın](#apply-the-migration) .

Yeni bir veritabanı oluşturmak için EF Core zorlamak için veritabanını bırakıp güncelleştirin:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:

  ```powershell
  Drop-Database
  ```

* *Geçişler* klasörünü silin, ardından aşağıdaki komutu çalıştırın:

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Bir komut penceresi açın ve proje klasörüne gidin. Proje klasörü *Contosouniversity. csproj* dosyasını içerir.

* Şu komutu çalıştırın:

  ```dotnetcli
  dotnet ef database drop --force
  ```

* *Geçişler* klasörünü silin, ardından aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Uygulamayı çalıştırın. Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemini çalıştırır. `DbInitializer.Initialize` yeni veritabanını doldurur.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Veritabanını SSOX içinde açın:

* Daha önce SSOX açıldıysa **Yenile** düğmesine tıklayın.
* **Tables** düğümünü genişletin. Oluşturulan tablolar görüntülenir.

  ![SSOX içindeki tablolar](complex-data-model/_static/ssox-tables.png)

* **Courseatama** tablosunu inceleyin:

  * **Courseatama** tablosuna sağ tıklayın ve **verileri görüntüle**' yi seçin.
  * **Courseatama** tablosunun veri içerdiğini doğrulayın.

  ![SSOX 'te Courseatama verileri](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Veritabanını incelemek için SQLite aracınızı kullanın:

* Yeni tablolar ve sütunlar.
* Tablolardaki verileri, örneğin, **Courseatama** tablosu.

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a>Geçişi Uygula

Bu bölüm isteğe bağlıdır. Bu adımlar yalnızca SQL Server LocalDB için çalışır ve yalnızca önceki [bırakma ve veritabanını yeniden oluştur](#drop) bölümünü atladıktan sonra.

Geçişler mevcut verilerle çalıştırıldığında, mevcut verilerin karşılanmadığı FK kısıtlamalar olabilir. Üretim verileriyle, mevcut verilerin geçirilmesi için adımların alınması gerekir. Bu bölüm, FK kısıtlama ihlallerinin düzeltilmesiyle bir örnek sağlar. Bu kod değişikliklerini yedekleme olmadan yapmayın. Önceki [bırakmayı tamamlayıp veritabanını yeniden oluşturduktan sonra](#drop) Bu kod değişikliklerini yapmayın.

*{Timestamp} _ComplexDataModel. cs* dosyası aşağıdaki kodu içerir:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

Yukarıdaki kod `Course` tabloya null atanamaz `DepartmentID` FK ekler. Önceki öğreticideki veritabanı `Course`satırları içerir, böylece tablo geçişler tarafından güncelleştirilemez.

`ComplexDataModel` geçişinin mevcut verilerle çalışmasını sağlamak için:

* Yeni sütuna (`DepartmentID`) varsayılan değer vermek için kodu değiştirin.
* Varsayılan departman olarak davranacak "Temp" adlı sahte bir departman oluşturun.

#### <a name="fix-the-foreign-key-constraints"></a>Yabancı anahtar kısıtlamalarını çözme

`ComplexDataModel` geçiş sınıfında `Up` yöntemini güncelleştirin:

* *{Timestamp} _ComplexDataModel. cs* dosyasını açın.
* `DepartmentID` sütununu `Course` tablosuna ekleyen kod satırını açıklama satırı yapın.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Aşağıdaki vurgulanmış kodu ekleyin. Yeni kod `.CreateTable( name: "Department"` bloğundan sonra geçer:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

Önceki değişikliklerle, `ComplexDataModel.Up` yöntemi çalıştıktan sonra mevcut `Course` satırları "Temp" departmanı ile ilişkilendirilir.

Burada gösterilen durumu işlemenin yolu, bu öğretici için basitleştirilmiştir. Bir üretim uygulaması şöyle olacaktır:

* Yeni `Department` satırlarına `Department` satırları ve ilgili `Course` satırlarını eklemek için kod veya komut dosyaları ekleyin.
* "Geçici" Departmanı veya `Course.DepartmentID`için varsayılan değeri kullanmayın.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:

  ```powershell
  Update-Database
  ```

`DbInitializer.Initialize` yöntemi yalnızca boş bir veritabanıyla çalışacak şekilde tasarlandığından, öğrenci ve kurs tablolarındaki tüm satırları silmek için SSOX kullanın. (Cascade silme, kayıt tablosundan işlem gerçekleştirir.)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Visual Studio Code ile SQL Server LocalDB kullanıyorsanız şu komutu çalıştırın:

  ```dotnetcli
  dotnet ef database update
  ```

---

Uygulamayı çalıştırın. Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemini çalıştırır. `DbInitializer.Initialize` yeni veritabanını doldurur.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki iki öğretici ilgili verilerin nasıl okunacağını ve güncelleştirilmesini gösterir.

> [!div class="step-by-step"]
> [Önceki öğretici](xref:data/ef-rp/migrations)
> [sonraki öğretici](xref:data/ef-rp/read-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Önceki öğreticiler, üç varlıktan oluşan temel bir veri modeliyle çalışmıştır. Bu öğreticide:

* Daha fazla varlık ve ilişki eklenmiştir.
* Veri modeli biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirtilerek özelleştirilir.

Tamamlanan veri modeli için varlık sınıfları aşağıdaki çizimde gösterilmiştir:

![Varlık diyagramı](complex-data-model/_static/diagram.png)

Çözemediğiniz sorunlarla karşılaşırsanız, [Tamamlanmış uygulamayı](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)indirin.

## <a name="customize-the-data-model-with-attributes"></a>Veri modelini özniteliklerle özelleştirme

Bu bölümde, veri modeli öznitelikler kullanılarak özelleştirilir.

### <a name="the-datatype-attribute"></a>DataType özniteliği

Öğrenci sayfaları Şu anda kayıt tarihinin saatini görüntüler. Genellikle, tarih alanları saati değil yalnızca tarihi gösterir.

*Modelleri/öğrenci. cs* 'yi aşağıdaki vurgulanmış kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) özniteliği, veritabanı iç türünden daha belirgin bir veri türünü belirtir. Bu durumda, tarih ve saat değil yalnızca tarih görüntülenmelidir. Veri [türü numaralandırması](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) , tarih, saat, PhoneNumber, para birimi, emaadresi vb. gibi birçok veri türü sağlar. `DataType` özniteliği Ayrıca uygulamanın türe özgü özellikleri otomatik olarak sağlamasını da sağlayabilir. Örneğin:

* `mailto:` bağlantısı `DataType.EmailAddress`için otomatik olarak oluşturulur.
* Tarih Seçici çoğu tarayıcıda `DataType.Date` için sağlanır.

`DataType` özniteliği HTML 5 tarayıcıların kullandığı HTML 5 `data-` (bir veri Dash) özniteliklerini yayar. `DataType` öznitelikleri doğrulama sağlamaz.

`DataType.Date` görüntülenen tarihin biçimini belirtmiyor. Varsayılan olarak, Tarih alanı sunucunun [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)öğesine göre varsayılan biçimlere göre görüntülenir.

`DisplayFormat` özniteliği, açıkça tarih biçimini belirtmek için kullanılır:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode` ayarı, biçimlendirmenin düzenleme kullanıcı arabirimine de uygulanacağını belirtir. Bazı alanlar `ApplyFormatInEditMode`kullanmamalıdır. Örneğin, para birimi simgesi genellikle bir düzenleme metin kutusunda gösterilmemelidir.

`DisplayFormat` özniteliği kendisi tarafından kullanılabilir. `DataType` özniteliğini `DisplayFormat` özniteliğiyle kullanmak genellikle iyi bir fikirdir. `DataType` özniteliği, verilerin semantiğini bir ekranda nasıl işleneceğini tersine alır. `DataType` özniteliği, `DisplayFormat`kullanılamayan aşağıdaki avantajları sağlar:

* Tarayıcı HTML5 özelliklerini etkinleştirebilir. Örneğin, bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını, istemci tarafı giriş doğrulamasını vb. göster
* Varsayılan olarak tarayıcı, verileri yerel ayara göre doğru biçimi kullanarak işler.

Daha fazla bilgi için bkz. [giriş > etiketi Yardımcısı belgeleri\<](xref:mvc/views/working-with-forms#the-input-tag-helper).

Uygulamayı çalıştırın. Öğrenciler dizin sayfasına gidin. Süreler artık görüntülenmiyor. `Student` modelini kullanan her görünüm, tarihi zaman içinde görüntüler.

![Öğrenciler Dizin sayfası tarihleri zamansız gösterme](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>StringLength özniteliği

Veri doğrulama kuralları ve doğrulama hatası iletileri özniteliklerle belirtilebilir. [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) özniteliği, bir veri alanında izin verilen en düşük ve en fazla karakter uzunluğunu belirtir. `StringLength` özniteliği de istemci tarafı ve sunucu tarafı doğrulaması sağlar. En küçük değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.

`Student` modelini aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Yukarıdaki kod, adları 50 karakterden fazla olmayacak şekilde sınırlandırır. `StringLength` özniteliği, kullanıcının bir ad için boşluk girmesini engellemez. [Cevap içerisinde RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) özniteliği, girişe kısıtlamalar uygulamak için kullanılır. Örneğin, aşağıdaki kod ilk karakterin büyük küçük harf olmasını ve geri kalan karakterlerin alfabetik olmasını gerektirir:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Uygulamayı çalıştırın:

* Öğrenciler sayfasına gidin.
* **Yeni oluştur**' u seçin ve 50 karakterden daha uzun bir ad girin.
* **Oluştur**' u seçin, istemci tarafı doğrulama bir hata iletisi gösterir.

![Öğrenciler Dizin sayfası dize uzunluğu hatalarını gösteriyor](complex-data-model/_static/string-length-errors.png)

**SQL Server Nesne Gezgini** (ssox) içinde **öğrenci** tablosuna çift tıklayarak öğrenci tablosu tasarımcısını açın.

![Geçişle önce SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-before-migration.png)

Önceki görüntüde `Student` tablo şeması gösterilmektedir. VERITABANı üzerinde geçişler çalıştırılmadığından, ad alanlarının türü `nvarchar(MAX)` vardır. Bu öğreticide daha sonra geçişler çalıştırıldığında, ad alanları `nvarchar(50)`olur.

### <a name="the-column-attribute"></a>Column özniteliği

Öznitelikler sınıfların ve özelliklerin veritabanına nasıl eşlenildiğini denetleyebilir. Bu bölümde, `Column` özniteliği, `FirstMidName` özelliğinin adını VERITABANıNDA "FirstName" olarak eşlemek için kullanılır.

DB oluşturulduğunda, model üzerindeki özellik adları sütun adları için kullanılır (`Column` özniteliği kullanıldığı durumlar dışında).

`Student` modeli ilk ad alanı için `FirstMidName` kullanır, çünkü alan de bir orta ad içerebilir.

*Student.cs* dosyasını aşağıdaki vurgulanmış kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Yukarıdaki değişiklik ile uygulamadaki `Student.FirstMidName`, `Student` tablosunun `FirstName` sütunuyla eşlenir.

`Column` özniteliğinin eklenmesi modeli `SchoolContext`yedekleyen şekilde değiştirir. `SchoolContext` yedekleyen model artık veritabanıyla eşleşmez. Geçiş uygulamadan önce uygulama çalışıyorsa aşağıdaki özel durum oluşturulur:

```
SqlException: Invalid column name 'FirstName'.
```

DB 'yi güncelleştirmek için:

* Projeyi derleyin.
* Proje klasöründe bir komut penceresi açın. Yeni bir geçiş oluşturmak ve DB 'yi güncelleştirmek için aşağıdaki komutları girin:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

`migrations add ColumnFirstName` komutu aşağıdaki uyarı iletisini üretir:

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

Ad alanları artık 50 karakterle sınırlı olduğundan uyarı oluşturulur. VERITABANıNDAKI bir ad 50 karakterden fazlasına sahipse, son karakter 51 ' i kaybedersiniz.

* Uygulamayı test etme.

Öğrenci tablosunu SSOX içinde açın:

![Geçişlerde SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-after-migration.png)

Geçiş uygulanmadan önce ad sütunları [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)türünde idi. Ad sütunları artık `nvarchar(50)`. Sütun adı, `FirstMidName` `FirstName`olarak değiştirildi.

> [!Note]
> Aşağıdaki bölümde, uygulamanın bazı aşamalardan oluşturulması derleyici hataları oluşturur. Yönergeler uygulamanın ne zaman derbir olduğunu belirtir.

## <a name="student-entity-update"></a>Öğrenci varlık güncelleştirmesi

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

*Modelleri/öğrenci. cs* 'yi aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Gerekli öznitelik

`Required` özniteliği, ad özellikleri gereken alanları sağlar. Değer türleri (`DateTime`, `int`, `double`, vb.) gibi null yapılamayan türler için `Required` özniteliği gerekli değildir. Null olmayan türler otomatik olarak gerekli alanlar olarak değerlendirilir.

`Required` özniteliği `StringLength` özniteliğinde minimum length parametresiyle değiştirilebilir:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Display özniteliği

`Display` özniteliği, metin kutularının açıklamalı alt yazısının "ad", "soyadı", "tam ad" ve "kayıt tarihi" olması gerektiğini belirtir. Varsayılan açıklamalı alt yazıların sözcükleri bölen bir boşluk yoktu, örneğin "LastName".

### <a name="the-fullname-calculated-property"></a>FullName hesaplanmış özelliği

`FullName`, diğer iki özelliği birleştirerek oluşturulmuş bir değer döndüren hesaplanmış bir özelliktir. `FullName` ayarlanamaz, yalnızca bir get erişimcisi vardır. Veritabanında `FullName` sütunu oluşturulmaz.

## <a name="create-the-instructor-entity"></a>Eğitmen varlığı oluşturma

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

Aşağıdaki kodla *modeller/eğitmen. cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

Birden çok öznitelik tek bir satırda olabilir. `HireDate` öznitelikleri aşağıdaki gibi yazılabilir:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Courseatamalar ve OfficeAssignment gezinti özellikleri

`CourseAssignments` ve `OfficeAssignment` özellikleri gezinti özellikleridir.

Bir eğitmen herhangi bir sayıda kurs öğretebilir, bu nedenle `CourseAssignments` bir koleksiyon olarak tanımlanır.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Bir gezinti özelliği birden çok varlık tutuyorsa:

* Girişlerin eklenebileceği, silinebileceği ve güncelleştirilebileceği bir liste türü olmalıdır.

Gezinti özelliği türleri şunları içerir:

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

`ICollection<T>` belirtilirse EF Core varsayılan olarak bir `HashSet<T>` koleksiyonu oluşturur.

`CourseAssignment` varlığı, çoktan çoğa ilişkilerin bölümünde açıklanmaktadır.

Contoso Üniversitesi iş kuralları, bir eğitmenin en fazla bir ofisiniz olabilir. `OfficeAssignment` özelliği tek bir `OfficeAssignment` varlığı barındırır. Office atanmamışsa `OfficeAssignment` null olur.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>OfficeAssignment varlığını oluşturma

![OfficeAssignment varlığı](complex-data-model/_static/officeassignment-entity.png)

Aşağıdaki kodla *modeller/OfficeAssignment. cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Anahtar özniteliği

Özellik adı Classnameıd veya ID dışında bir şey olduğunda, bir özelliği birincil anahtar (PK) olarak tanımlamak için `[Key]` özniteliği kullanılır.

`Instructor` ve `OfficeAssignment` varlıkları arasında bire sıfır veya arasında bir ilişki vardır. Office ataması, atandığı eğitmenle ilişkili olarak yalnızca vardır. `OfficeAssignment` PK Ayrıca, `Instructor` varlığa ait yabancı anahtarıdır (FK). EF Core, şu nedenle `InstructorID` otomatik olarak `OfficeAssignment` PK olarak tanıyamaz:

* `InstructorID`, ID veya Classnameıd adlandırma kuralını takip etmez.

Bu nedenle, `Key` özniteliği, `InstructorID` PK olarak tanımlamak için kullanılır:

```csharp
[Key]
public int InstructorID { get; set; }
```

Varsayılan olarak, EF Core, sütun tanımlayıcı bir ilişki için olduğundan, anahtarı veritabanı olmayan bir şekilde değerlendirir.

### <a name="the-instructor-navigation-property"></a>Eğitmen gezintisi özelliği

`Instructor` varlık için `OfficeAssignment` gezinti özelliği null yapılabilir, çünkü:

* Başvuru türleri (örneğin, sınıflar Nullable).
* Bir eğitmenin Office ataması olmayabilir.

`OfficeAssignment` varlık null atanamaz `Instructor` bir gezinti özelliğine sahiptir çünkü:

* `InstructorID` null değer atanamaz.
* Bir Office ataması, bir eğitmen olmadan bulunamaz.

`Instructor` bir varlık ilişkili bir `OfficeAssignment` varlığına sahip olduğunda, her varlığın gezinti özelliğinde diğer birine bir başvurusu vardır.

`[Required]` özniteliği `Instructor` gezinti özelliğine uygulanabilir:

```csharp
[Required]
public Instructor Instructor { get; set; }
```

Yukarıdaki kod, ilgili bir eğitmen olması gerektiğini belirtir. `InstructorID` yabancı anahtar (aynı zamanda PK) null değer atanamaz olduğundan, yukarıdaki kod gereksizdir.

## <a name="modify-the-course-entity"></a>Kurs varlığını değiştirme

![Kurs varlığı](complex-data-model/_static/course-entity.png)

*Modelleri/kursu. cs* aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

`Course` varlığın bir yabancı anahtar (FK) özelliği `DepartmentID`vardır. `DepartmentID` ilgili `Department` varlığına işaret eder. `Course` varlığın bir `Department` gezinti özelliği vardır.

EF Core, modelin ilgili bir varlık için gezinti özelliği olduğunda bir veri modeli için FK özelliği gerektirmez.

EF Core, gerektiği yerde otomatik olarak veritabanında FKs 'ler oluşturur. EF Core otomatik olarak oluşturulan FKs 'ler için [gölge Özellikler](/ef/core/modeling/shadow-properties) oluşturur. FK 'in veri modelinde olması, güncelleştirmeleri daha basit ve daha verimli hale getirir. Örneğin, FK özelliğinin `DepartmentID` dahil *olmadığı* bir model düşünün. Bir kurs varlığı düzenlemek üzere getirilirken:

* `Department` varlığı açık bir şekilde yüklenmediyse null olur.
* Kurs varlığını güncelleştirmek için öncelikle `Department` varlığın getirilmesi gerekir.

FK özelliği `DepartmentID` veri modeline dahil edildiğinde, güncelleştirmeden önce `Department` varlığını getirmeye gerek yoktur.

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated özniteliği

`[DatabaseGenerated(DatabaseGeneratedOption.None)]` özniteliği, PK 'nin veritabanı tarafından oluşturulması yerine uygulama tarafından sağlandığını belirtir.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Varsayılan olarak, EF Core PK değerlerinin DB tarafından oluşturulduğunu varsayar. VERITABANı tarafından oluşturulan PK değerleri genellikle en iyi yaklaşımdır. `Course` varlıklar için Kullanıcı PK 'yi belirtir. Örneğin, matematik departmanı için 1000 serisi, Ingilizce departmanı için 2000 serisi gibi bir kurs numarası.

`DatabaseGenerated` özniteliği varsayılan değerler oluşturmak için de kullanılabilir. Örneğin, VERITABANı bir satırın oluşturulduğu veya güncelleştirildiği tarihi kaydetmek için otomatik olarak bir tarih alanı oluşturabilir. Daha fazla bilgi için bkz. [üretilen Özellikler](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

`Course` varlığındaki yabancı anahtar (FK) özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

Bir kurs bir departmana atanır, bu nedenle bir `DepartmentID` FK ve `Department` gezinti özelliği vardır.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir, bu nedenle `Enrollments` gezinti özelliği bir koleksiyondur:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Bir kurs birden fazla eğitmen tarafından tada olabilir, bu nedenle `CourseAssignments` gezinti özelliği bir koleksiyondur:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` [daha sonra](#many-to-many-relationships)açıklanmaktadır.

## <a name="create-the-department-entity"></a>Departman varlığı oluşturma

![Bölüm varlığı](complex-data-model/_static/department-entity.png)

Aşağıdaki kodla *modeller/departman. cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Column özniteliği

Daha önce `Column` özniteliği sütun adı eşlemesini değiştirmek için kullanılmıştır. `Department` varlığın kodunda, SQL veri türü eşlemesini değiştirmek için `Column` özniteliği kullanılır. `Budget` sütunu, VERITABANıNDA SQL Server para türü kullanılarak tanımlanır:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Sütun eşlemesi genellikle gerekli değildir. EF Core genellikle özelliğin CLR türüne göre uygun SQL Server veri türünü seçer. CLR `decimal` türü bir SQL Server `decimal` türüne eşlenir. `Budget` para birimi için ve para veri türü para birimi için daha uygundur.

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

FK ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

* Bir departman yönetici olabilir veya olmayabilir.
* Yönetici her zaman bir eğitmendir. Bu nedenle `InstructorID` özelliği, `Instructor` varlığa FK olarak eklenir.

Gezinti özelliği `Administrator` olarak adlandırılır ancak bir `Instructor` varlığı tutar:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Önceki koddaki soru işareti (?) özelliği null yapılabilir olduğunu belirtiyor.

Bir departmanın birçok kursu olabilir, bu nedenle bir kurs gezintisi özelliği vardır:

```csharp
public ICollection<Course> Courses { get; set; }
```

Note: kurala göre EF Core, null yapılamayan ve çoktan çoğa ilişkiler için art arda silme imkanı sağlar. Basamaklı silme, dairesel basamaklı silme kurallarına neden olabilir. Döngüsel basamaklı silme kuralları, bir geçiş eklendiğinde özel duruma neden olur.

Örneğin, `Department.InstructorID` özelliği null yapılamayan olarak tanımlanmışsa:

* EF Core, eğitmen silindiğinde departmanı silmek için bir basamakla silme kuralı yapılandırır.
* Eğitmen silindiğinde departmanı silmek amaçlanan davranış değildir.
* Aşağıdaki [Fluent API](#fluent-api-alternative-to-attributes) Cascade yerine bir kısıtlama kuralı ayarlar.

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

Yukarıdaki kod, departman-eğitmen ilişkisindeki basamaklı silmeyi devre dışı bırakır.

## <a name="update-the-enrollment-entity"></a>Kayıt varlığını güncelleştirme

Kayıt kaydı, tek bir öğrenci tarafından gerçekleştirilen bir kurs içindir.

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

*Modelleri/kaydı. cs* 'yi aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

FK özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

Kayıt kaydı tek bir kursa yöneliktir, bu nedenle bir `CourseID` FK özelliği ve `Course` gezinti özelliği vardır:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Kayıt kaydı bir öğrenci içindir, bu nedenle bir `StudentID` FK özelliği ve `Student` gezinti özelliği vardır:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Çoktan çoğa Ilişkiler

`Student` ve `Course` varlıkları arasında çok-çok ilişkisi vardır. `Enrollment` varlık, veritabanında *Yük ile* çoktan çoğa bir JOIN tablosu olarak çalışır. "Yükle", `Enrollment` tablosunun birleştirilmiş tablolar (Bu durumda, PK ve `Grade`) gibi ek verileri içerdiği anlamına gelir.

Aşağıdaki çizimde bu ilişkilerin bir varlık diyagramında nasıl göründüğünü gösterilmektedir. (Bu diyagram EF 6. x için [EF güç araçları](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) kullanılarak oluşturulmuştur. Diyagram oluşturmak öğreticinin bir parçası değildir.)

![Öğrenci-çok fazla ilişki](complex-data-model/_static/student-course.png)

Her ilişki ucu ve bir yıldız işareti (*) 1 diğer sırasında bir-çok ilişkisi belirten bulunur.

`Enrollment` tablo, sınıf bilgilerini içermiyorsa, yalnızca iki FKs (`CourseID` ve `StudentID`) içermesi gerekir. Yük olmadan çoktan çoğa bir JOIN tablosu bazen saf JOIN tablosu (PJT) olarak adlandırılır.

`Instructor` ve `Course` varlıkların, saf bir JOIN tablosu kullanılarak çoktan çoğa bir ilişkisi vardır.

Note: EF 6. x, çoktan çoğa ilişkiler için örtük birleştirmeyi destekler, ancak EF Core değildir. Daha fazla bilgi için [EF Core 2,0 ' de çoktan çoğa ilişkiler](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)bölümüne bakın.

## <a name="the-courseassignment-entity"></a>Courseatama varlığı

![Courseatama varlığı](complex-data-model/_static/courseassignment-entity.png)

Aşağıdaki kodla *modeller/Courseatama. cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>Eğitmenden kurslar

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

Eğitmenin kurslardan çok-çok ilişkisi:

* Bir varlık kümesiyle temsil etmelidir bir JOIN tablosu gerektirir.
* , Saf bir JOIN tablosu (yük içermeyen tablo).

`EntityName1EntityName2`bir JOIN varlığı adı yaygın olarak vardır. Örneğin, bu model kullanılarak eğitmen-kurslar JOIN tablosu `CourseInstructor`. Ancak, ilişkiyi açıklayan bir ad kullanmanızı öneririz.

Veri modelleri basit ve büyümeye başlar. Yük yükü dahil olmak üzere genellikle yük-yük birleştirmeleri (PJTs) gelişmektedir. Açıklayıcı bir varlık adıyla başlayarak, ekleme tablosu değiştiğinde adın değiştirilmesi gerekmez. İdeal olarak, JOIN varlığının iş etki alanında kendi doğal (muhtemelen tek bir kelime) adına sahip olması gerekir. Örneğin, kitaplar ve müşteriler, derecelendirmeler adlı bir JOIN varlığıyla bağlantı kurulabilir. Eğitmenin kursa çok-çok ilişkisi için `CourseInstructor``CourseAssignment` tercih edilir.

### <a name="composite-key"></a>Bileşik anahtar

FKs null değer atanamaz. `CourseAssignment` (`InstructorID` ve `CourseID`) içindeki iki FKs, `CourseAssignment` tablosunun her satırını benzersiz olarak tanımlar. `CourseAssignment` adanmış bir PK gerektirmez. `InstructorID` ve `CourseID` özellikleri bileşik bir PK olarak çalışır. EF Core bileşik PKs 'leri belirtmenin tek yolu *Fluent API*' dir. Sonraki bölümde, bileşik PK 'nin nasıl yapılandırılacağı gösterilmektedir.

Bileşik anahtar şunları sağlar:

* Tek bir kurs için birden çok satıra izin verilir.
* Birden çok satıra bir eğitmen için izin verilir.
* Aynı eğitmen ve kurs için birden çok satıra izin verilmez.

`Enrollment` JOIN varlığı kendi PK 'yi tanımlar, bu nedenle bu sıralamanın yinelemeleri mümkündür. Bu tür yinelemeleri engellemek için:

* FK alanlara benzersiz bir dizin ekleyin veya
* `CourseAssignment`benzer bir birincil bileşik anahtarla `Enrollment` yapılandırın. Daha fazla bilgi için bkz. [dizinler](/ef/core/modeling/indexes).

## <a name="update-the-db-context"></a>DB bağlamını güncelleştirme

*Data/SchoolContext. cs*' ye aşağıdaki vurgulanmış kodu ekleyin:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Yukarıdaki kod, yeni varlıkları ekler ve `CourseAssignment` varlığın bileşik PK öğesini yapılandırır.

## <a name="fluent-api-alternative-to-attributes"></a>Tutarlı API 'nin özniteliklere alternatif

Önceki koddaki `OnModelCreating` yöntemi EF Core davranışını yapılandırmak için *Fluent API* kullanır. Genellikle tek bir bildirimde bir dizi yöntem çağrısı olan dize olarak kullanıldığından, API "akıcı" olarak adlandırılır. [Aşağıdaki kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) Fluent API bir örneğidir:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

Bu öğreticide, Fluent API yalnızca özniteliklerle yapılamadığını DB eşlemesi için kullanılır. Ancak Fluent API, özniteliklerle yapılabilecek biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtebilir.

`MinimumLength` gibi bazı öznitelikler Fluent API uygulanamaz. `MinimumLength` şemayı değiştirmez, yalnızca bir minimum uzunluk doğrulama kuralı uygular.

Bazı geliştiriciler, varlık sınıflarının "temiz" olmasını sağlamak için Fluent API özel olarak kullanmayı tercih eder. Öznitelikler ve Fluent API karışık olabilir. Yalnızca Fluent API (bileşik bir PK belirterek) yapılabilecek bazı konfigürasyonlar vardır. Yalnızca özniteliklerle (`MinimumLength`) yapılabilecek bazı konfigürasyonlar vardır. Fluent API veya özniteliklerini kullanmak için önerilen uygulama:

* Bu iki yaklaşımdan birini seçin.
* Seçilen yaklaşımı mümkün olduğunca düzenli olarak kullanın.

Bu öğreticide kullanılan özniteliklerin bazıları için kullanılır:

* Yalnızca doğrulama (örneğin, `MinimumLength`).
* Yalnızca yapılandırma EF Core (örneğin, `HasKey`).
* Doğrulama ve EF Core yapılandırma (örneğin, `[StringLength(50)]`).

Öznitelikler ile Fluent API hakkında daha fazla bilgi için bkz. [yapılandırma yöntemleri](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Ilişkileri gösteren varlık diyagramı

Aşağıdaki çizimde, tamamlanmış okul modeli için EF Power Tools 'un oluştura diyagramı gösterilmektedir.

![Varlık diyagramı](complex-data-model/_static/diagram.png)

Önceki diyagramda şunları gösterir:

* Birkaç bire çok ilişki satırı (\*1).
* `Instructor` ve `OfficeAssignment` varlıkları arasında bire sıfır veya-bir ilişki çizgisi (1 ila 0.. 1).
* `Instructor` ve `Department` varlıkları arasında sıfır veya-bire çok ilişki çizgisi (0.. 1-*).

## <a name="seed-the-db-with-test-data"></a>VERITABANıNı test verileriyle çekirdek olarak

*Data/Dbınizer. cs*dosyasındaki kodu güncelleştirin:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

Yukarıdaki kod, yeni varlıklar için tohum verileri sağlar. Bu kodun çoğu yeni varlık nesneleri oluşturur ve örnek verileri yükler. Örnek veriler test için kullanılır. Çoktan çoğa ekleme tablolarının nasıl çalıştırılabilir olduğunu gösteren örnekler için `Enrollments` ve `CourseAssignments` bakın.

## <a name="add-a-migration"></a>Geçiş Ekle

Projeyi derleyin.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

Yukarıdaki komut, olası veri kaybı hakkında bir uyarı görüntüler.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

`database update` komutu çalıştırıldıysanız aşağıdaki hata oluşturulur:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>Geçişi Uygula

Artık var olan bir veritabanınız olduğuna göre, bundan sonraki değişikliklere nasıl uygulanacağını düşünmeniz gerekir. Bu öğreticide iki yaklaşım gösterilmektedir:

* [Veritabanını bırakıp yeniden oluşturun](#drop)
* [Geçişi mevcut veritabanına uygulayın](#applyexisting). Bu yöntem daha karmaşıktır ve zaman alabilir. Bu, gerçek dünyada üretim ortamları için tercih edilen yaklaşımdır. **Note**: Bu, öğreticinin isteğe bağlı bir bölümüdür. Bırakma ve yeniden oluşturma adımlarını gerçekleştirebilir ve bu bölümü atlayabilirsiniz. Bu bölümdeki adımları izlemek isterseniz, bırakma ve yeniden oluşturma adımlarını yapmayın. 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>Veritabanını bırakıp yeniden oluşturun

Güncelleştirilmiş `DbInitializer` kod, yeni varlıklar için tohum verileri ekler. Yeni bir VERITABANı oluşturmak için EF Core zorlamak için DB 'yi bırakıp güncelleştirin:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

**Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:

```powershell
Drop-Database
Update-Database
```

Yardım bilgileri almak için PMC 'ten `Get-Help about_EntityFrameworkCore` çalıştırın.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Bir komut penceresi açın ve proje klasörüne gidin. Proje klasörü *Startup.cs* dosyasını içerir.

Komut penceresine şunu girin:

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

Uygulamayı çalıştırın. Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemini çalıştırır. `DbInitializer.Initialize` yeni DB 'yi doldurur.

VERITABANıNı SSOX içinde açın:

* Daha önce SSOX açıldıysa **Yenile** düğmesine tıklayın.
* **Tables** düğümünü genişletin. Oluşturulan tablolar görüntülenir.

![SSOX içindeki tablolar](complex-data-model/_static/ssox-tables.png)

**Courseatama** tablosunu inceleyin:

* **Courseatama** tablosuna sağ tıklayın ve **verileri görüntüle**' yi seçin.
* **Courseatama** tablosunun veri içerdiğini doğrulayın.

![SSOX 'te Courseatama verileri](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>Geçişi mevcut veritabanına Uygula

Bu bölüm isteğe bağlıdır. Bu adımlar yalnızca önceki [bırakma ve veritabanını yeniden oluşturma](#drop) bölümünü atladıysanız çalışır.

Geçişler mevcut verilerle çalıştırıldığında, mevcut verilerin karşılanmadığı FK kısıtlamalar olabilir. Üretim verileriyle, mevcut verilerin geçirilmesi için adımların alınması gerekir. Bu bölüm, FK kısıtlama ihlallerinin düzeltilmesiyle bir örnek sağlar. Bu kod değişikliklerini yedekleme olmadan yapmayın. Önceki bölümü tamamlayıp veritabanını güncelleştirdiyseniz, bu kod değişikliklerini yapmayın.

*{Timestamp} _ComplexDataModel. cs* dosyası aşağıdaki kodu içerir:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

Yukarıdaki kod `Course` tabloya null atanamaz `DepartmentID` FK ekler. Önceki öğreticideki VERITABANı `Course`satırları içerir, böylece tablo geçişler tarafından güncelleştirilemez.

`ComplexDataModel` geçişinin mevcut verilerle çalışmasını sağlamak için:

* Yeni sütuna (`DepartmentID`) varsayılan değer vermek için kodu değiştirin.
* Varsayılan departman olarak davranacak "Temp" adlı sahte bir departman oluşturun.

#### <a name="fix-the-foreign-key-constraints"></a>Yabancı anahtar kısıtlamalarını çözme

`ComplexDataModel` sınıfları `Up` yöntemini güncelleştirin:

* *{Timestamp} _ComplexDataModel. cs* dosyasını açın.
* `DepartmentID` sütununu `Course` tablosuna ekleyen kod satırını açıklama satırı yapın.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Aşağıdaki vurgulanmış kodu ekleyin. Yeni kod `.CreateTable( name: "Department"` bloğundan sonra geçer:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Önceki değişikliklerle, `ComplexDataModel` `Up` yöntemi çalıştıktan sonra mevcut `Course` satırları "geçici" departmanıyla ilişkilendirilir.

Bir üretim uygulaması şöyle olacaktır:

* Yeni `Department` satırlarına `Department` satırları ve ilgili `Course` satırlarını eklemek için kod veya komut dosyaları ekleyin.
* "Geçici" Departmanı veya `Course.DepartmentID`için varsayılan değeri kullanmayın.

Sonraki öğreticide ilgili veriler ele alınmaktadır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü (Bölüm 1)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [Bu öğreticinin YouTube sürümü (Bölüm 2)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> [Önceki](xref:data/ef-rp/migrations)
> [İleri](xref:data/ef-rp/read-related-data)

::: moniker-end
