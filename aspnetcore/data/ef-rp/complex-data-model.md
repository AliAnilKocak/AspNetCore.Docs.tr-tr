---
title: ASP.NET Core veri modelinde EF Core ile Razor Pages-5/8
author: tdykstra
description: Bu öğreticide, daha fazla varlık ve ilişki ekleyin ve biçimlendirme, doğrulama ve eşleme kurallarını belirterek veri modelini özelleştirin.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 78ff36b291b3215460d9ae8e560f49871862d19f
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080980"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="ebfc3-103">ASP.NET Core veri modelinde EF Core ile Razor Pages-5/8</span><span class="sxs-lookup"><span data-stu-id="ebfc3-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="ebfc3-104">Tarafından [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ebfc3-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ebfc3-105">Önceki öğreticiler, üç varlıktan oluşan temel bir veri modeliyle çalışmıştır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="ebfc3-106">Bu öğreticide:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-106">In this tutorial:</span></span>

* <span data-ttu-id="ebfc3-107">Daha fazla varlık ve ilişki eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="ebfc3-108">Veri modeli biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirtilerek özelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="ebfc3-109">Tamamlanan veri modeli aşağıdaki çizimde gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-109">The completed data model is shown in the following illustration:</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="ebfc3-111">Öğrenci varlık</span><span class="sxs-lookup"><span data-stu-id="ebfc3-111">The Student entity</span></span>

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

<span data-ttu-id="ebfc3-113">*Modeller/öğrenci. cs* dosyasındaki kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="ebfc3-114">Yukarıdaki kod, bir `FullName` özelliği ekler ve var olan özelliklere aşağıdaki öznitelikleri ekler:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="ebfc3-115">FullName hesaplanmış özelliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-115">The FullName calculated property</span></span>

<span data-ttu-id="ebfc3-116">`FullName`, iki diğer özelliğin bitiştirerek oluşturulmuş bir değer döndüren hesaplanmış bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="ebfc3-117">`FullName`ayarlanamaz, bu nedenle yalnızca bir get erişimcisi vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="ebfc3-118">Veritabanında `FullName` hiçbir sütun oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="ebfc3-119">DataType özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="ebfc3-120">Öğrenci kayıt tarihleri için, tüm sayfalar şu anda tarihle birlikte tarih ile görüntülenir, ancak yalnızca tarihin ilgili olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="ebfc3-121">Veri ek açıklaması özniteliklerini kullanarak, verileri gösteren her sayfada görüntü biçimini giderecek bir kod değişikliği yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="ebfc3-122">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) özniteliği, veritabanı iç türünden daha belirgin bir veri türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="ebfc3-123">Bu durumda, tarih ve saat değil yalnızca tarih görüntülenmelidir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="ebfc3-124">Veri [türü numaralandırması](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) , tarih, saat, PhoneNumber, para birimi, emaadresi vb. gibi birçok veri türü sağlar. `DataType` Özniteliği Ayrıca uygulamanın türe özgü özellikleri otomatik olarak sağlamasını da sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="ebfc3-125">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-125">For example:</span></span>

* <span data-ttu-id="ebfc3-126">`mailto:` Bağlantı için`DataType.EmailAddress`otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="ebfc3-127">Tarih Seçici çoğu tarayıcıda için `DataType.Date` verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="ebfc3-128">Öznitelik HTML 5 `data-` (bir veri Dash) özniteliklerini yayar. `DataType`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="ebfc3-129">`DataType` Öznitelikler doğrulama sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="ebfc3-130">DisplayFormat özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="ebfc3-131">`DataType.Date`görüntülenen tarihin biçimini belirtmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="ebfc3-132">Varsayılan olarak, Tarih alanı sunucunun [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)öğesine göre varsayılan biçimlere göre görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="ebfc3-133">`DisplayFormat` Öznitelik, tarih biçimini açıkça belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="ebfc3-134">`ApplyFormatInEditMode` Ayar, biçimlendirmenin düzenleme kullanıcı arabirimine de uygulanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="ebfc3-135">Bazı alanlar kullanmamanız `ApplyFormatInEditMode`gerekir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="ebfc3-136">Örneğin, para birimi simgesi genellikle bir düzenleme metin kutusunda gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="ebfc3-137">`DisplayFormat` Özniteliği kendi başına kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="ebfc3-138">Özniteliği `DataType` özniteliği`DisplayFormat` ile kullanmak genellikle iyi bir fikirdir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="ebfc3-139">`DataType` Özniteliği, bir ekranda nasıl işleneceğini değil, verilerin semantiğini alır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="ebfc3-140">Özniteliği `DataType` , içinde `DisplayFormat`kullanılamayan aşağıdaki avantajları sağlar:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="ebfc3-141">Tarayıcı HTML5 özelliklerini etkinleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="ebfc3-142">Örneğin, bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını ve istemci tarafı giriş doğrulamasını gösterin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="ebfc3-143">Varsayılan olarak tarayıcı, verileri yerel ayara göre doğru biçimi kullanarak işler.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="ebfc3-144">Daha fazla bilgi için bkz [ \<. Giriş > etiketi Yardımcısı belgeleri](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="ebfc3-145">StringLength özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="ebfc3-146">Veri doğrulama kuralları ve doğrulama hatası iletileri özniteliklerle belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="ebfc3-147">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) özniteliği, bir veri alanında izin verilen en düşük ve en fazla karakter uzunluğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="ebfc3-148">Gösterilen kod, adları 50 karakterden fazla olmayacak şekilde sınırlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="ebfc3-149">En küçük dize uzunluğunu ayarlayan bir örnek [daha sonra](#the-required-attribute)gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="ebfc3-150">`StringLength` Öznitelik Ayrıca istemci tarafı ve sunucu tarafı doğrulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="ebfc3-151">En küçük değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="ebfc3-152">Öznitelik `StringLength` , bir kullanıcının ad için boşluk girmesini engellemez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="ebfc3-153">[Cevap içerisinde RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) özniteliği, girişe kısıtlamalar uygulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="ebfc3-154">Örneğin, aşağıdaki kod ilk karakterin büyük küçük harf olmasını ve geri kalan karakterlerin alfabetik olmasını gerektirir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ebfc3-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ebfc3-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ebfc3-156">**SQL Server Nesne Gezgini** (ssox) içinde **öğrenci** tablosuna çift tıklayarak öğrenci tablosu tasarımcısını açın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Geçişle önce SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="ebfc3-158">Önceki görüntüde `Student` tablo için şema gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="ebfc3-159">Ad alanlarının türü `nvarchar(MAX)`vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="ebfc3-160">Bu öğreticide daha sonra bir geçiş oluşturulup uygulandığında, ad alanları dize uzunluğu özniteliklerinin bir `nvarchar(50)` sonucu olarak olur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ebfc3-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ebfc3-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ebfc3-162">SQLite aracınız içinde `Student` tablo için sütun tanımlarını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="ebfc3-163">Ad alanlarının türü `Text`vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-163">The name fields have type `Text`.</span></span> <span data-ttu-id="ebfc3-164">İlk ad alanının çağrıldığından `FirstMidName`emin olun.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="ebfc3-165">Sonraki bölümde, bu sütunun adını olarak `FirstName`değiştirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="ebfc3-166">Column özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="ebfc3-167">Öznitelikler sınıfların ve özelliklerin veritabanına nasıl eşlenildiğini denetleyebilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="ebfc3-168">Modelinde öznitelik, `FirstMidName` özelliğin adını veritabanında "FirstName" olarak eşlemek için kullanılır. `Column` `Student`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="ebfc3-169">Veritabanı oluşturulduğunda, modeldeki Özellik adları sütun adları için kullanılır ( `Column` özniteliği kullanıldığı durumlar dışında).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="ebfc3-170">Model, birinci `FirstMidName` ad alanı için kullanılır çünkü alan de bir orta ad içerebilir. `Student`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="ebfc3-171">Özniteliği ile, `Student.FirstMidName` `Student` veri modelinde tablonun sütunuyla eşlenir. `FirstName` `[Column]`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="ebfc3-172">`Column` Özniteliği ekleme modeli, `SchoolContext`öğesini yedekleyen olarak değiştirir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="ebfc3-173">' İ destekleyen `SchoolContext` model artık veritabanıyla eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="ebfc3-174">Bu tutarsızlık, daha sonra bu öğreticide bir geçiş eklenerek çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="ebfc3-175">Gerekli öznitelik</span><span class="sxs-lookup"><span data-stu-id="ebfc3-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="ebfc3-176">`Required` Özniteliği, ad özellikleri gerekli alanları yapar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="ebfc3-177">Öznitelik, değer türleri ( `DateTime`örneğin `int`,, ve `double`) gibi null yapılamayan türler için gerekli değildir. `Required`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="ebfc3-178">Null olmayan türler otomatik olarak gerekli alanlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="ebfc3-179">Özniteliğinin zorlanmak üzere ile birlikte `MinimumLength` `MinimumLength` kullanılması gerekir. `Required`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="ebfc3-180">`MinimumLength`ve `Required` doğrulamanın doğrulanmasını karşılamamak için boşluk.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="ebfc3-181">Dize üzerinde tam denetim için özniteliğinikullanın.`RegularExpression`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="ebfc3-182">Display özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="ebfc3-183">`Display` Öznitelik, metin kutuları için başlığın "ad", "soyadı", "tam ad" ve "kayıt tarihi" olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="ebfc3-184">Varsayılan açıklamalı alt yazıların sözcükleri bölen bir boşluk yoktu, örneğin "LastName".</span><span class="sxs-lookup"><span data-stu-id="ebfc3-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="ebfc3-185">Geçiş oluşturma</span><span class="sxs-lookup"><span data-stu-id="ebfc3-185">Create a migration</span></span>

<span data-ttu-id="ebfc3-186">Uygulamayı çalıştırın ve öğrenciler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="ebfc3-187">Bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-187">An exception is thrown.</span></span> <span data-ttu-id="ebfc3-188">Özniteliği, EF 'in adlı `FirstName`bir sütun bulmasını beklemesine neden olur, ancak veritabanındaki sütun adı hala kalır `FirstMidName`. `[Column]`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ebfc3-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ebfc3-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ebfc3-190">Hata iletisi aşağıdaki örneğe benzer:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="ebfc3-191">PMC 'de yeni bir geçiş oluşturmak ve veritabanını güncelleştirmek için aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="ebfc3-192">Bu komutlardan ilki aşağıdaki uyarı iletisini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="ebfc3-193">Ad alanları artık 50 karakterle sınırlı olduğundan uyarı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="ebfc3-194">Veritabanındaki bir ad 50 karakterden fazlasına sahipse, son karakter 51 ' i kaybedersiniz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="ebfc3-195">Öğrenci tablosunu SSOX içinde açın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-195">Open the Student table in SSOX:</span></span>

  ![Geçişlerde SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="ebfc3-197">Geçiş uygulanmadan önce ad sütunları [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)türünde idi.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="ebfc3-198">Ad sütunları artık `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="ebfc3-199">Sütun adı `FirstMidName` olarak `FirstName`değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ebfc3-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ebfc3-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ebfc3-201">Hata iletisi aşağıdaki örneğe benzer:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="ebfc3-202">Proje klasöründe bir komut penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-202">Open a command window in the project folder.</span></span> <span data-ttu-id="ebfc3-203">Yeni bir geçiş oluşturmak ve veritabanını güncelleştirmek için aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="ebfc3-204">Veritabanı güncelleştirme komutu aşağıdaki örnekte olduğu gibi bir hata görüntüler:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="ebfc3-205">Bu öğreticide, bu hatayı geçmenin yolu ilk geçişi silmek ve yeniden oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="ebfc3-206">Daha fazla bilgi için, [geçiş öğreticisinin](xref:data/ef-rp/migrations)en üstündeki SQLite uyarı notuna bakın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="ebfc3-207">*Geçişler* klasörünü silin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="ebfc3-208">Veritabanını bırakmak, yeni bir başlangıç geçişi oluşturmak ve geçişi uygulamak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="ebfc3-209">SQLite araclarınızın öğrenci tablosunu inceleyin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="ebfc3-210">FirstMidName sütunu artık FirstName.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="ebfc3-211">Uygulamayı çalıştırın ve öğrenciler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="ebfc3-212">Saatin giriş veya tarih ile birlikte görüntülenmediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="ebfc3-213">**Yeni oluştur**' u seçin ve 50 karakterden daha uzun bir ad girmeyi deneyin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="ebfc3-214">Aşağıdaki bölümlerde, uygulamanın bazı aşamalardan oluşturulması derleyici hataları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="ebfc3-215">Yönergeler uygulamanın ne zaman derbir olduğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="ebfc3-216">Eğitmen varlığı</span><span class="sxs-lookup"><span data-stu-id="ebfc3-216">The Instructor Entity</span></span>

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="ebfc3-218">Aşağıdaki kodla *modeller/eğitmen. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="ebfc3-219">Birden çok öznitelik tek bir satırda olabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="ebfc3-220">`HireDate` Öznitelikler aşağıdaki gibi yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="ebfc3-221">Gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="ebfc3-221">Navigation properties</span></span>

<span data-ttu-id="ebfc3-222">`CourseAssignments` Ve`OfficeAssignment` özellikleri gezinti özellikleridir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="ebfc3-223">Bir eğitmen herhangi bir sayıda kurs öğretebilir, bu `CourseAssignments` nedenle bir koleksiyon olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="ebfc3-224">Bir eğitmenin en fazla bir ofisi olabilir, bu nedenle `OfficeAssignment` özellik tek `OfficeAssignment` bir varlık içerir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="ebfc3-225">`OfficeAssignment`hiçbir Office atanmamışsa null olur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="ebfc3-226">OfficeAssignment varlığı</span><span class="sxs-lookup"><span data-stu-id="ebfc3-226">The OfficeAssignment entity</span></span>

![OfficeAssignment varlığı](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="ebfc3-228">Aşağıdaki kodla *modeller/OfficeAssignment. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="ebfc3-229">Anahtar özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-229">The Key attribute</span></span>

<span data-ttu-id="ebfc3-230">`[Key]` Öznitelik, özellik adı classnameıd veya ID dışında bir şey olduğunda, bir özelliği birincil anahtar (PK) olarak tanımlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="ebfc3-231">`Instructor` Ve`OfficeAssignment` varlıkları arasında bire sıfır veya-bir ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="ebfc3-232">Office ataması, atandığı eğitmenle ilişkili olarak yalnızca vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="ebfc3-233">PK Ayrıca `Instructor` varlığa ait yabancı anahtardır (FK). `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="ebfc3-234">EF Core, ID veya `InstructorID` classnameıd adlandırma `OfficeAssignment` kuralını `InstructorID` takip ettiğinden, ' ın ' ın ' i tarafından otomatik olarak tanıyamamaktadır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="ebfc3-235">Bu nedenle, `Key` özniteliği PK olarak tanımlamak `InstructorID` için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="ebfc3-236">Varsayılan olarak, EF Core, sütun tanımlayıcı bir ilişki için olduğundan, anahtarı veritabanı olmayan bir şekilde değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="ebfc3-237">Eğitmen gezintisi özelliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-237">The Instructor navigation property</span></span>

<span data-ttu-id="ebfc3-238">Belirtilen `Instructor.OfficeAssignment` bir eğitmen için bir `OfficeAssignment` satır olmadığı için, gezinti özelliği null olabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="ebfc3-239">Bir eğitmenin Office ataması olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="ebfc3-240">Yabancı anahtar`InstructorID` türü `OfficeAssignment.Instructor` nullyapılamayanbirdeğertürüolduğundan,gezintiözelliğiherzamanbireğitmen`int`varlığına sahip olur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="ebfc3-241">Bir Office ataması, bir eğitmen olmadan bulunamaz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="ebfc3-242">Bir `Instructor` varlık ilişkili `OfficeAssignment` bir varlığa sahip olduğunda, her varlığın gezinti özelliğinde diğer bir başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="ebfc3-243">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="ebfc3-243">The Course Entity</span></span>

![Kurs varlığı](complex-data-model/_static/course-entity.png)

<span data-ttu-id="ebfc3-245">*Modelleri/kursu. cs* aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="ebfc3-246">Varlığın yabancı anahtar (FK) özelliği `DepartmentID`vardır. `Course`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="ebfc3-247">`DepartmentID`ilgili `Department` varlığa işaret eder.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="ebfc3-248">`Course` Varlığın bir`Department` gezinti özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="ebfc3-249">EF Core, modelin ilgili bir varlık için gezinti özelliği olduğunda bir veri modeli için yabancı anahtar özelliği gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="ebfc3-250">EF Core, gerektiği yerde otomatik olarak veritabanında FKs 'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="ebfc3-251">EF Core otomatik olarak oluşturulan FKs 'ler için [gölge Özellikler](/ef/core/modeling/shadow-properties) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="ebfc3-252">Ancak, doğrudan veri modelinde FK dahil edilmesi, güncelleştirmelerin daha basit ve daha verimli olmasını sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="ebfc3-253">Örneğin, FK özelliğinin `DepartmentID` dahil *olmadığı* bir model düşünün.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="ebfc3-254">Bir kurs varlığı düzenlemek üzere getirilirken:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="ebfc3-255">Açık bir şekilde yüklenmediyse özelliğinullolur.`Department`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="ebfc3-256">Kurs varlığını `Department` güncelleştirmek için önce varlığın getirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="ebfc3-257">FK özelliği `DepartmentID` veri modeline dahil edildiğinde, bir güncelleştirmeden önce `Department` varlığı getirme gerekmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="ebfc3-258">DatabaseGenerated özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="ebfc3-259">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` Özniteliği, PK 'nin veritabanı tarafından oluşturulması yerine uygulama tarafından sağlandığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="ebfc3-260">Varsayılan olarak, EF Core PK değerlerinin veritabanı tarafından oluşturulduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="ebfc3-261">Veritabanı tarafından oluşturulan genellikle en iyi yaklaşım vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="ebfc3-262">Varlıklar `Course` için Kullanıcı PK 'yi belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="ebfc3-263">Örneğin, matematik departmanı için 1000 serisi, Ingilizce departmanı için 2000 serisi gibi bir kurs numarası.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="ebfc3-264">`DatabaseGenerated` Öznitelik varsayılan değerler oluşturmak için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="ebfc3-265">Örneğin, veritabanı bir satırın oluşturulduğu veya güncelleştirildiği tarihi kaydetmek için otomatik olarak bir tarih alanı oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="ebfc3-266">Daha fazla bilgi için bkz. [üretilen Özellikler](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ebfc3-267">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="ebfc3-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="ebfc3-268">`Course` Varlıktaki yabancı anahtar (FK) özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="ebfc3-269">Bir kurs bir departmana atanır, bu nedenle bir `DepartmentID` FK ve bir `Department` gezinti özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="ebfc3-270">Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir, bu nedenle `Enrollments` gezinti özelliği bir koleksiyondur:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="ebfc3-271">Kurs, birden fazla eğitmen tarafından tada olabilir, bu nedenle `CourseAssignments` gezinti özelliği bir koleksiyon olur:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="ebfc3-272">`CourseAssignment`[daha sonra](#many-to-many-relationships)açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="ebfc3-273">Departman varlığı</span><span class="sxs-lookup"><span data-stu-id="ebfc3-273">The Department entity</span></span>

![Bölüm varlığı](complex-data-model/_static/department-entity.png)

<span data-ttu-id="ebfc3-275">Aşağıdaki kodla *modeller/departman. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="ebfc3-276">Column özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-276">The Column attribute</span></span>

<span data-ttu-id="ebfc3-277">Daha önce `Column` öznitelik, sütun adı eşlemesini değiştirmek için kullanıldı.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="ebfc3-278">`Department` Varlığın kodunda`Column` , özniteliği SQL veri türü eşlemesini değiştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="ebfc3-279">`Budget` Sütun, veritabanında SQL Server para türü kullanılarak tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="ebfc3-280">Sütun eşlemesi genellikle gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-280">Column mapping is generally not required.</span></span> <span data-ttu-id="ebfc3-281">EF Core, özelliğin CLR türüne göre uygun SQL Server veri türünü seçer.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="ebfc3-282">CLR `decimal` türü SQL Server `decimal` bir türe eşlenir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="ebfc3-283">`Budget`para birimi için, para veri türü ise para birimi için daha uygundur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ebfc3-284">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="ebfc3-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="ebfc3-285">FK ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="ebfc3-286">Bir departman yönetici olabilir veya olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="ebfc3-287">Yönetici her zaman bir eğitmendir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-287">An administrator is always an instructor.</span></span> <span data-ttu-id="ebfc3-288">Bu nedenle, `Instructor` özelliği varlığa FK olarak dahil edilir. `InstructorID`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="ebfc3-289">Gezinti özelliği adlandırılır `Administrator` ancak bir `Instructor` varlık barındırır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="ebfc3-290">Önceki koddaki soru işareti (?) özelliği null yapılabilir olduğunu belirtiyor.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="ebfc3-291">Bir departmanın birçok kursu olabilir, bu nedenle bir kurs gezintisi özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="ebfc3-292">Kural gereği EF Core, null yapılamayan ve çok-çok ilişkiler için basamaklı silme imkanı sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="ebfc3-293">Bu varsayılan davranış, dairesel basamaklı silme kurallarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="ebfc3-294">Bir geçiş eklendiğinde dairesel basamaklı silme kuralları bir özel duruma neden olur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="ebfc3-295">Örneğin, `Department.InstructorID` özellik null yapılamayan olarak tanımlanmışsa EF Core basamaklı silme kuralı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="ebfc3-296">Bu durumda, yönetici olarak atanan eğitmen silindiğinde departman silinir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="ebfc3-297">Bu senaryoda kısıtlama kuralı daha anlamlı hale getirir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="ebfc3-298">Aşağıdaki [Fluent API](#fluent-api-alternative-to-attributes) bir kısıtlama kuralı ayarlar ve art arda silmeyi devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="ebfc3-299">Kayıt varlık</span><span class="sxs-lookup"><span data-stu-id="ebfc3-299">The Enrollment entity</span></span>

<span data-ttu-id="ebfc3-300">Kayıt kaydı, tek bir öğrenci tarafından gerçekleştirilen bir kurs içindir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-300">An enrollment record is for one course taken by one student.</span></span>

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="ebfc3-302">*Modelleri/kaydı. cs* 'yi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ebfc3-303">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="ebfc3-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="ebfc3-304">FK özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="ebfc3-305">Kayıt kaydı tek bir kurs için olduğundan, `CourseID` FK özelliği ve bir `Course` gezinti özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="ebfc3-306">Kayıt kaydı bir öğrenci içindir, bu nedenle bir `StudentID` FK özelliği ve bir `Student` gezinti özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="ebfc3-307">Çoktan çoğa Ilişkiler</span><span class="sxs-lookup"><span data-stu-id="ebfc3-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="ebfc3-308">`Student` Ve`Course` varlıkları arasında çoktan çoğa bir ilişki vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="ebfc3-309">Varlık `Enrollment` , veritabanında *Yük içeren* çoktan çoğa bir JOIN tablosu olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="ebfc3-310">"Yükle", `Enrollment` tablonun birleştirilmiş tablolar için FKS 'ler (Bu durumda, PK ve `Grade`) gibi ek veriler içerdiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="ebfc3-311">Aşağıdaki çizimde bu ilişkilerin bir varlık diyagramında nasıl göründüğünü gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="ebfc3-312">(Bu diyagram EF 6. x için [EF güç araçları](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="ebfc3-313">Diyagram oluşturmak öğreticinin bir parçası değildir.)</span><span class="sxs-lookup"><span data-stu-id="ebfc3-313">Creating the diagram isn't part of the tutorial.)</span></span>

![Öğrenci-çok fazla ilişki](complex-data-model/_static/student-course.png)

<span data-ttu-id="ebfc3-315">Her ilişki ucu ve bir yıldız işareti (\*) 1 diğer sırasında bir-çok ilişkisi belirten bulunur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="ebfc3-316">Tablo, sınıf bilgileri içermiyorsa, yalnızca iki FKS (`CourseID` ve `StudentID`) içermesi gerekir. `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="ebfc3-317">Yük olmadan çoktan çoğa bir JOIN tablosu bazen saf JOIN tablosu (PJT) olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="ebfc3-318">`Instructor` Ve`Course` varlıklarının saf bir JOIN tablosu kullanılarak çoktan çoğa bir ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="ebfc3-319">Not: EF 6. x, çoktan çoğa ilişkiler için örtük birleştirmeyi destekler, ancak EF Core değildir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="ebfc3-320">Daha fazla bilgi için [EF Core 2,0 ' de çoktan çoğa ilişkiler](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="ebfc3-321">Courseatama varlığı</span><span class="sxs-lookup"><span data-stu-id="ebfc3-321">The CourseAssignment entity</span></span>

![Courseatama varlığı](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="ebfc3-323">Aşağıdaki kodla *modeller/Courseatama. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="ebfc3-324">Eğitmenden çok-çok ilişkisi için bir JOIN tablosu gerekir ve bu ekleme tablosu için varlık kurs atamasıdır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="ebfc3-326">Bir JOIN varlığına `EntityName1EntityName2`ad vermek yaygındır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="ebfc3-327">Örneğin, bu model kullanılarak eğitmen-kurslar 'a katılması tablosu olacaktır `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="ebfc3-328">Ancak, ilişkiyi açıklayan bir ad kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="ebfc3-329">Veri modelleri basit ve büyümeye başlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-329">Data models start out simple and grow.</span></span> <span data-ttu-id="ebfc3-330">Yük (PJTs) olmayan ekleme tabloları genellikle yükü içerecek şekilde gelişmektedir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="ebfc3-331">Açıklayıcı bir varlık adıyla başlayarak, ekleme tablosu değiştiğinde adın değiştirilmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="ebfc3-332">İdeal olarak, JOIN varlığının iş etki alanında kendi doğal (muhtemelen tek bir kelime) adına sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="ebfc3-333">Örneğin, kitaplar ve müşteriler, derecelendirmeler adlı bir JOIN varlığıyla bağlantı kurulabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="ebfc3-334">Eğitmenin kursa çok-çok ilişkisi `CourseAssignment` için `CourseInstructor`tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="ebfc3-335">Bileşik anahtar</span><span class="sxs-lookup"><span data-stu-id="ebfc3-335">Composite key</span></span>

<span data-ttu-id="ebfc3-336">( `CourseAssignment` `InstructorID` Ve )`CourseID`içindekiiki FKS, tablonunhersatırınıbenzersiz`CourseAssignment` bir şekilde tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="ebfc3-337">`CourseAssignment`adanmış bir PK gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="ebfc3-338">`InstructorID` Ve`CourseID` özellikleri bileşik bir PK olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="ebfc3-339">EF Core bileşik PKs 'leri belirtmenin tek yolu *Fluent API*' dir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="ebfc3-340">Sonraki bölümde, bileşik PK 'nin nasıl yapılandırılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="ebfc3-341">Bileşik anahtar şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-341">The composite key ensures that:</span></span>

* <span data-ttu-id="ebfc3-342">Tek bir kurs için birden çok satıra izin verilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="ebfc3-343">Birden çok satıra bir eğitmen için izin verilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="ebfc3-344">Aynı eğitmen ve kurs için birden çok satıra izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="ebfc3-345">`Enrollment` JOIN varlığı kendi PK 'yi tanımlar, bu nedenle bu sıralamanın yinelemeleri mümkündür.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="ebfc3-346">Bu tür yinelemeleri engellemek için:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="ebfc3-347">FK alanlara benzersiz bir dizin ekleyin veya</span><span class="sxs-lookup"><span data-stu-id="ebfc3-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="ebfc3-348">`Enrollment` İle`CourseAssignment`benzer bir birincil bileşik anahtarla yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="ebfc3-349">Daha fazla bilgi için bkz. [dizinler](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="ebfc3-350">Veritabanı bağlamını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="ebfc3-350">Update the database context</span></span>

<span data-ttu-id="ebfc3-351">*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="ebfc3-352">Yukarıdaki kod, yeni varlıkları ekler ve `CourseAssignment` varlığın bileşik PK 'yi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="ebfc3-353">Tutarlı API 'nin özniteliklere alternatif</span><span class="sxs-lookup"><span data-stu-id="ebfc3-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="ebfc3-354">Önceki koddaki yöntemi EF Core davranışını yapılandırmak için Fluent API kullanır. `OnModelCreating`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="ebfc3-355">Genellikle tek bir bildirimde bir dizi yöntem çağrısı olan dize olarak kullanıldığından, API "akıcı" olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="ebfc3-356">[Aşağıdaki kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) Fluent API bir örneğidir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="ebfc3-357">Bu öğreticide, Fluent API yalnızca özniteliklerle yapılamadığını veritabanı eşlemesi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="ebfc3-358">Ancak Fluent API, özniteliklerle yapılabilecek biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="ebfc3-359">Gibi bazı öznitelikler `MinimumLength` Fluent API uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="ebfc3-360">`MinimumLength`şemayı değiştirmez, yalnızca bir minimum uzunluk doğrulama kuralı uygular.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="ebfc3-361">Bazı geliştiriciler, varlık sınıflarının "temiz" olmasını sağlamak için Fluent API özel olarak kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="ebfc3-362">Öznitelikler ve Fluent API karışık olabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="ebfc3-363">Yalnızca Fluent API (bileşik bir PK belirterek) yapılabilecek bazı konfigürasyonlar vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="ebfc3-364">Yalnızca özniteliklerle (`MinimumLength`) yapılabilecek bazı konfigürasyonlar vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="ebfc3-365">Fluent API veya özniteliklerini kullanmak için önerilen uygulama:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="ebfc3-366">Bu iki yaklaşımdan birini seçin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="ebfc3-367">Seçilen yaklaşımı mümkün olduğunca düzenli olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="ebfc3-368">Bu öğreticide kullanılan özniteliklerin bazıları için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="ebfc3-369">Yalnızca doğrulama (örneğin, `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="ebfc3-370">Yalnızca yapılandırma EF Core (örneğin, `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="ebfc3-371">Doğrulama ve EF Core yapılandırma (örneğin, `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="ebfc3-372">Öznitelikler ile Fluent API hakkında daha fazla bilgi için bkz. [yapılandırma yöntemleri](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="ebfc3-373">Varlık diyagramı</span><span class="sxs-lookup"><span data-stu-id="ebfc3-373">Entity diagram</span></span>

<span data-ttu-id="ebfc3-374">Aşağıdaki çizimde, tamamlanmış okul modeli için EF Power Tools 'un oluştura diyagramı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="ebfc3-376">Önceki diyagramda şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="ebfc3-377">Birden çok çoktan çoğa ilişki satırı (1 ile \*).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="ebfc3-378">`Instructor` Ve`OfficeAssignment` varlıkları arasında bire sıfır veya-bir ilişki çizgisi (1 ila 0.. 1).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="ebfc3-379">`Instructor` Ve`Department` varlıkları arasında sıfır veya-bire çok ilişki çizgisi (0.. 1-\*).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="ebfc3-380">Veritabanının çekirdeğini oluşturma</span><span class="sxs-lookup"><span data-stu-id="ebfc3-380">Seed the database</span></span>

<span data-ttu-id="ebfc3-381">*Data/Dbınizer. cs*dosyasındaki kodu güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="ebfc3-382">Yukarıdaki kod, yeni varlıklar için tohum verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="ebfc3-383">Bu kodun çoğu yeni varlık nesneleri oluşturur ve örnek verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="ebfc3-384">Örnek veriler test için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-384">The sample data is used for testing.</span></span> <span data-ttu-id="ebfc3-385">Birden `Enrollments` çok `CourseAssignments` -çok JOIN tablosunun nasıl çalıştırılabilir olduğunu gösteren örnekler için bkz. ve.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="ebfc3-386">Geçiş Ekle</span><span class="sxs-lookup"><span data-stu-id="ebfc3-386">Add a migration</span></span>

<span data-ttu-id="ebfc3-387">Projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-387">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ebfc3-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ebfc3-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ebfc3-389">PMC 'de aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="ebfc3-390">Yukarıdaki komut, olası veri kaybı hakkında bir uyarı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="ebfc3-391">`database update` Komut çalıştırıldığında, aşağıdaki hata oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="ebfc3-392">Sonraki bölümde, bu hatayla ilgili ne yapılacağını görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ebfc3-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ebfc3-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ebfc3-394">Bir geçiş ekler ve `database update` komutu çalıştırırsanız, aşağıdaki hata üretilir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="ebfc3-395">Sonraki bölümde, bu hatanın nasıl önleneceğini görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="ebfc3-396">Geçişi uygulayın veya bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="ebfc3-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="ebfc3-397">Artık var olan bir veritabanınız olduğuna göre, değişikliklere nasıl uygulanacağını düşünmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="ebfc3-398">Bu öğreticide iki alternatif gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="ebfc3-399">[Veritabanını bırakıp yeniden oluşturun](#drop).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="ebfc3-400">SQLite kullanıyorsanız bu bölümü seçin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="ebfc3-401">[Geçişi mevcut veritabanına uygulayın](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="ebfc3-402">Bu bölümdeki yönergeler yalnızca SQL Server için geçerlidir, **SQLite için değildir**.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="ebfc3-403">Her iki seçenek de SQL Server için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="ebfc3-404">Apply-Migration yöntemi daha karmaşıktır ve zaman alabilir. Bu, gerçek dünyada üretim ortamları için tercih edilen yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="ebfc3-405">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="ebfc3-405">Drop and re-create the database</span></span>

<span data-ttu-id="ebfc3-406">SQL Server kullanıyorsanız ve aşağıdaki bölümde uygulanacak geçiş yaklaşımını yapmak istiyorsanız [Bu bölümü atlayın](#apply-the-migration) .</span><span class="sxs-lookup"><span data-stu-id="ebfc3-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="ebfc3-407">Yeni bir veritabanı oluşturmak için EF Core zorlamak için veritabanını bırakıp güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ebfc3-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ebfc3-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ebfc3-409">**Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="ebfc3-410">*Geçişler* klasörünü silin, ardından aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ebfc3-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ebfc3-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ebfc3-412">Bir komut penceresi açın ve proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="ebfc3-413">Proje klasörü *Contosouniversity. csproj* dosyasını içerir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="ebfc3-414">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-414">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="ebfc3-415">*Geçişler* klasörünü silin, ardından aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="ebfc3-416">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-416">Run the app.</span></span> <span data-ttu-id="ebfc3-417">Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="ebfc3-418">Yeni `DbInitializer.Initialize` veritabanını doldurur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ebfc3-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ebfc3-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ebfc3-420">Veritabanını SSOX içinde açın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="ebfc3-421">Daha önce SSOX açıldıysa **Yenile** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="ebfc3-422">Genişletin **tabloları** düğümü.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-422">Expand the **Tables** node.</span></span> <span data-ttu-id="ebfc3-423">Oluşturulan tablolar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-423">The created tables are displayed.</span></span>

  ![SSOX içindeki tablolar](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="ebfc3-425">**Courseatama** tablosunu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="ebfc3-426">**Courseatama** tablosuna sağ tıklayın ve **verileri görüntüle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="ebfc3-427">**Courseatama** tablosunun veri içerdiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![SSOX 'te Courseatama verileri](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ebfc3-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ebfc3-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ebfc3-430">Veritabanını incelemek için SQLite aracınızı kullanın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="ebfc3-431">Yeni tablolar ve sütunlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-431">New tables and columns.</span></span>
* <span data-ttu-id="ebfc3-432">Tablolardaki verileri, örneğin, **Courseatama** tablosu.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="ebfc3-433">Geçişi Uygula</span><span class="sxs-lookup"><span data-stu-id="ebfc3-433">Apply the migration</span></span>

<span data-ttu-id="ebfc3-434">Bu bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-434">This section is optional.</span></span> <span data-ttu-id="ebfc3-435">Bu adımlar yalnızca SQL Server LocalDB için çalışır ve yalnızca önceki [bırakma ve veritabanını yeniden oluştur](#drop) bölümünü atladıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="ebfc3-436">Geçişler mevcut verilerle çalıştırıldığında, mevcut verilerin karşılanmadığı FK kısıtlamalar olabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="ebfc3-437">Üretim verileriyle, mevcut verilerin geçirilmesi için adımların alınması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="ebfc3-438">Bu bölüm, FK kısıtlama ihlallerinin düzeltilmesiyle bir örnek sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="ebfc3-439">Bu kod değişikliklerini yedekleme olmadan yapmayın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="ebfc3-440">Önceki [bırakmayı tamamlayıp veritabanını yeniden oluşturduktan sonra](#drop) Bu kod değişikliklerini yapmayın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="ebfc3-441">*{Timestamp} _ComplexDataModel. cs* dosyası aşağıdaki kodu içerir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="ebfc3-442">Yukarıdaki kod, `DepartmentID` `Course` tabloya null yapılamayan bir FK ekler.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="ebfc3-443">Önceki öğreticideki veritabanı, içindeki `Course`satırları içerir, böylece tablo geçişler tarafından güncelleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="ebfc3-444">`ComplexDataModel` Geçişin mevcut verilerle çalışmasını sağlamak için:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="ebfc3-445">Yeni sütuna (`DepartmentID`) varsayılan değer vermek için kodu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="ebfc3-446">Varsayılan departman olarak davranacak "Temp" adlı sahte bir departman oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="ebfc3-447">Yabancı anahtar kısıtlamalarını çözme</span><span class="sxs-lookup"><span data-stu-id="ebfc3-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="ebfc3-448">Geçiş sınıfında, `Up` yöntemi güncelleştirin: `ComplexDataModel`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="ebfc3-449">*{Timestamp} _ComplexDataModel. cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="ebfc3-450">`DepartmentID` Sütunu `Course` tabloya ekleyen kod satırını açıklama olarak yapın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="ebfc3-451">Aşağıdaki vurgulanmış kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-451">Add the following highlighted code.</span></span> <span data-ttu-id="ebfc3-452">Yeni kod `.CreateTable( name: "Department"` bloğundan sonra geçer:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

<span data-ttu-id="ebfc3-453">[! Code-CSharp [] (giriş/örnekler/cu30snapshots/5-Complex/geçişler/ComplexDataModel. cs? Name = snippet_CreateDefaultValue & vurgulama = 23-31)]</span><span class="sxs-lookup"><span data-stu-id="ebfc3-453">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span></span>

<span data-ttu-id="ebfc3-454">Önceki değişikliklerle, varolan `Course` satırlar, `ComplexDataModel.Up` Yöntem çalıştıktan sonra "geçici" departmanıyla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-454">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="ebfc3-455">Burada gösterilen durumu işlemenin yolu, bu öğretici için basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-455">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="ebfc3-456">Bir üretim uygulaması şöyle olacaktır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-456">A production app would:</span></span>

* <span data-ttu-id="ebfc3-457">Yeni `Course` `Department` satırlarasatırlarveilgilisatırlareklemekiçinkodveyakomutdosyalarıekleyin.`Department`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-457">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="ebfc3-458">İçin `Course.DepartmentID`"geçici" Departmanı veya varsayılan değeri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-458">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ebfc3-459">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ebfc3-459">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ebfc3-460">**Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-460">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="ebfc3-461">`DbInitializer.Initialize` Yöntemi yalnızca boş bir veritabanıyla çalışacak şekilde tasarlandığından, öğrenci ve kurs tablolarındaki tüm satırları silmek için ssox kullanın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-461">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="ebfc3-462">(Cascade silme, kayıt tablosundan işlem gerçekleştirir.)</span><span class="sxs-lookup"><span data-stu-id="ebfc3-462">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ebfc3-463">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ebfc3-463">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ebfc3-464">Visual Studio Code ile SQL Server LocalDB kullanıyorsanız şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-464">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="ebfc3-465">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-465">Run the app.</span></span> <span data-ttu-id="ebfc3-466">Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-466">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="ebfc3-467">Yeni `DbInitializer.Initialize` veritabanını doldurur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-467">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ebfc3-468">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="ebfc3-468">Next steps</span></span>

<span data-ttu-id="ebfc3-469">Sonraki iki öğretici ilgili verilerin nasıl okunacağını ve güncelleştirilmesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-469">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ebfc3-470">[Önceki öğretici](xref:data/ef-rp/migrations)
> [sonraki öğretici](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="ebfc3-470">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ebfc3-471">Önceki öğreticiler, üç varlıktan oluşan temel bir veri modeliyle çalışmıştır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-471">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="ebfc3-472">Bu öğreticide:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-472">In this tutorial:</span></span>

* <span data-ttu-id="ebfc3-473">Daha fazla varlık ve ilişki eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-473">More entities and relationships are added.</span></span>
* <span data-ttu-id="ebfc3-474">Veri modeli biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirtilerek özelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-474">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="ebfc3-475">Tamamlanan veri modeli için varlık sınıfları aşağıdaki çizimde gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-475">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="ebfc3-477">Çözemediğiniz sorunlarla karşılaşırsanız, [tamamlanmış uygulamayı](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)indirin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-477">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="ebfc3-478">Veri modelini özniteliklerle özelleştirme</span><span class="sxs-lookup"><span data-stu-id="ebfc3-478">Customize the data model with attributes</span></span>

<span data-ttu-id="ebfc3-479">Bu bölümde, veri modeli öznitelikler kullanılarak özelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-479">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="ebfc3-480">DataType özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-480">The DataType attribute</span></span>

<span data-ttu-id="ebfc3-481">Öğrenci sayfaları Şu anda kayıt tarihinin saatini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-481">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="ebfc3-482">Genellikle, tarih alanları saati değil yalnızca tarihi gösterir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-482">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="ebfc3-483">*Modelleri/öğrenci. cs* 'yi aşağıdaki vurgulanmış kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-483">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="ebfc3-484">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) özniteliği, veritabanı iç türünden daha belirgin bir veri türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-484">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="ebfc3-485">Bu durumda, tarih ve saat değil yalnızca tarih görüntülenmelidir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-485">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="ebfc3-486">Veri [türü numaralandırması](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) , tarih, saat, PhoneNumber, para birimi, emaadresi vb. gibi birçok veri türü sağlar. `DataType` Özniteliği Ayrıca uygulamanın türe özgü özellikleri otomatik olarak sağlamasını da sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-486">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="ebfc3-487">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-487">For example:</span></span>

* <span data-ttu-id="ebfc3-488">`mailto:` Bağlantı için`DataType.EmailAddress`otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-488">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="ebfc3-489">Tarih Seçici çoğu tarayıcıda için `DataType.Date` verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-489">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="ebfc3-490">Özniteliği HTML 5 tarayıcılarının kullandığı `data-` HTML 5 (bir veri Dash) özniteliklerini yayar. `DataType`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-490">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="ebfc3-491">`DataType` Öznitelikler doğrulama sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-491">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="ebfc3-492">`DataType.Date`görüntülenen tarihin biçimini belirtmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-492">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="ebfc3-493">Varsayılan olarak, Tarih alanı sunucunun [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)öğesine göre varsayılan biçimlere göre görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-493">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="ebfc3-494">`DisplayFormat` Öznitelik, tarih biçimini açıkça belirtmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-494">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="ebfc3-495">`ApplyFormatInEditMode` Ayar, biçimlendirmenin düzenleme kullanıcı arabirimine de uygulanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-495">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="ebfc3-496">Bazı alanlar kullanmamanız `ApplyFormatInEditMode`gerekir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-496">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="ebfc3-497">Örneğin, para birimi simgesi genellikle bir düzenleme metin kutusunda gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-497">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="ebfc3-498">`DisplayFormat` Özniteliği kendi başına kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-498">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="ebfc3-499">Özniteliği `DataType` özniteliği`DisplayFormat` ile kullanmak genellikle iyi bir fikirdir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-499">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="ebfc3-500">`DataType` Özniteliği, bir ekranda nasıl işleneceğini değil, verilerin semantiğini alır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-500">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="ebfc3-501">Özniteliği `DataType` , içinde `DisplayFormat`kullanılamayan aşağıdaki avantajları sağlar:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-501">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="ebfc3-502">Tarayıcı HTML5 özelliklerini etkinleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-502">The browser can enable HTML5 features.</span></span> <span data-ttu-id="ebfc3-503">Örneğin, bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını, istemci tarafı giriş doğrulamasını vb. göster</span><span class="sxs-lookup"><span data-stu-id="ebfc3-503">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="ebfc3-504">Varsayılan olarak tarayıcı, verileri yerel ayara göre doğru biçimi kullanarak işler.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-504">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="ebfc3-505">Daha fazla bilgi için bkz [ \<. Giriş > etiketi Yardımcısı belgeleri](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-505">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="ebfc3-506">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-506">Run the app.</span></span> <span data-ttu-id="ebfc3-507">Öğrenciler dizin sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-507">Navigate to the Students Index page.</span></span> <span data-ttu-id="ebfc3-508">Süreler artık görüntülenmiyor.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-508">Times are no longer displayed.</span></span> <span data-ttu-id="ebfc3-509">`Student` Modeli kullanan her görünüm tarihi zaman içinde görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-509">Every view that uses the `Student` model displays the date without time.</span></span>

![Öğrenciler Dizin sayfası tarihleri zamansız gösterme](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="ebfc3-511">StringLength özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-511">The StringLength attribute</span></span>

<span data-ttu-id="ebfc3-512">Veri doğrulama kuralları ve doğrulama hatası iletileri özniteliklerle belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-512">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="ebfc3-513">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) özniteliği, bir veri alanında izin verilen en düşük ve en fazla karakter uzunluğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-513">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="ebfc3-514">`StringLength` Öznitelik Ayrıca istemci tarafı ve sunucu tarafı doğrulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-514">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="ebfc3-515">En küçük değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-515">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="ebfc3-516">`Student` Modeli aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-516">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="ebfc3-517">Yukarıdaki kod, adları 50 karakterden fazla olmayacak şekilde sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-517">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="ebfc3-518">Öznitelik `StringLength` , bir kullanıcının ad için boşluk girmesini engellemez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-518">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="ebfc3-519">[Cevap içerisinde RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) özniteliği, girişe kısıtlamalar uygulamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-519">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="ebfc3-520">Örneğin, aşağıdaki kod ilk karakterin büyük küçük harf olmasını ve geri kalan karakterlerin alfabetik olmasını gerektirir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-520">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="ebfc3-521">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-521">Run the app:</span></span>

* <span data-ttu-id="ebfc3-522">Öğrenciler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-522">Navigate to the Students page.</span></span>
* <span data-ttu-id="ebfc3-523">**Yeni oluştur**' u seçin ve 50 karakterden daha uzun bir ad girin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-523">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="ebfc3-524">**Oluştur**' u seçin, istemci tarafı doğrulama bir hata iletisi gösterir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-524">Select **Create**, client-side validation shows an error message.</span></span>

![Öğrenciler Dizin sayfası dize uzunluğu hatalarını gösteriyor](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="ebfc3-526">**SQL Server Nesne Gezgini** (ssox) içinde **öğrenci** tablosuna çift tıklayarak öğrenci tablosu tasarımcısını açın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-526">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Geçişle önce SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="ebfc3-528">Önceki görüntüde `Student` tablo için şema gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-528">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="ebfc3-529">Veritabanı üzerinde geçişler çalıştırılmadığından `nvarchar(MAX)` ad alanlarının türü var.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-529">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="ebfc3-530">Bu öğreticide daha sonra geçişler çalıştırıldığında, ad alanları olur `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-530">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="ebfc3-531">Column özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-531">The Column attribute</span></span>

<span data-ttu-id="ebfc3-532">Öznitelikler sınıfların ve özelliklerin veritabanına nasıl eşlenildiğini denetleyebilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-532">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="ebfc3-533">Bu bölümde, `Column` özniteliği, `FirstMidName` özelliğin adını DB 'deki "FirstName" olarak eşlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-533">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="ebfc3-534">DB oluşturulduğunda modeldeki Özellik adları sütun adları için kullanılır ( `Column` özniteliği kullanıldığı durumlar dışında).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-534">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="ebfc3-535">Model, birinci `FirstMidName` ad alanı için kullanılır çünkü alan de bir orta ad içerebilir. `Student`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-535">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="ebfc3-536">*Student.cs* dosyasını aşağıdaki vurgulanmış kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-536">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="ebfc3-537">Uygulamanın önceki değişikliği `Student.FirstMidName` ile `Student` tablonun `FirstName` sütunuyla eşlenir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-537">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="ebfc3-538">`Column` Özniteliği ekleme modeli, `SchoolContext`öğesini yedekleyen olarak değiştirir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-538">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="ebfc3-539">' İ destekleyen `SchoolContext` model artık veritabanıyla eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-539">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="ebfc3-540">Geçiş uygulamadan önce uygulama çalışıyorsa aşağıdaki özel durum oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-540">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="ebfc3-541">DB 'yi güncelleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-541">To update the DB:</span></span>

* <span data-ttu-id="ebfc3-542">Projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-542">Build the project.</span></span>
* <span data-ttu-id="ebfc3-543">Proje klasöründe bir komut penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-543">Open a command window in the project folder.</span></span> <span data-ttu-id="ebfc3-544">Yeni bir geçiş oluşturmak ve DB 'yi güncelleştirmek için aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-544">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ebfc3-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ebfc3-545">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ebfc3-546">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ebfc3-546">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="ebfc3-547">`migrations add ColumnFirstName` Komut aşağıdaki uyarı iletisini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-547">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="ebfc3-548">Ad alanları artık 50 karakterle sınırlı olduğundan uyarı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-548">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="ebfc3-549">VERITABANıNDAKI bir ad 50 karakterden fazlasına sahipse, son karakter 51 ' i kaybedersiniz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-549">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="ebfc3-550">Uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-550">Test the app.</span></span>

<span data-ttu-id="ebfc3-551">Öğrenci tablosunu SSOX içinde açın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-551">Open the Student table in SSOX:</span></span>

![Geçişlerde SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="ebfc3-553">Geçiş uygulanmadan önce ad sütunları [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)türünde idi.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-553">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="ebfc3-554">Ad sütunları artık `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-554">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="ebfc3-555">Sütun adı `FirstMidName` olarak `FirstName`değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-555">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="ebfc3-556">Aşağıdaki bölümde, uygulamanın bazı aşamalardan oluşturulması derleyici hataları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-556">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="ebfc3-557">Yönergeler uygulamanın ne zaman derbir olduğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-557">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="ebfc3-558">Öğrenci varlık güncelleştirmesi</span><span class="sxs-lookup"><span data-stu-id="ebfc3-558">Student entity update</span></span>

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

<span data-ttu-id="ebfc3-560">*Modelleri/öğrenci. cs* 'yi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-560">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="ebfc3-561">Gerekli öznitelik</span><span class="sxs-lookup"><span data-stu-id="ebfc3-561">The Required attribute</span></span>

<span data-ttu-id="ebfc3-562">`Required` Özniteliği, ad özellikleri gerekli alanları yapar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-562">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="ebfc3-563">Öznitelik `Required` , değer türleri (`DateTime`, `int`, `double`vb.) gibi null yapılamayan türler için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-563">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="ebfc3-564">Null olmayan türler otomatik olarak gerekli alanlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-564">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="ebfc3-565">Öznitelik, `StringLength` özniteliğinde bir minimum length parametresiyle değiştirilebilir: `Required`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-565">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="ebfc3-566">Display özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-566">The Display attribute</span></span>

<span data-ttu-id="ebfc3-567">`Display` Öznitelik, metin kutuları için başlığın "ad", "soyadı", "tam ad" ve "kayıt tarihi" olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-567">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="ebfc3-568">Varsayılan açıklamalı alt yazıların sözcükleri bölen bir boşluk yoktu, örneğin "LastName".</span><span class="sxs-lookup"><span data-stu-id="ebfc3-568">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="ebfc3-569">FullName hesaplanmış özelliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-569">The FullName calculated property</span></span>

<span data-ttu-id="ebfc3-570">`FullName`, iki diğer özelliğin bitiştirerek oluşturulmuş bir değer döndüren hesaplanmış bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-570">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="ebfc3-571">`FullName`ayarlanamaz, yalnızca bir get erişimcisine sahip.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-571">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="ebfc3-572">Veritabanında `FullName` hiçbir sütun oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-572">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="ebfc3-573">Eğitmen varlığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="ebfc3-573">Create the Instructor Entity</span></span>

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="ebfc3-575">Aşağıdaki kodla *modeller/eğitmen. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-575">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="ebfc3-576">Birden çok öznitelik tek bir satırda olabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-576">Multiple attributes can be on one line.</span></span> <span data-ttu-id="ebfc3-577">`HireDate` Öznitelikler aşağıdaki gibi yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-577">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="ebfc3-578">Courseatamalar ve OfficeAssignment gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="ebfc3-578">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="ebfc3-579">`CourseAssignments` Ve`OfficeAssignment` özellikleri gezinti özellikleridir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-579">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="ebfc3-580">Bir eğitmen herhangi bir sayıda kurs öğretebilir, bu `CourseAssignments` nedenle bir koleksiyon olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-580">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="ebfc3-581">Bir gezinti özelliği birden çok varlık tutuyorsa:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-581">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="ebfc3-582">Girişlerin eklenebileceği, silinebileceği ve güncelleştirilebileceği bir liste türü olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-582">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="ebfc3-583">Gezinti özelliği türleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-583">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="ebfc3-584">Belirtilmişse, EF Core varsayılan olarak bir `HashSet<T>` koleksiyon oluşturur. `ICollection<T>`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-584">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="ebfc3-585">`CourseAssignment` Varlık, çoktan çoğa ilişkilerin bölümünde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-585">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="ebfc3-586">Contoso Üniversitesi iş kuralları, bir eğitmenin en fazla bir ofisiniz olabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-586">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="ebfc3-587">`OfficeAssignment` Özelliği tek`OfficeAssignment` bir varlık içerir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-587">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="ebfc3-588">`OfficeAssignment`hiçbir Office atanmamışsa null olur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-588">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="ebfc3-589">OfficeAssignment varlığını oluşturma</span><span class="sxs-lookup"><span data-stu-id="ebfc3-589">Create the OfficeAssignment entity</span></span>

![OfficeAssignment varlığı](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="ebfc3-591">Aşağıdaki kodla *modeller/OfficeAssignment. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-591">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="ebfc3-592">Anahtar özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-592">The Key attribute</span></span>

<span data-ttu-id="ebfc3-593">`[Key]` Öznitelik, özellik adı classnameıd veya ID dışında bir şey olduğunda, bir özelliği birincil anahtar (PK) olarak tanımlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-593">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="ebfc3-594">`Instructor` Ve`OfficeAssignment` varlıkları arasında bire sıfır veya-bir ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-594">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="ebfc3-595">Office ataması, atandığı eğitmenle ilişkili olarak yalnızca vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-595">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="ebfc3-596">PK Ayrıca `Instructor` varlığa ait yabancı anahtardır (FK). `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-596">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="ebfc3-597">EF Core, `InstructorID` `OfficeAssignment` şu nedenle otomatik olarak tanıyamaz:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-597">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="ebfc3-598">`InstructorID`ID veya Classnameıd adlandırma kuralını takip etmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-598">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="ebfc3-599">Bu nedenle, `Key` özniteliği PK olarak tanımlamak `InstructorID` için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-599">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="ebfc3-600">Varsayılan olarak, EF Core, sütun tanımlayıcı bir ilişki için olduğundan, anahtarı veritabanı olmayan bir şekilde değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-600">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="ebfc3-601">Eğitmen gezintisi özelliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-601">The Instructor navigation property</span></span>

<span data-ttu-id="ebfc3-602">Varlık için gezinti özelliği null yapılabilir çünkü: `OfficeAssignment` `Instructor`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-602">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="ebfc3-603">Başvuru türleri (örneğin, sınıflar Nullable).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-603">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="ebfc3-604">Bir eğitmenin Office ataması olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-604">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="ebfc3-605">Varlık null atanamaz `Instructor` bir gezinti özelliğine sahip, çünkü: `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-605">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="ebfc3-606">`InstructorID`null atanamaz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-606">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="ebfc3-607">Bir Office ataması, bir eğitmen olmadan bulunamaz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-607">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="ebfc3-608">Bir `Instructor` varlık ilişkili `OfficeAssignment` bir varlığa sahip olduğunda, her varlığın gezinti özelliğinde diğer bir başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-608">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="ebfc3-609">Öznitelik, `Instructor` gezinti özelliğine uygulanabilir: `[Required]`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-609">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="ebfc3-610">Yukarıdaki kod, ilgili bir eğitmen olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-610">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="ebfc3-611">`InstructorID` Yabancı anahtar (aynı zamanda PK) null değer atanamaz olduğundan, yukarıdaki kod gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-611">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="ebfc3-612">Kurs varlığını değiştirme</span><span class="sxs-lookup"><span data-stu-id="ebfc3-612">Modify the Course Entity</span></span>

![Kurs varlığı](complex-data-model/_static/course-entity.png)

<span data-ttu-id="ebfc3-614">*Modelleri/kursu. cs* aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-614">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="ebfc3-615">Varlığın yabancı anahtar (FK) özelliği `DepartmentID`vardır. `Course`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-615">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="ebfc3-616">`DepartmentID`ilgili `Department` varlığa işaret eder.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-616">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="ebfc3-617">`Course` Varlığın bir`Department` gezinti özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-617">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="ebfc3-618">EF Core, modelin ilgili bir varlık için gezinti özelliği olduğunda bir veri modeli için FK özelliği gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-618">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="ebfc3-619">EF Core, gerektiği yerde otomatik olarak veritabanında FKs 'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-619">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="ebfc3-620">EF Core otomatik olarak oluşturulan FKs 'ler için [gölge Özellikler](/ef/core/modeling/shadow-properties) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-620">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="ebfc3-621">FK 'in veri modelinde olması, güncelleştirmeleri daha basit ve daha verimli hale getirir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-621">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="ebfc3-622">Örneğin, FK özelliğinin `DepartmentID` dahil *olmadığı* bir model düşünün.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-622">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="ebfc3-623">Bir kurs varlığı düzenlemek üzere getirilirken:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-623">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="ebfc3-624">Açık bir şekilde yüklenmediyse varlıknullolur.`Department`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-624">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="ebfc3-625">Kurs varlığını `Department` güncelleştirmek için önce varlığın getirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-625">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="ebfc3-626">FK özelliği `DepartmentID` veri modeline dahil edildiğinde, bir güncelleştirmeden önce `Department` varlığı getirme gerekmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-626">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="ebfc3-627">DatabaseGenerated özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-627">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="ebfc3-628">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` Özniteliği, PK 'nin veritabanı tarafından oluşturulması yerine uygulama tarafından sağlandığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-628">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="ebfc3-629">Varsayılan olarak, EF Core PK değerlerinin DB tarafından oluşturulduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-629">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="ebfc3-630">VERITABANı tarafından oluşturulan PK değerleri genellikle en iyi yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-630">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="ebfc3-631">Varlıklar `Course` için Kullanıcı PK 'yi belirtir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-631">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="ebfc3-632">Örneğin, matematik departmanı için 1000 serisi, Ingilizce departmanı için 2000 serisi gibi bir kurs numarası.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-632">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="ebfc3-633">`DatabaseGenerated` Öznitelik varsayılan değerler oluşturmak için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-633">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="ebfc3-634">Örneğin, VERITABANı bir satırın oluşturulduğu veya güncelleştirildiği tarihi kaydetmek için otomatik olarak bir tarih alanı oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-634">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="ebfc3-635">Daha fazla bilgi için bkz. [üretilen Özellikler](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-635">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ebfc3-636">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="ebfc3-636">Foreign key and navigation properties</span></span>

<span data-ttu-id="ebfc3-637">`Course` Varlıktaki yabancı anahtar (FK) özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-637">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="ebfc3-638">Bir kurs bir departmana atanır, bu nedenle bir `DepartmentID` FK ve bir `Department` gezinti özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-638">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="ebfc3-639">Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir, bu nedenle `Enrollments` gezinti özelliği bir koleksiyondur:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-639">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="ebfc3-640">Kurs, birden fazla eğitmen tarafından tada olabilir, bu nedenle `CourseAssignments` gezinti özelliği bir koleksiyon olur:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-640">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="ebfc3-641">`CourseAssignment`[daha sonra](#many-to-many-relationships)açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-641">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="ebfc3-642">Departman varlığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="ebfc3-642">Create the Department entity</span></span>

![Bölüm varlığı](complex-data-model/_static/department-entity.png)

<span data-ttu-id="ebfc3-644">Aşağıdaki kodla *modeller/departman. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-644">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="ebfc3-645">Column özniteliği</span><span class="sxs-lookup"><span data-stu-id="ebfc3-645">The Column attribute</span></span>

<span data-ttu-id="ebfc3-646">Daha önce `Column` öznitelik, sütun adı eşlemesini değiştirmek için kullanıldı.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-646">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="ebfc3-647">`Department` Varlığın kodunda`Column` , özniteliği SQL veri türü eşlemesini değiştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-647">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="ebfc3-648">`Budget` Sütun, veritabanında SQL Server para türü kullanılarak tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-648">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="ebfc3-649">Sütun eşlemesi genellikle gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-649">Column mapping is generally not required.</span></span> <span data-ttu-id="ebfc3-650">EF Core genellikle özelliğin CLR türüne göre uygun SQL Server veri türünü seçer.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-650">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="ebfc3-651">CLR `decimal` türü SQL Server `decimal` bir türe eşlenir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-651">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="ebfc3-652">`Budget`para birimi için, para veri türü ise para birimi için daha uygundur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-652">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ebfc3-653">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="ebfc3-653">Foreign key and navigation properties</span></span>

<span data-ttu-id="ebfc3-654">FK ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-654">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="ebfc3-655">Bir departman yönetici olabilir veya olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-655">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="ebfc3-656">Yönetici her zaman bir eğitmendir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-656">An administrator is always an instructor.</span></span> <span data-ttu-id="ebfc3-657">Bu nedenle, `Instructor` özelliği varlığa FK olarak dahil edilir. `InstructorID`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-657">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="ebfc3-658">Gezinti özelliği adlandırılır `Administrator` ancak bir `Instructor` varlık barındırır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-658">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="ebfc3-659">Önceki koddaki soru işareti (?) özelliği null yapılabilir olduğunu belirtiyor.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-659">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="ebfc3-660">Bir departmanın birçok kursu olabilir, bu nedenle bir kurs gezintisi özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-660">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="ebfc3-661">Not: Kural gereği EF Core, null yapılamayan ve çok-çok ilişkiler için basamaklı silme imkanı sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-661">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="ebfc3-662">Basamaklı silme, dairesel basamaklı silme kurallarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-662">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="ebfc3-663">Döngüsel basamaklı silme kuralları, bir geçiş eklendiğinde özel duruma neden olur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-663">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="ebfc3-664">Örneğin, `Department.InstructorID` özellik null yapılamayan olarak tanımlanmışsa:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-664">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="ebfc3-665">EF Core, eğitmen silindiğinde departmanı silmek için bir basamakla silme kuralı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-665">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="ebfc3-666">Eğitmen silindiğinde departmanı silmek amaçlanan davranış değildir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-666">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="ebfc3-667">Aşağıdaki [Fluent API](#fluent-api-alternative-to-attributes) Cascade yerine bir kısıtlama kuralı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-667">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="ebfc3-668">Yukarıdaki kod, departman-eğitmen ilişkisindeki basamaklı silmeyi devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-668">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="ebfc3-669">Kayıt varlığını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="ebfc3-669">Update the Enrollment entity</span></span>

<span data-ttu-id="ebfc3-670">Kayıt kaydı, tek bir öğrenci tarafından gerçekleştirilen bir kurs içindir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-670">An enrollment record is for one course taken by one student.</span></span>

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="ebfc3-672">*Modelleri/kaydı. cs* 'yi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-672">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ebfc3-673">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="ebfc3-673">Foreign key and navigation properties</span></span>

<span data-ttu-id="ebfc3-674">FK özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-674">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="ebfc3-675">Kayıt kaydı tek bir kurs için olduğundan, `CourseID` FK özelliği ve bir `Course` gezinti özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-675">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="ebfc3-676">Kayıt kaydı bir öğrenci içindir, bu nedenle bir `StudentID` FK özelliği ve bir `Student` gezinti özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-676">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="ebfc3-677">Çoktan çoğa Ilişkiler</span><span class="sxs-lookup"><span data-stu-id="ebfc3-677">Many-to-Many Relationships</span></span>

<span data-ttu-id="ebfc3-678">`Student` Ve`Course` varlıkları arasında çoktan çoğa bir ilişki vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-678">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="ebfc3-679">Varlık `Enrollment` , veritabanında *Yük içeren* çoktan çoğa bir JOIN tablosu olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-679">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="ebfc3-680">"Yükle", `Enrollment` tablonun birleştirilmiş tablolar için FKS 'ler (Bu durumda, PK ve `Grade`) gibi ek veriler içerdiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-680">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="ebfc3-681">Aşağıdaki çizimde bu ilişkilerin bir varlık diyagramında nasıl göründüğünü gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-681">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="ebfc3-682">(Bu diyagram EF 6. x için [EF güç araçları](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-682">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="ebfc3-683">Diyagram oluşturmak öğreticinin bir parçası değildir.)</span><span class="sxs-lookup"><span data-stu-id="ebfc3-683">Creating the diagram isn't part of the tutorial.)</span></span>

![Öğrenci-çok fazla ilişki](complex-data-model/_static/student-course.png)

<span data-ttu-id="ebfc3-685">Her ilişki ucu ve bir yıldız işareti (\*) 1 diğer sırasında bir-çok ilişkisi belirten bulunur.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-685">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="ebfc3-686">Tablo, sınıf bilgileri içermiyorsa, yalnızca iki FKS (`CourseID` ve `StudentID`) içermesi gerekir. `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-686">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="ebfc3-687">Yük olmadan çoktan çoğa bir JOIN tablosu bazen saf JOIN tablosu (PJT) olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-687">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="ebfc3-688">`Instructor` Ve`Course` varlıklarının saf bir JOIN tablosu kullanılarak çoktan çoğa bir ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-688">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="ebfc3-689">Not: EF 6. x, çoktan çoğa ilişkiler için örtük birleştirmeyi destekler, ancak EF Core değildir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-689">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="ebfc3-690">Daha fazla bilgi için [EF Core 2,0 ' de çoktan çoğa ilişkiler](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-690">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="ebfc3-691">Courseatama varlığı</span><span class="sxs-lookup"><span data-stu-id="ebfc3-691">The CourseAssignment entity</span></span>

![Courseatama varlığı](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="ebfc3-693">Aşağıdaki kodla *modeller/Courseatama. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-693">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="ebfc3-694">Eğitmenden kurslar</span><span class="sxs-lookup"><span data-stu-id="ebfc3-694">Instructor-to-Courses</span></span>

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="ebfc3-696">Eğitmenin kurslardan çok-çok ilişkisi:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-696">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="ebfc3-697">Bir varlık kümesiyle temsil etmelidir bir JOIN tablosu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-697">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="ebfc3-698">, Saf bir JOIN tablosu (yük içermeyen tablo).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-698">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="ebfc3-699">Bir JOIN varlığına `EntityName1EntityName2`ad vermek yaygındır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-699">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="ebfc3-700">Örneğin, bu model `CourseInstructor`kullanılarak eğitmen-kurslar 'a katılması tablosu.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-700">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="ebfc3-701">Ancak, ilişkiyi açıklayan bir ad kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-701">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="ebfc3-702">Veri modelleri basit ve büyümeye başlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-702">Data models start out simple and grow.</span></span> <span data-ttu-id="ebfc3-703">Yük yükü dahil olmak üzere genellikle yük-yük birleştirmeleri (PJTs) gelişmektedir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-703">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="ebfc3-704">Açıklayıcı bir varlık adıyla başlayarak, ekleme tablosu değiştiğinde adın değiştirilmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-704">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="ebfc3-705">İdeal olarak, JOIN varlığının iş etki alanında kendi doğal (muhtemelen tek bir kelime) adına sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-705">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="ebfc3-706">Örneğin, kitaplar ve müşteriler, derecelendirmeler adlı bir JOIN varlığıyla bağlantı kurulabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-706">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="ebfc3-707">Eğitmenin kursa çok-çok ilişkisi `CourseAssignment` için `CourseInstructor`tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-707">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="ebfc3-708">Bileşik anahtar</span><span class="sxs-lookup"><span data-stu-id="ebfc3-708">Composite key</span></span>

<span data-ttu-id="ebfc3-709">FKs null değer atanamaz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-709">FKs are not nullable.</span></span> <span data-ttu-id="ebfc3-710">( `CourseAssignment` `InstructorID` Ve )`CourseID`içindekiiki FKS, tablonunhersatırınıbenzersiz`CourseAssignment` bir şekilde tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-710">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="ebfc3-711">`CourseAssignment`adanmış bir PK gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-711">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="ebfc3-712">`InstructorID` Ve`CourseID` özellikleri bileşik bir PK olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-712">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="ebfc3-713">EF Core bileşik PKs 'leri belirtmenin tek yolu *Fluent API*' dir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-713">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="ebfc3-714">Sonraki bölümde, bileşik PK 'nin nasıl yapılandırılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-714">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="ebfc3-715">Bileşik anahtar şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-715">The composite key ensures:</span></span>

* <span data-ttu-id="ebfc3-716">Tek bir kurs için birden çok satıra izin verilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-716">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="ebfc3-717">Birden çok satıra bir eğitmen için izin verilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-717">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="ebfc3-718">Aynı eğitmen ve kurs için birden çok satıra izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-718">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="ebfc3-719">`Enrollment` JOIN varlığı kendi PK 'yi tanımlar, bu nedenle bu sıralamanın yinelemeleri mümkündür.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-719">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="ebfc3-720">Bu tür yinelemeleri engellemek için:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-720">To prevent such duplicates:</span></span>

* <span data-ttu-id="ebfc3-721">FK alanlara benzersiz bir dizin ekleyin veya</span><span class="sxs-lookup"><span data-stu-id="ebfc3-721">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="ebfc3-722">`Enrollment` İle`CourseAssignment`benzer bir birincil bileşik anahtarla yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-722">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="ebfc3-723">Daha fazla bilgi için bkz. [dizinler](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-723">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="ebfc3-724">DB bağlamını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="ebfc3-724">Update the DB context</span></span>

<span data-ttu-id="ebfc3-725">*Data/SchoolContext. cs*' ye aşağıdaki vurgulanmış kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-725">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="ebfc3-726">Yukarıdaki kod, yeni varlıkları ekler ve `CourseAssignment` varlığın bileşik PK 'yi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-726">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="ebfc3-727">Tutarlı API 'nin özniteliklere alternatif</span><span class="sxs-lookup"><span data-stu-id="ebfc3-727">Fluent API alternative to attributes</span></span>

<span data-ttu-id="ebfc3-728">Önceki koddaki yöntemi EF Core davranışını yapılandırmak için Fluent API kullanır. `OnModelCreating`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-728">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="ebfc3-729">Genellikle tek bir bildirimde bir dizi yöntem çağrısı olan dize olarak kullanıldığından, API "akıcı" olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-729">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="ebfc3-730">[Aşağıdaki kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) Fluent API bir örneğidir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-730">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="ebfc3-731">Bu öğreticide, Fluent API yalnızca özniteliklerle yapılamadığını DB eşlemesi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-731">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="ebfc3-732">Ancak Fluent API, özniteliklerle yapılabilecek biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-732">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="ebfc3-733">Gibi bazı öznitelikler `MinimumLength` Fluent API uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-733">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="ebfc3-734">`MinimumLength`şemayı değiştirmez, yalnızca bir minimum uzunluk doğrulama kuralı uygular.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-734">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="ebfc3-735">Bazı geliştiriciler, varlık sınıflarının "temiz" olmasını sağlamak için Fluent API özel olarak kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-735">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="ebfc3-736">Öznitelikler ve Fluent API karışık olabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-736">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="ebfc3-737">Yalnızca Fluent API (bileşik bir PK belirterek) yapılabilecek bazı konfigürasyonlar vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-737">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="ebfc3-738">Yalnızca özniteliklerle (`MinimumLength`) yapılabilecek bazı konfigürasyonlar vardır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-738">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="ebfc3-739">Fluent API veya özniteliklerini kullanmak için önerilen uygulama:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-739">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="ebfc3-740">Bu iki yaklaşımdan birini seçin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-740">Choose one of these two approaches.</span></span>
* <span data-ttu-id="ebfc3-741">Seçilen yaklaşımı mümkün olduğunca düzenli olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-741">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="ebfc3-742">Bu öğreticide kullanılan özniteliklerin bazıları için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-742">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="ebfc3-743">Yalnızca doğrulama (örneğin, `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-743">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="ebfc3-744">Yalnızca yapılandırma EF Core (örneğin, `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-744">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="ebfc3-745">Doğrulama ve EF Core yapılandırma (örneğin, `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-745">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="ebfc3-746">Öznitelikler ile Fluent API hakkında daha fazla bilgi için bkz. [yapılandırma yöntemleri](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-746">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="ebfc3-747">Ilişkileri gösteren varlık diyagramı</span><span class="sxs-lookup"><span data-stu-id="ebfc3-747">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="ebfc3-748">Aşağıdaki çizimde, tamamlanmış okul modeli için EF Power Tools 'un oluştura diyagramı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-748">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="ebfc3-750">Önceki diyagramda şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-750">The preceding diagram shows:</span></span>

* <span data-ttu-id="ebfc3-751">Birden çok çoktan çoğa ilişki satırı (1 ile \*).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-751">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="ebfc3-752">`Instructor` Ve`OfficeAssignment` varlıkları arasında bire sıfır veya-bir ilişki çizgisi (1 ila 0.. 1).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-752">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="ebfc3-753">`Instructor` Ve`Department` varlıkları arasında sıfır veya-bire çok ilişki çizgisi (0.. 1-\*).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-753">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="ebfc3-754">VERITABANıNı test verileriyle çekirdek olarak</span><span class="sxs-lookup"><span data-stu-id="ebfc3-754">Seed the DB with Test Data</span></span>

<span data-ttu-id="ebfc3-755">*Data/Dbınizer. cs*dosyasındaki kodu güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-755">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="ebfc3-756">Yukarıdaki kod, yeni varlıklar için tohum verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-756">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="ebfc3-757">Bu kodun çoğu yeni varlık nesneleri oluşturur ve örnek verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-757">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="ebfc3-758">Örnek veriler test için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-758">The sample data is used for testing.</span></span> <span data-ttu-id="ebfc3-759">Birden `Enrollments` çok `CourseAssignments` -çok JOIN tablosunun nasıl çalıştırılabilir olduğunu gösteren örnekler için bkz. ve.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-759">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="ebfc3-760">Geçiş Ekle</span><span class="sxs-lookup"><span data-stu-id="ebfc3-760">Add a migration</span></span>

<span data-ttu-id="ebfc3-761">Projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-761">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ebfc3-762">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ebfc3-762">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ebfc3-763">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ebfc3-763">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="ebfc3-764">Yukarıdaki komut, olası veri kaybı hakkında bir uyarı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-764">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="ebfc3-765">`database update` Komut çalıştırıldığında, aşağıdaki hata oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-765">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="ebfc3-766">Geçişi Uygula</span><span class="sxs-lookup"><span data-stu-id="ebfc3-766">Apply the migration</span></span>

<span data-ttu-id="ebfc3-767">Artık var olan bir veritabanınız olduğuna göre, bundan sonraki değişikliklere nasıl uygulanacağını düşünmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-767">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="ebfc3-768">Bu öğreticide iki yaklaşım gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-768">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="ebfc3-769">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="ebfc3-769">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="ebfc3-770">[Geçişi mevcut veritabanına uygulayın](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="ebfc3-770">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="ebfc3-771">Bu yöntem daha karmaşıktır ve zaman alabilir. Bu, gerçek dünyada üretim ortamları için tercih edilen yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-771">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="ebfc3-772">**Not**: Bu, öğreticinin isteğe bağlı bir bölümüdür.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-772">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="ebfc3-773">Bırakma ve yeniden oluşturma adımlarını gerçekleştirebilir ve bu bölümü atlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-773">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="ebfc3-774">Bu bölümdeki adımları izlemek isterseniz, bırakma ve yeniden oluşturma adımlarını yapmayın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-774">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="ebfc3-775">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="ebfc3-775">Drop and re-create the database</span></span>

<span data-ttu-id="ebfc3-776">Güncelleştirilmiş `DbInitializer` kod, yeni varlıklar için tohum verileri ekler.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-776">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="ebfc3-777">Yeni bir VERITABANı oluşturmak için EF Core zorlamak için DB 'yi bırakıp güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-777">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ebfc3-778">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ebfc3-778">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ebfc3-779">**Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-779">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="ebfc3-780">Yardım `Get-Help about_EntityFrameworkCore` bilgileri almak için PMC 'den çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-780">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ebfc3-781">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ebfc3-781">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ebfc3-782">Bir komut penceresi açın ve proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-782">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="ebfc3-783">Proje klasörü *Startup.cs* dosyasını içerir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-783">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="ebfc3-784">Komut penceresine şunu girin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-784">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="ebfc3-785">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-785">Run the app.</span></span> <span data-ttu-id="ebfc3-786">Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-786">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="ebfc3-787">Yeni DB 'yi doldurur. `DbInitializer.Initialize`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-787">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="ebfc3-788">VERITABANıNı SSOX içinde açın:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-788">Open the DB in SSOX:</span></span>

* <span data-ttu-id="ebfc3-789">Daha önce SSOX açıldıysa **Yenile** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-789">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="ebfc3-790">Genişletin **tabloları** düğümü.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-790">Expand the **Tables** node.</span></span> <span data-ttu-id="ebfc3-791">Oluşturulan tablolar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-791">The created tables are displayed.</span></span>

![SSOX içindeki tablolar](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="ebfc3-793">**Courseatama** tablosunu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-793">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="ebfc3-794">**Courseatama** tablosuna sağ tıklayın ve **verileri görüntüle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-794">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="ebfc3-795">**Courseatama** tablosunun veri içerdiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-795">Verify the **CourseAssignment** table contains data.</span></span>

![SSOX 'te Courseatama verileri](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="ebfc3-797">Geçişi mevcut veritabanına Uygula</span><span class="sxs-lookup"><span data-stu-id="ebfc3-797">Apply the migration to the existing database</span></span>

<span data-ttu-id="ebfc3-798">Bu bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-798">This section is optional.</span></span> <span data-ttu-id="ebfc3-799">Bu adımlar yalnızca önceki [bırakma ve veritabanını yeniden oluşturma](#drop) bölümünü atladıysanız çalışır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-799">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="ebfc3-800">Geçişler mevcut verilerle çalıştırıldığında, mevcut verilerin karşılanmadığı FK kısıtlamalar olabilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-800">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="ebfc3-801">Üretim verileriyle, mevcut verilerin geçirilmesi için adımların alınması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-801">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="ebfc3-802">Bu bölüm, FK kısıtlama ihlallerinin düzeltilmesiyle bir örnek sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-802">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="ebfc3-803">Bu kod değişikliklerini yedekleme olmadan yapmayın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-803">Don't make these code changes without a backup.</span></span> <span data-ttu-id="ebfc3-804">Önceki bölümü tamamlayıp veritabanını güncelleştirdiyseniz, bu kod değişikliklerini yapmayın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-804">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="ebfc3-805">*{Timestamp} _ComplexDataModel. cs* dosyası aşağıdaki kodu içerir:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-805">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="ebfc3-806">Yukarıdaki kod, `DepartmentID` `Course` tabloya null yapılamayan bir FK ekler.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-806">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="ebfc3-807">Önceki öğreticideki veritabanı, içindeki `Course`satırları içerir, böylece tablo geçişler tarafından güncelleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-807">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="ebfc3-808">`ComplexDataModel` Geçişin mevcut verilerle çalışmasını sağlamak için:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-808">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="ebfc3-809">Yeni sütuna (`DepartmentID`) varsayılan değer vermek için kodu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-809">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="ebfc3-810">Varsayılan departman olarak davranacak "Temp" adlı sahte bir departman oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-810">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="ebfc3-811">Yabancı anahtar kısıtlamalarını çözme</span><span class="sxs-lookup"><span data-stu-id="ebfc3-811">Fix the foreign key constraints</span></span>

<span data-ttu-id="ebfc3-812">`ComplexDataModel` Classes`Up` metodunu güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-812">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="ebfc3-813">*{Timestamp} _ComplexDataModel. cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-813">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="ebfc3-814">`DepartmentID` Sütunu `Course` tabloya ekleyen kod satırını açıklama olarak yapın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-814">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="ebfc3-815">Aşağıdaki vurgulanmış kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-815">Add the following highlighted code.</span></span> <span data-ttu-id="ebfc3-816">Yeni kod `.CreateTable( name: "Department"` bloğundan sonra geçer:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-816">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="ebfc3-817">Önceki değişikliklerle, varolan `Course` satırlar, `Up` Yöntem çalıştıktan `ComplexDataModel` sonra "geçici" departmanıyla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-817">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="ebfc3-818">Bir üretim uygulaması şöyle olacaktır:</span><span class="sxs-lookup"><span data-stu-id="ebfc3-818">A production app would:</span></span>

* <span data-ttu-id="ebfc3-819">Yeni `Course` `Department` satırlarasatırlarveilgilisatırlareklemekiçinkodveyakomutdosyalarıekleyin.`Department`</span><span class="sxs-lookup"><span data-stu-id="ebfc3-819">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="ebfc3-820">İçin `Course.DepartmentID`"geçici" Departmanı veya varsayılan değeri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-820">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="ebfc3-821">Sonraki öğreticide ilgili veriler ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ebfc3-821">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ebfc3-822">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ebfc3-822">Additional resources</span></span>

* [<span data-ttu-id="ebfc3-823">Bu öğreticinin YouTube sürümü (Bölüm 1)</span><span class="sxs-lookup"><span data-stu-id="ebfc3-823">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="ebfc3-824">Bu öğreticinin YouTube sürümü (Bölüm 2)</span><span class="sxs-lookup"><span data-stu-id="ebfc3-824">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="ebfc3-825">[Önceki](xref:data/ef-rp/migrations)İleri
> [](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="ebfc3-825">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
