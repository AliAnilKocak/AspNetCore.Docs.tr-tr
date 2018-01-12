---
title: "EF çekirdek - veri modeli - 5, 10 ile ASP.NET Core MVC"
author: tdykstra
description: "Bu öğreticide daha fazla varlıkları ve ilişkileri ekleyebilir ve veri modelinin biçimlendirme, doğrulama ve veritabanı eşleme kurallarını belirterek özelleştirebilirsiniz."
keywords: "ASP.NET Core, Entity Framework Çekirdek veri ek açıklamaları"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 0dd63913-a041-48b6-96a4-3aeaedbdf5d0
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: cacb23441e5f5ab06c6be27f3068276f21ff4ed9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
# <a name="creating-a-complex-data-model---ef-core-with-aspnet-core-mvc-tutorial-5-of-10"></a><span data-ttu-id="64452-104">Karmaşık veri model - EF çekirdek ASP.NET Core MVC Öğreticisi (5 / 10) ile oluşturma</span><span class="sxs-lookup"><span data-stu-id="64452-104">Creating a complex data model - EF Core with ASP.NET Core MVC tutorial (5 of 10)</span></span>

<span data-ttu-id="64452-105">Tarafından [zel Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="64452-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="64452-106">Contoso University örnek web uygulaması Entity Framework Çekirdek ve Visual Studio kullanarak ASP.NET Core MVC web uygulamalarının nasıl oluşturulacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="64452-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="64452-107">Eğitmen serisi hakkında daha fazla bilgi için bkz: [serideki ilk öğreticide](intro.md).</span><span class="sxs-lookup"><span data-stu-id="64452-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="64452-108">Önceki eğitimlerine üç varlıklarının oluşan basit veri modeli ile çalışmıştır.</span><span class="sxs-lookup"><span data-stu-id="64452-108">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="64452-109">Bu öğreticide daha fazla varlıkları ve ilişkileri ekleyeceksiniz ve biçimlendirme, doğrulama ve veritabanı eşleme kurallarını belirterek veri modelini özelleştirmek.</span><span class="sxs-lookup"><span data-stu-id="64452-109">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="64452-110">İşlemi tamamladığınızda, sınıflar aşağıdaki çizimde gösterilen tamamlanmış veri modeli oluşturan:</span><span class="sxs-lookup"><span data-stu-id="64452-110">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

## <a name="customize-the-data-model-by-using-attributes"></a><span data-ttu-id="64452-112">Veri modeli öznitelikleri kullanarak özelleştirme</span><span class="sxs-lookup"><span data-stu-id="64452-112">Customize the Data Model by Using Attributes</span></span>

<span data-ttu-id="64452-113">Bu bölümde biçimlendirme, doğrulama ve veritabanı eşleme kurallarını belirtin öznitelikleri kullanarak veri modelini özelleştirmek nasıl görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="64452-113">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="64452-114">Ardından birkaç oluşturacağınız aşağıdaki bölümlerde tam Okul veri modeline ekleyerek sınıfları, önceden oluşturulmuş ve kalan varlık türleri için yeni sınıflar modelde oluşturma öznitelikleri.</span><span class="sxs-lookup"><span data-stu-id="64452-114">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="64452-115">Veri türü özniteliği</span><span class="sxs-lookup"><span data-stu-id="64452-115">The DataType attribute</span></span>

<span data-ttu-id="64452-116">Tüm bu alan için ilgilendiğiniz olmasına rağmen tarih Öğrenci kayıt tarihler için tüm web sayfalarının şu anda süreyi tarihi ile birlikte gösterir.</span><span class="sxs-lookup"><span data-stu-id="64452-116">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="64452-117">Veri ek açıklaması öznitelikleri kullanarak bir Yapabileceğiniz kod görüntüleme biçimi verileri görüntüler her görünümünde düzeltecektir Değiştir.</span><span class="sxs-lookup"><span data-stu-id="64452-117">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="64452-118">Bir öznitelik ekleyeceksiniz, yapmak nasıl bir örnek görmek için `EnrollmentDate` özelliğinde `Student` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="64452-118">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="64452-119">İçinde *Models/Student.cs*, ekleme bir `using` bildirimi `System.ComponentModel.DataAnnotations` ad alanı ekleyin `DataType` ve `DisplayFormat` özniteliklerini `EnrollmentDate` özelliği, aşağıdaki örnekte gösterildiği gibi:</span><span class="sxs-lookup"><span data-stu-id="64452-119">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="64452-120">`DataType` Özniteliği veritabanı geçerli bir tür daha fazla belirli bir veri türünü belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="64452-120">The `DataType` attribute is used to specify a data type that is more specific than the database intrinsic type.</span></span> <span data-ttu-id="64452-121">Bu durumda yalnızca tarihi, tarih ve saat değil izlemek istiyoruz.</span><span class="sxs-lookup"><span data-stu-id="64452-121">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="64452-122">`DataType` Tarih, saat, PhoneNumber, para birimi, EmailAddress ve daha fazla gibi birçok veri türleri için numaralandırma sağlar.</span><span class="sxs-lookup"><span data-stu-id="64452-122">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="64452-123">`DataType` Özniteliği de otomatik olarak türüne özgü özellikleri sağlamak uygulama etkinleştir.</span><span class="sxs-lookup"><span data-stu-id="64452-123">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="64452-124">Örneğin, bir `mailto:` bağlantı için oluşturulabilir `DataType.EmailAddress`, ve bir tarih seçici için sağlanan `DataType.Date` HTML5 destekleyen tarayıcılarda.</span><span class="sxs-lookup"><span data-stu-id="64452-124">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="64452-125">`DataType` Özniteliği yayar HTML 5 `data-` HTML 5 tarayıcılar anlayabilirsiniz (okunur veri tire) öznitelikler.</span><span class="sxs-lookup"><span data-stu-id="64452-125">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="64452-126">`DataType` Öznitelikleri tüm doğrulama sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="64452-126">The `DataType` attributes do not provide any validation.</span></span>

<span data-ttu-id="64452-127">`DataType.Date`Görüntülenen tarih biçimi belirtmiyor.</span><span class="sxs-lookup"><span data-stu-id="64452-127">`DataType.Date` does not specify the format of the date that is displayed.</span></span> <span data-ttu-id="64452-128">Varsayılan olarak, sunucunun CultureInfo üzerinde temel alan varsayılan biçimler göre veri alanı görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="64452-128">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="64452-129">`DisplayFormat` Özniteliği açıkça tarih biçimini belirtmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="64452-129">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="64452-130">`ApplyFormatInEditMode` Ayar değeri düzenlemek için bir metin kutusu görüntülendiğinde biçimlendirmeyi de uygulanması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="64452-130">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="64452-131">(, Bazı alanlar--Örneğin, para birimi değerleri için metin kutusuna para birimi simgesini düzenleme için istemeyebilirsiniz olduğunu istemeyebilirsiniz.)</span><span class="sxs-lookup"><span data-stu-id="64452-131">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="64452-132">Kullanabileceğiniz `DisplayFormat` kendisi, ancak tarafından özniteliktir genellikle kullanmak iyi bir fikir `DataType` de özniteliği.</span><span class="sxs-lookup"><span data-stu-id="64452-132">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="64452-133">`DataType` Özniteliği bir ekranda işlemesini nasıl aksine veri semantiği iletir ve ile elde etmezsiniz aşağıdaki yararları sağlar `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="64452-133">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="64452-134">Tarayıcı HTML5 özellikleri etkinleştirebilirsiniz (örneğin, bir Takvim denetimi, yerel ayar uygun para birimi simgesini, e-posta bağlantıları göstermek, bazı istemci-tarafı giriş doğrulama, vs.).</span><span class="sxs-lookup"><span data-stu-id="64452-134">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="64452-135">Varsayılan olarak, tarayıcı, bölgesel ayarına göre doğru biçimi kullanarak bir veri oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="64452-135">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="64452-136">Daha fazla bilgi için bkz: [ \<Giriş > etiketi yardımcı belgelerine](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="64452-136">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="64452-137">Uygulamayı çalıştırın, Öğrenciler dizin sayfasına gidin ve zamanlar için kayıt tarihleri artık görüntülenir dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="64452-137">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="64452-138">Aynı Öğrenci modelini kullanan herhangi bir görünüm için geçerli olacaktır.</span><span class="sxs-lookup"><span data-stu-id="64452-138">The same will be true for any view that uses the Student model.</span></span>

![Tarihleri gösteren Öğrenciler dizin sayfası](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="64452-140">StringLength özniteliği</span><span class="sxs-lookup"><span data-stu-id="64452-140">The StringLength attribute</span></span>

<span data-ttu-id="64452-141">Veri doğrulama kuralları ve öznitelikleri kullanarak bir doğrulama hata iletisi de belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="64452-141">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="64452-142">`StringLength` Özniteliği veritabanında uzunluk üst sınırını ayarlar ve istemci tarafı ve sunucu tarafı sağlar ASP.NET MVC için doğrulama.</span><span class="sxs-lookup"><span data-stu-id="64452-142">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET MVC.</span></span> <span data-ttu-id="64452-143">En az dize uzunluğu Bu öznitelikte belirtebilirsiniz, ancak en düşük değer veritabanı şemasını temel bir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="64452-143">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="64452-144">Kullanıcılar için bir ad 50'den fazla karakter girmeyin sağlamak istediğinizi varsayın.</span><span class="sxs-lookup"><span data-stu-id="64452-144">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="64452-145">Bu sınırlama eklemek için Ekle `StringLength` özniteliklerini `LastName` ve `FirstMidName` aşağıdaki örnekte gösterildiği gibi özellikleri:</span><span class="sxs-lookup"><span data-stu-id="64452-145">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="64452-146">`StringLength` Özniteliği olmaz önlemek bir kullanıcı için bir ad boşluk girerek.</span><span class="sxs-lookup"><span data-stu-id="64452-146">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="64452-147">Kullanabileceğiniz `RegularExpression` özniteliği girişine kısıtlamalar getirmek için.</span><span class="sxs-lookup"><span data-stu-id="64452-147">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="64452-148">Örneğin, aşağıdaki kod, büyük harf olması için ilk karakter ve alfabetik olarak geriye kalan karakterler gerektirir:</span><span class="sxs-lookup"><span data-stu-id="64452-148">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z''-'\s]*$")]
```

<span data-ttu-id="64452-149">`MaxLength` Özniteliği benzer işlevleri sağlayan `StringLength` özniteliği ancak istemci tarafı sağlamaz doğrulama.</span><span class="sxs-lookup"><span data-stu-id="64452-149">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="64452-150">Veritabanı modeli artık veritabanı şeması değişikliği gerektirdiği şekilde değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="64452-150">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="64452-151">Geçişler, veritabanına uygulama kullanıcı Arabirimi kullanarak eklemiş olabileceğiniz herhangi bir veri kaybetmeden şemasını güncelleştirmek için kullanacağız.</span><span class="sxs-lookup"><span data-stu-id="64452-151">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="64452-152">Yaptığınız değişiklikleri kaydedin ve projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="64452-152">Save your changes and build the project.</span></span> <span data-ttu-id="64452-153">Sonra proje klasöründe komut penceresi açın ve aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="64452-153">Then open the command window in the project folder and enter the following commands:</span></span>

```console
dotnet ef migrations add MaxLengthOnNames
```

```console
dotnet ef database update
```

<span data-ttu-id="64452-154">`migrations add` Komutu uyarır veri kaybına neden olabilir, çünkü değişiklik iki sütun için daha kısa uzunluk sağlar.</span><span class="sxs-lookup"><span data-stu-id="64452-154">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="64452-155">Geçişler adlı bir dosya oluşturur  *\<zaman damgası > _MaxLengthOnNames.cs*.</span><span class="sxs-lookup"><span data-stu-id="64452-155">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="64452-156">Kod bu dosyayı içeren `Up` geçerli veri modeli eşleşecek şekilde veritabanını güncelleştirmek yöntemi.</span><span class="sxs-lookup"><span data-stu-id="64452-156">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="64452-157">`database update` Komutu çalıştırılmadan bu kodu.</span><span class="sxs-lookup"><span data-stu-id="64452-157">The `database update` command ran that code.</span></span>

<span data-ttu-id="64452-158">Geçişler dosya adına önek zaman damgası geçişler düzenlemek için Entity Framework tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="64452-158">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="64452-159">Update-database komutunu çalıştırmadan önce birden çok geçiş oluşturabilir ve ardından tüm geçişler, oluşturuldukları sırada uygulanır.</span><span class="sxs-lookup"><span data-stu-id="64452-159">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="64452-160">Uygulama, belirleyin **Öğrenciler** sekmesini tıklatın, **Yeni Oluştur**ve en fazla 50 karakter uzunluğunda ya da bir ad girin.</span><span class="sxs-lookup"><span data-stu-id="64452-160">Run the app, select the **Students** tab, click **Create New**, and enter either name longer than 50 characters.</span></span> <span data-ttu-id="64452-161">Tıkladığınızda **oluşturma**, istemci tarafı doğrulama hata iletisi gösterir.</span><span class="sxs-lookup"><span data-stu-id="64452-161">When you click **Create**, client side validation shows an error message.</span></span>

![Dize uzunluğu hataları gösteren sayfa Öğrenciler dizin](complex-data-model/_static/string-length-errors.png)

### <a name="the-column-attribute"></a><span data-ttu-id="64452-163">Sütun özniteliği</span><span class="sxs-lookup"><span data-stu-id="64452-163">The Column attribute</span></span>

<span data-ttu-id="64452-164">Sınıfları ve özellikleri veritabanına nasıl eşlendiğini denetlemek için öznitelikler de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="64452-164">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="64452-165">Kullanılan adı olduğunu varsayın `FirstMidName` -ad alanı ikinci adı içeriyor olabilir çünkü alan.</span><span class="sxs-lookup"><span data-stu-id="64452-165">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="64452-166">Ancak veritabanı sütunun adlandırılması istediğiniz `FirstName`, veritabanı geçici sorguları yazma kullanıcılar bu adı alışmanızı.</span><span class="sxs-lookup"><span data-stu-id="64452-166">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="64452-167">Bu eşleme yapmak için kullanabileceğiniz `Column` özniteliği.</span><span class="sxs-lookup"><span data-stu-id="64452-167">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="64452-168">`Column` Özniteliği belirtir, bu, veritabanı oluşturulduktan sonra sütunu `Student` eşlendiği tablo `FirstMidName` özelliği adlı `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="64452-168">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="64452-169">Diğer bir deyişle, ne zaman kodunuzu başvurduğu `Student.FirstMidName`, veri öğesinden gelir veya içinde güncelleştirilmesi `FirstName` sütunu `Student` tablo.</span><span class="sxs-lookup"><span data-stu-id="64452-169">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="64452-170">Sütun adları belirtmezseniz, bunlar özellik adı olarak aynı adı verilir.</span><span class="sxs-lookup"><span data-stu-id="64452-170">If you don't specify column names, they are given the same name as the property name.</span></span>

<span data-ttu-id="64452-171">İçinde *Student.cs* dosya, ekleme bir `using` deyimi için `System.ComponentModel.DataAnnotations.Schema` ve sütun adı özniteliğe eklemek `FirstMidName` vurgulanan aşağıdaki kodda gösterildiği gibi özelliği:</span><span class="sxs-lookup"><span data-stu-id="64452-171">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="64452-172">Eklenmesi `Column` özniteliği değiştirir destekleyen model `SchoolContext`, veritabanı eşleşmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="64452-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="64452-173">Yaptığınız değişiklikleri kaydedin ve projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="64452-173">Save your changes and build the project.</span></span> <span data-ttu-id="64452-174">Sonra proje klasöründe komut penceresi açın ve başka bir geçiş oluşturmak için aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="64452-174">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```console
dotnet ef migrations add ColumnFirstName
```

```console
dotnet ef database update
```

<span data-ttu-id="64452-175">İçinde **SQL Server Nesne Gezgini**, Öğrenci Tablo Tasarımcısı'nı çift tıklatarak açın **Öğrenci** tablo.</span><span class="sxs-lookup"><span data-stu-id="64452-175">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![SSOX Öğrenciler tabloda geçişler sonra](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="64452-177">İlk iki geçiş uygulamadan önce adı sütun türü nvarchar(MAX) bulunuyordu.</span><span class="sxs-lookup"><span data-stu-id="64452-177">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="64452-178">Artık nvarchar(50) olmaları ve sütun adı FirstMidName FirstName değişti.</span><span class="sxs-lookup"><span data-stu-id="64452-178">They are now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="64452-179">Tüm sınıflar aşağıdaki bölümlerde oluşturma bitirmeden derlemek çalışırsanız, derleyici hataları alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="64452-179">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="final-changes-to-the-student-entity"></a><span data-ttu-id="64452-180">Öğrenci varlık son değişiklikler</span><span class="sxs-lookup"><span data-stu-id="64452-180">Final changes to the Student entity</span></span>

![Öğrenci varlık](complex-data-model/_static/student-entity.png)

<span data-ttu-id="64452-182">İçinde *Models/Student.cs*, eklediğiniz kod daha önce aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="64452-182">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="64452-183">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="64452-183">The changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="64452-184">Gerekli özniteliği</span><span class="sxs-lookup"><span data-stu-id="64452-184">The Required attribute</span></span>

<span data-ttu-id="64452-185">`Required` Öznitelik adı özellikleri gerekli alanlar yapar.</span><span class="sxs-lookup"><span data-stu-id="64452-185">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="64452-186">`Required` Öznitelik değer türleri gibi null türleri için gerekli değildir (DateTime, int, çift, float, vb..).</span><span class="sxs-lookup"><span data-stu-id="64452-186">The `Required` attribute is not needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="64452-187">Null olamaz türleri gerekli alanlar olarak otomatik olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="64452-187">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="64452-188">Kullanarak kaldırabilirsiniz `Required` özniteliği ve en az uzunluk parametresi için WITH replace `StringLength` özniteliği:</span><span class="sxs-lookup"><span data-stu-id="64452-188">You could remove the `Required` attribute and replace it with a minimum length parameter for the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="64452-189">Görüntüleme özniteliği</span><span class="sxs-lookup"><span data-stu-id="64452-189">The Display attribute</span></span>

<span data-ttu-id="64452-190">`Display` Özniteliği, metin kutuları için resim yazısı "Ad", "Son adı", "Tam adı" ve "Kayıt tarihi" özellik adı yerine (Sözcük bölünmesi boşluk olan) her örnek olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="64452-190">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="64452-191">Hesaplanan FullName özelliği</span><span class="sxs-lookup"><span data-stu-id="64452-191">The FullName calculated property</span></span>

<span data-ttu-id="64452-192">`FullName`diğer iki özellik birleştirerek oluşturulan bir değer döndürür hesaplanan bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="64452-192">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="64452-193">Bu nedenle yalnızca get erişimcisi ve Hayır sahip `FullName` sütun veritabanında oluşturulmayacak.</span><span class="sxs-lookup"><span data-stu-id="64452-193">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="64452-194">Eğitmen varlık oluştur</span><span class="sxs-lookup"><span data-stu-id="64452-194">Create the Instructor Entity</span></span>

![Eğitmen varlık](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="64452-196">Oluşturma *Models/Instructor.cs*, şablonu kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="64452-196">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="64452-197">Çeşitli özellikler Öğrenci ve Eğitmen varlıkları aynı olmasına dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="64452-197">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="64452-198">İçinde [uygulama devralma](inheritance.md) bu serideki sonraki öğretici, artıklık ortadan kaldırmak için bu kodu yeniden düzenlemeniz.</span><span class="sxs-lookup"><span data-stu-id="64452-198">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="64452-199">Ayrıca yazabilirsiniz şekilde tek bir çizgi birden çok öznitelik koyabilirsiniz `HireDate` gibi öznitelikleri:</span><span class="sxs-lookup"><span data-stu-id="64452-199">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="64452-200">CourseAssignments ve OfficeAssignment Gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="64452-200">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="64452-201">`CourseAssignments` Ve `OfficeAssignment` özelliklerdir Gezinti özellikleri.</span><span class="sxs-lookup"><span data-stu-id="64452-201">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="64452-202">Bir eğitmen kurslar herhangi bir sayıda öğretmek, bu nedenle `CourseAssignments` koleksiyonu olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="64452-202">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="64452-203">Bir gezinti özelliği birden çok varlık tutarsanız, türü, girişleri eklenmiş, silinmiş, güncelleştirilen ve bir liste olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="64452-203">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="64452-204">Belirleyebileceğiniz `ICollection<T>` veya gibi bir tür `List<T>` veya `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="64452-204">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="64452-205">Belirtirseniz `ICollection<T>`, EF oluşturur bir `HashSet<T>` varsayılan koleksiyon.</span><span class="sxs-lookup"><span data-stu-id="64452-205">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="64452-206">Bunlar neden neden `CourseAssignment` varlıklar açıklanmıştır aşağıda çok-çok ilişkileri hakkında bölümünde.</span><span class="sxs-lookup"><span data-stu-id="64452-206">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="64452-207">Contoso University iş kuralları durum bir eğitmen en çok bir office yalnızca olabilir böylece `OfficeAssignment` özelliği (hangi hiçbir office atanmışsa boş olabilir) tek bir OfficeAssignment varlık tutar.</span><span class="sxs-lookup"><span data-stu-id="64452-207">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="64452-208">OfficeAssignment varlık oluştur</span><span class="sxs-lookup"><span data-stu-id="64452-208">Create the OfficeAssignment entity</span></span>

![OfficeAssignment varlık](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="64452-210">Oluşturma *Models/OfficeAssignment.cs* aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="64452-210">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="64452-211">Key özniteliği</span><span class="sxs-lookup"><span data-stu-id="64452-211">The Key attribute</span></span>

<span data-ttu-id="64452-212">Eğitmen ve OfficeAssignment varlıklar arasındaki bir-sıfır-veya-bir ilişkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="64452-212">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="64452-213">Office atama yalnızca atandığı Eğitmen bağlantılı olarak var ve bu nedenle birincil anahtarı da kendi Eğitmen varlığa yabancı anahtar.</span><span class="sxs-lookup"><span data-stu-id="64452-213">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="64452-214">Ancak adını kimliği veya classnameID adlandırma kuralını uyguladığınızdan değil çünkü Entity Framework otomatik olarak InstructorID bu varlığın birincil anahtarı olarak tanıyamıyor.</span><span class="sxs-lookup"><span data-stu-id="64452-214">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="64452-215">Bu nedenle, `Key` özniteliği anahtar olarak tanımlamak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="64452-215">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="64452-216">Aynı zamanda `Key` varlık birincil anahtarı yok ancak bir şey classnameID veya kimliği dışında name özelliği istiyorsanız özniteliği</span><span class="sxs-lookup"><span data-stu-id="64452-216">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="64452-217">Sütunu için bir tanımlayıcı ilişkisi olduğundan varsayılan olmayan veritabanı-üretilmiş EF anahtar değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="64452-217">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="64452-218">Eğitmen gezinti özelliği</span><span class="sxs-lookup"><span data-stu-id="64452-218">The Instructor navigation property</span></span>

<span data-ttu-id="64452-219">Eğitmen varlık null atanabilir sahip `OfficeAssignment` gezinti özelliği (bir eğitmen office atama olmayabilir nedeniyle), ve OfficeAssignment varlık atanamayan bir sahip `Instructor` gezinti özelliği (office atama yapılamıyor çünkü bir eğitmen--mevcut `InstructorID` NULL olmayan olabilir).</span><span class="sxs-lookup"><span data-stu-id="64452-219">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="64452-220">Eğitmen varlık ilgili OfficeAssignment varlık olduğunda, her bir varlık, gezinti özelliğinin başka bir başvuru olacaktır.</span><span class="sxs-lookup"><span data-stu-id="64452-220">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="64452-221">Put bir `[Required]` ilgili Eğitmen olmalıdır, ancak, çünkü bunu yapmanız gerekmez belirtmek için Eğitmen gezinti özelliği öznitelikte `InstructorID` (aynı zamanda olan bu tablo anahtarı) yabancı anahtar null.</span><span class="sxs-lookup"><span data-stu-id="64452-221">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="64452-222">İndirmelere varlık değiştirme</span><span class="sxs-lookup"><span data-stu-id="64452-222">Modify the Course Entity</span></span>

![İndirmelere varlık](complex-data-model/_static/course-entity.png)

<span data-ttu-id="64452-224">İçinde *Models/Course.cs*, eklediğiniz kod daha önce aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="64452-224">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="64452-225">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="64452-225">The changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="64452-226">İndirmelere varlık yabancı anahtar özelliğine `DepartmentID` ilgili departmanı varlık ve bu işaret ettiği sahip bir `Department` gezinti özelliği.</span><span class="sxs-lookup"><span data-stu-id="64452-226">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="64452-227">Entity Framework ilgili varlık gezinme özelliğinin olduğunda yabancı anahtar özelliği, veri modeline Ekle gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="64452-227">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="64452-228">EF otomatik olarak gerekli olan her yerde veritabanındaki yabancı anahtarlar oluşturur ve oluşturur [gölge özellikleri](https://docs.microsoft.com/ef/core/modeling/shadow-properties) bunlar için.</span><span class="sxs-lookup"><span data-stu-id="64452-228">EF automatically creates foreign keys in the database wherever they are needed and creates [shadow properties](https://docs.microsoft.com/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="64452-229">Ancak veri modelinde yabancı anahtar olan güncelleştirmeler daha basit ve daha verimli hale getirebilir.</span><span class="sxs-lookup"><span data-stu-id="64452-229">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="64452-230">Düzenlemek için bir indirmelere varlık getirme, örneğin, departman varlık null olduğunda, yükleme böylece indirmelere varlık güncelleştirdiğinizde varsa ilk bölüm varlık getirilemedi.</span><span class="sxs-lookup"><span data-stu-id="64452-230">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="64452-231">Zaman yabancı anahtar özelliği `DepartmentID` bulunan veri modelinde güncelleştirmeden önce departmanı varlık fetch gerekmez.</span><span class="sxs-lookup"><span data-stu-id="64452-231">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="64452-232">DatabaseGenerated özniteliği</span><span class="sxs-lookup"><span data-stu-id="64452-232">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="64452-233">`DatabaseGenerated` İle öznitelik `None` parametresini `CourseID` özelliği, birincil anahtar değerlerini kullanıcı tarafından sağlanan yerine veritabanı tarafından oluşturulan belirtir.</span><span class="sxs-lookup"><span data-stu-id="64452-233">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="64452-234">Varsayılan olarak, birincil anahtar değerlerinin veritabanı tarafından oluşturulan Entity Framework varsayar.</span><span class="sxs-lookup"><span data-stu-id="64452-234">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="64452-235">Çoğu senaryoda istediğiniz olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="64452-235">That's what you want in most scenarios.</span></span> <span data-ttu-id="64452-236">Ancak, indirmelere varlıklar için 1000 serisi gibi bir kullanıcı tarafından belirtilen indirmelere numarası bir bölüm, başka bir bölüm için bir 2000 serisi için kullanabilir ve benzeri.</span><span class="sxs-lookup"><span data-stu-id="64452-236">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="64452-237">`DatabaseGenerated` Özniteliği de söz konusu olduğunda veritabanı sütunlarının tarih kaydetmek için kullanılan bir satır oluşturulurken veya güncelleştirilirken gibi varsayılan değerlerini oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="64452-237">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="64452-238">Daha fazla bilgi için bkz: [oluşturulan Özellikler](https://docs.microsoft.com/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="64452-238">For more information, see [Generated Properties](https://docs.microsoft.com/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="64452-239">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="64452-239">Foreign key and navigation properties</span></span>

<span data-ttu-id="64452-240">Yabancı anahtar özellikleri ve gezinti özellikleri indirmelere varlıktaki aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="64452-240">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="64452-241">Bu yüzden bir indirmelere bir bölüme atanan bir `DepartmentID` yabancı anahtar ve `Department` yukarıda belirtilen nedenlerden için gezinme özelliği.</span><span class="sxs-lookup"><span data-stu-id="64452-241">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="64452-242">Bir indirmelere içinde kayıtlı Öğrenciler herhangi bir sayıda olabilir böylece `Enrollments` gezinti özelliği olduğundan koleksiyonu:</span><span class="sxs-lookup"><span data-stu-id="64452-242">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="64452-243">Bir indirmelere birden çok Eğitmen tarafından öğrettin böylece `CourseAssignments` bir koleksiyon gezinti özelliği olduğundan (tür `CourseAssignment` anlatılmıştır [daha sonra](#many-to-many-relationships)):</span><span class="sxs-lookup"><span data-stu-id="64452-243">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-the-department-entity"></a><span data-ttu-id="64452-244">Departman varlık oluştur</span><span class="sxs-lookup"><span data-stu-id="64452-244">Create the Department entity</span></span>

![Departman varlık](complex-data-model/_static/department-entity.png)


<span data-ttu-id="64452-246">Oluşturma *Models/Department.cs* aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="64452-246">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="64452-247">Sütun özniteliği</span><span class="sxs-lookup"><span data-stu-id="64452-247">The Column attribute</span></span>

<span data-ttu-id="64452-248">Daha önce kullandığınız `Column` sütun adı eşlemesi değiştirmek için öznitelik.</span><span class="sxs-lookup"><span data-stu-id="64452-248">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="64452-249">Departman varlık kodunda `Column` özniteliği kullanılan sütun veritabanında SQL Server para türü kullanılarak tanımlanacağı için SQL veri türü eşlemesi değiştirmek için:</span><span class="sxs-lookup"><span data-stu-id="64452-249">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="64452-250">Sütun eşlemesi Entity Framework özelliği için tanımladığınız CLR türüne göre uygun SQL Server veri türü seçtiği için genellikle gerekli değil.</span><span class="sxs-lookup"><span data-stu-id="64452-250">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="64452-251">CLR `decimal` yazın eşlemeleri SQL Server'a `decimal` türü.</span><span class="sxs-lookup"><span data-stu-id="64452-251">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="64452-252">Ancak bu durumda sütun para birimi miktarları bulunduran ve para veri türü için daha uygun olan biliyor.</span><span class="sxs-lookup"><span data-stu-id="64452-252">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="64452-253">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="64452-253">Foreign key and navigation properties</span></span>

<span data-ttu-id="64452-254">Yabancı anahtar ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="64452-254">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="64452-255">Bir bölüm olabilir veya bir yönetici olmayabilir ve yönetici her zaman bir eğitmen.</span><span class="sxs-lookup"><span data-stu-id="64452-255">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="64452-256">Bu nedenle `InstructorID` Eğitmen varlığa yabancı anahtar olarak özellik eklenmiştir ve sonra bir soru işareti eklenir `int` özelliği null olarak işaretlemek için ataması yazın.</span><span class="sxs-lookup"><span data-stu-id="64452-256">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="64452-257">Gezinti özelliği adlı `Administrator` ancak Eğitmen varlık tutar:</span><span class="sxs-lookup"><span data-stu-id="64452-257">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="64452-258">Bu yüzden kurslar gezinti özelliği bir bölüm birçok kurslar sahip olabilir:</span><span class="sxs-lookup"><span data-stu-id="64452-258">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="64452-259">Kurala göre art arda silme çok-çok ilişkileri ve null yabancı anahtarlar için Entity Framework sağlar.</span><span class="sxs-lookup"><span data-stu-id="64452-259">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="64452-260">Bu, bir geçiş eklemeye çalıştığınızda, bir özel durum neden olacak döngüsel cascade delete kuralları'nda neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="64452-260">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="64452-261">Örneğin, Department.InstructorID özelliği null olarak tanımlamadığınız, EF durum olmasını istediğiniz değil departmanı sildiğinizde Eğitmen silmek için bir cascade delete kuralı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="64452-261">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the instructor when you delete the department, which is not what you want to have happen.</span></span> <span data-ttu-id="64452-262">İş kuralları gerekirse `InstructorID` özelliğini alamayan, art arda silme ilişkiyi devre dışı bırakmak için aşağıdaki fluent API deyimi kullanması gerekir:</span><span class="sxs-lookup"><span data-stu-id="64452-262">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-the-enrollment-entity"></a><span data-ttu-id="64452-263">Kayıt varlık değiştirme</span><span class="sxs-lookup"><span data-stu-id="64452-263">Modify the Enrollment entity</span></span>

![Kayıt varlık](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="64452-265">İçinde *Models/Enrollment.cs*, eklediğiniz kod daha önce aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="64452-265">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="64452-266">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="64452-266">Foreign key and navigation properties</span></span>

<span data-ttu-id="64452-267">Yabancı anahtar özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="64452-267">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="64452-268">Bu yüzden bir kaydolma kaydı için tek bir yol, kullanmamaktır bir `CourseID` yabancı anahtar özellik ve `Course` gezinti özelliği:</span><span class="sxs-lookup"><span data-stu-id="64452-268">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="64452-269">Bu yüzden bir kaydı tek bir öğrenci için olan bir `StudentID` yabancı anahtar özellik ve `Student` gezinti özelliği:</span><span class="sxs-lookup"><span data-stu-id="64452-269">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="64452-270">Çok-çok ilişkileri</span><span class="sxs-lookup"><span data-stu-id="64452-270">Many-to-Many Relationships</span></span>

<span data-ttu-id="64452-271">Öğrenci ve indirmelere varlıklar arasında çok-çok ilişkisi vardır ve kayıt varlık işlevleri çoktan bire çok birleşme tablo olarak *yükü ile* veritabanındaki.</span><span class="sxs-lookup"><span data-stu-id="64452-271">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="64452-272">"Yükünü" anlamına gelir kayıt tablo birleştirilmiş tablolarında (Bu durumda, bir birincil anahtar ve bir sınıf özelliği) için yabancı anahtarları yanı sıra ek veriler içerir.</span><span class="sxs-lookup"><span data-stu-id="64452-272">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="64452-273">Aşağıdaki çizimde, bu tür bir varlık şemada nasıl göründüğünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="64452-273">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="64452-274">(Bu diyagramda EF için Entity Framework güç araçları kullanılarak oluşturulan 6.x; diyagram oluşturma öğreticinin bir parçası değil, yalnızca kullanılıyor burada çizim olarak.)</span><span class="sxs-lookup"><span data-stu-id="64452-274">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Öğrenci indirmelere çoklu ilişki çok](complex-data-model/_static/student-course.png)

<span data-ttu-id="64452-276">Her ilişki ucu ve bir yıldız işareti (\\*) 1 diğer sırasında bir-çok ilişkisi belirten bulunur.</span><span class="sxs-lookup"><span data-stu-id="64452-276">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="64452-277">Kayıt tablo düzeyde bilgi eklemediyseniz, yalnızca iki yabancı anahtarları CourseID ve StudentID içerecek şekilde gerekir.</span><span class="sxs-lookup"><span data-stu-id="64452-277">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="64452-278">Bu durumda, bir çok-çok birleştirme tablo yükü olmadan (veya bir saf birleştirme tablo) veritabanında olacaktır.</span><span class="sxs-lookup"><span data-stu-id="64452-278">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="64452-279">Eğitmen ve indirmelere varlıkları bu tür bir çok-çok ilişkisi vardır ve sonraki adımınız yükü olmadan birleştirme tablosu olarak çalışması için bir varlık sınıfı oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="64452-279">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="64452-280">(Çok-çok ilişkileri ancak EF çekirdek için örtük birleştirme tabloları desteklemez EF 6.x destekler.</span><span class="sxs-lookup"><span data-stu-id="64452-280">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core does not.</span></span> <span data-ttu-id="64452-281">Daha fazla bilgi için bkz: [EF çekirdek GitHub deposuna tartışmada](https://github.com/aspnet/EntityFramework/issues/1368).)</span><span class="sxs-lookup"><span data-stu-id="64452-281">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span> 

## <a name="the-courseassignment-entity"></a><span data-ttu-id="64452-282">CourseAssignment varlık</span><span class="sxs-lookup"><span data-stu-id="64452-282">The CourseAssignment entity</span></span>

![CourseAssignment varlık](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="64452-284">Oluşturma *Models/CourseAssignment.cs* aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="64452-284">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="64452-285">Varlık adları birleştirme</span><span class="sxs-lookup"><span data-stu-id="64452-285">Join entity names</span></span>

<span data-ttu-id="64452-286">Eğitmen kurslar çok-çok ilişkisi için veritabanında bir birleşim tablosundan gereklidir ve bir varlık kümesi tarafından temsil edilebilir gerekir.</span><span class="sxs-lookup"><span data-stu-id="64452-286">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="64452-287">Bir birleştirme varlık adı yaygındır `EntityName1EntityName2`, bu durumda olacak `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="64452-287">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="64452-288">Ancak, ilişkiyi tanımlayan bir ad seçmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="64452-288">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="64452-289">Veri modelleri basit başlatın ve, sık yükü daha sonra alma Hayır yükü birleştirmeler ile büyütün.</span><span class="sxs-lookup"><span data-stu-id="64452-289">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="64452-290">Açıklayıcı varlık adı ile başlatırsanız, adı daha sonra değiştirmek zorunda kalmazsınız.</span><span class="sxs-lookup"><span data-stu-id="64452-290">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="64452-291">Birleşim varlık ideal olarak, iş etki alanında kendi doğal (büyük olasılıkla tek sözcüklük) adına sahip.</span><span class="sxs-lookup"><span data-stu-id="64452-291">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="64452-292">Örneğin, Books ve müşteriler derecelendirmeleri ilişkilendirilemiyor.</span><span class="sxs-lookup"><span data-stu-id="64452-292">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="64452-293">Bu ilişki için `CourseAssignment` değerinden daha iyi bir seçimdir `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="64452-293">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="64452-294">Bileşik anahtar</span><span class="sxs-lookup"><span data-stu-id="64452-294">Composite key</span></span>

<span data-ttu-id="64452-295">Yabancı anahtarlar benzersiz olarak boş değer atanabilir ve birlikte olmadığından tablonun her satırında belirlemek, ayrı bir birincil anahtar için gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="64452-295">Since the foreign keys are not nullable and together uniquely identify each row of the table, there is no need for a separate primary key.</span></span> <span data-ttu-id="64452-296">*InstructorID* ve *CourseID* özellikleri birleşik birincil anahtar olarak çalışması.</span><span class="sxs-lookup"><span data-stu-id="64452-296">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="64452-297">EF için birleşik birincil anahtarlar tanımlamak için tek yolu kullanmaktır *fluent API* (bunu öznitelikleri kullanarak yapılamaz).</span><span class="sxs-lookup"><span data-stu-id="64452-297">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="64452-298">Sonraki bölümde birleşik birincil anahtar yapılandırmak nasıl görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="64452-298">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="64452-299">Bileşik anahtarın bir indirmelere ve bir eğitmen için birden çok satır için birden çok satır sahip olsa da, aynı eğitmen ve indirmelere için birden fazla satır bulunamaz sağlar.</span><span class="sxs-lookup"><span data-stu-id="64452-299">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="64452-300">`Enrollment` Birleştirme varlık çoğaltmaları bu tür olası şekilde kendi birincil anahtar tanımlar.</span><span class="sxs-lookup"><span data-stu-id="64452-300">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="64452-301">Bu tür çoğaltmaları önlemek için benzersiz bir dizin yabancı anahtar alanları eklediğinizde veya yapılandırma `Enrollment` benzer birincil bileşik anahtar ile `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="64452-301">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="64452-302">Daha fazla bilgi için bkz: [dizinleri](https://docs.microsoft.com/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="64452-302">For more information, see [Indexes](https://docs.microsoft.com/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="64452-303">Veritabanı bağlamı güncelleştir</span><span class="sxs-lookup"><span data-stu-id="64452-303">Update the database context</span></span>

<span data-ttu-id="64452-304">Aşağıdaki vurgulanmış kodu ekleyin *Data/SchoolContext.cs* dosyası:</span><span class="sxs-lookup"><span data-stu-id="64452-304">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="64452-305">Bu kod, yeni varlıklar ekler ve CourseAssignment varlığın birleşik birincil anahtar yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="64452-305">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="64452-306">Öznitelikleri Fluent API alternatifi</span><span class="sxs-lookup"><span data-stu-id="64452-306">Fluent API alternative to attributes</span></span>

<span data-ttu-id="64452-307">Kodda `OnModelCreating` yöntemi `DbContext` sınıfını kullanan *fluent API* EF davranışı yapılandırmak için.</span><span class="sxs-lookup"><span data-stu-id="64452-307">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="64452-308">Genellikle bu örnekte olduğu gibi tek bir deyimde içine bir dizi yöntem çağrısı birlikte stringing tarafından kullanılmakta olduğu için API "fluent" olarak adlandırılır [EF Core belgeleri](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration):</span><span class="sxs-lookup"><span data-stu-id="64452-308">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="64452-309">Bu öğreticide, yalnızca özniteliklerle yapamayacağı veritabanı eşleme fluent API kullanıyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="64452-309">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="64452-310">Ancak, çoğu biçimlendirmeyi, doğrulama ve öznitelikleri kullanarak yapabilirsiniz eşleme kurallarını belirtmek için fluent API kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="64452-310">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="64452-311">Gibi bazı öznitelikler `MinimumLength` fluent API'si ile uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="64452-311">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="64452-312">Daha önce belirtildiği gibi `MinimumLength` şema değiştirmez yalnızca bir istemci ve sunucu tarafı doğrulama kuralı uygular.</span><span class="sxs-lookup"><span data-stu-id="64452-312">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="64452-313">Bazı geliştiriciler özel olarak bunlar kendi sınıflar "temiz." tutabilirsiniz böylece fluent API kullanmayı tercih eder</span><span class="sxs-lookup"><span data-stu-id="64452-313">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="64452-314">İstediğiniz ve yalnızca fluent API kullanılarak yapılabilir birkaç özelleştirmeler varsa öznitelikleri ve fluent API karıştırabilirsiniz, ancak genel olarak önerilen uygulama bu iki yaklaşımlardan birini seçin ve bu tutarlı bir şekilde olabildiğince kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="64452-314">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="64452-315">Her ikisi de kullanırsanız, bir çakışma olduğunda Fluent API öznitelikleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="64452-315">If you do use both, note that wherever there is a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="64452-316">Öznitelikler fluent API karşılaştırması hakkında daha fazla bilgi için bkz: [yapılandırma yöntemlerini](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration).</span><span class="sxs-lookup"><span data-stu-id="64452-316">For more information about attributes vs. fluent API, see [Methods of configuration](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="64452-317">Varlık diyagram gösteren ilişkileri</span><span class="sxs-lookup"><span data-stu-id="64452-317">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="64452-318">Aşağıdaki çizimde Entity Framework güç araçları için tamamlanan Okul modeli oluşturma diyagramda gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="64452-318">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="64452-320">Bir-çok ilişkisi satırları yanı sıra (1 \*), eğitmen ve OfficeAssignment varlıkları ve sıfır-veya-bir-çok ilişkisi satırı arasında bir-sıfır-veya-bir ilişkisi satır (1 için 0.. 1 çokluğa) burada görebilirsiniz (0.. 1 çokluğa için *) arasında Eğitmen ve departman varlıklar.</span><span class="sxs-lookup"><span data-stu-id="64452-320">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to *) between the Instructor and Department entities.</span></span>

## <a name="seed-the-database-with-test-data"></a><span data-ttu-id="64452-321">Çekirdek Test verileri veritabanıyla</span><span class="sxs-lookup"><span data-stu-id="64452-321">Seed the Database with Test Data</span></span>

<span data-ttu-id="64452-322">Kodla *Data/DbInitializer.cs* dosya ile oluşturduğunuz yeni varlıklar için çekirdek veri sağlamak için aşağıdaki kodu.</span><span class="sxs-lookup"><span data-stu-id="64452-322">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="64452-323">İlk öğreticide gördüğünüz gibi bu kod çoğunu sadece yeni varlık nesnesi oluşturur ve örnek veri özelliklerini test etmek için gerektiği gibi yükler.</span><span class="sxs-lookup"><span data-stu-id="64452-323">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="64452-324">Çok-çok ilişkileri işlenme dikkat edin: varlıklarda oluşturarak ilişkileri kod oluşturur `Enrollments` ve `CourseAssignment` katılma varlık kümeleri.</span><span class="sxs-lookup"><span data-stu-id="64452-324">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="64452-325">Bir geçiş ekleme</span><span class="sxs-lookup"><span data-stu-id="64452-325">Add a migration</span></span>

<span data-ttu-id="64452-326">Yaptığınız değişiklikleri kaydedin ve projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="64452-326">Save your changes and build the project.</span></span> <span data-ttu-id="64452-327">Sonra proje klasöründe komut penceresi açın ve girin `migrations add` komutu (yapma update-database komutunu henüz):</span><span class="sxs-lookup"><span data-stu-id="64452-327">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```console
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="64452-328">Olası veri kaybı hakkında bir uyarı alırsınız.</span><span class="sxs-lookup"><span data-stu-id="64452-328">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="64452-329">Çalıştırmak çalıştıysanız `database update` bu noktada komutu (yapma, henüz), aşağıdaki hatayı alıyorsunuz:</span><span class="sxs-lookup"><span data-stu-id="64452-329">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="64452-330">ALTER TABLE deyimi "FK_dbo. yabancı anahtar kısıtlaması ile çakıştı. Course_dbo. Department_DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="64452-330">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="64452-331">Veritabanı "ContosoUniversity" Tablo "dbo. çakışma oluştu Departman", sütun 'DepartmentID'.</span><span class="sxs-lookup"><span data-stu-id="64452-331">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="64452-332">Bazen geçişler var olan verilerle yürüttüğünüzde saplama veri yabancı anahtar kısıtlamaları karşılamak için veritabanına eklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="64452-332">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="64452-333">Oluşturulan kod `Up` yöntemi null DepartmentID yabancı anahtar indirmelere tabloya ekler.</span><span class="sxs-lookup"><span data-stu-id="64452-333">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="64452-334">Zaten satır yoksa indirmelere tabloda kod çalıştığında `AddColumn` SQL Server ne null olamaz sütununda yerleştirilecek değer bilmiyor olduğundan işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="64452-334">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="64452-335">Bu öğretici için yeni bir veritabanı üzerinde geçiş çalıştıracaksınız ancak bir üretim uygulamasında aşağıdaki yönergeleri nasıl örneği göstermek için var olan verileri işlemek geçiş yapmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="64452-335">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="64452-336">Varsayılan departman olarak davranacak şekilde "Temp" adlı yeni bir sütun varsayılan bir değer vermek için kodu değiştirmek zorunda olan verilerle çalışmak ve bir saplama bölüm oluşturma bu geçiş yapmak için.</span><span class="sxs-lookup"><span data-stu-id="64452-336">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="64452-337">Sonuç olarak, indirmelere satırları varolan tüm sonra "Temp" departmanına ilişkili olacağı `Up` yöntemi çalışır.</span><span class="sxs-lookup"><span data-stu-id="64452-337">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="64452-338">Açık *{timestamp}_ComplexDataModel.cs* dosya.</span><span class="sxs-lookup"><span data-stu-id="64452-338">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span> 

* <span data-ttu-id="64452-339">DepartmentID sütun indirmelere tabloya ekler kod satırı çıkışı açıklama.</span><span class="sxs-lookup"><span data-stu-id="64452-339">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="64452-340">Aşağıdaki vurgulanmış kodu sonra departman tablosu yaratır kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="64452-340">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="64452-341">Bir üretim uygulamasında, kod veya komut departmanı satır eklemek ve yeni bölüm satırlar indirmelere satırları ilişkili yazarsınız.</span><span class="sxs-lookup"><span data-stu-id="64452-341">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="64452-342">Ardından artık "Temp" departman veya Course.DepartmentID sütunda varsayılan değer gerekir.</span><span class="sxs-lookup"><span data-stu-id="64452-342">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="64452-343">Yaptığınız değişiklikleri kaydedin ve projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="64452-343">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string-and-update-the-database"></a><span data-ttu-id="64452-344">Bağlantı dizesini değiştirin ve veritabanı güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="64452-344">Change the connection string and update the database</span></span>

<span data-ttu-id="64452-345">Bir artık yeni kod sahip `DbInitializer` çekirdek verileri yeni varlıklar için boş bir veritabanı ekler sınıfı.</span><span class="sxs-lookup"><span data-stu-id="64452-345">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="64452-346">Yeni bir boş veritabanı oluşturmak EF yapmak için bağlantı dizesinde veritabanında adını değiştirmek *appsettings.json* ContosoUniversity3 veya kullanmakta olduğunuz bilgisayarda kullandığınız parolalardan başka bir adı.</span><span class="sxs-lookup"><span data-stu-id="64452-346">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="64452-347">Değişiklik Kaydet *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="64452-347">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="64452-348">Veritabanı adının değiştirilmesi alternatif olarak, veritabanını silebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="64452-348">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="64452-349">Kullanım **SQL Server Nesne Gezgini** (SSOX) veya `database drop` CLI komutu:</span><span class="sxs-lookup"><span data-stu-id="64452-349">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
> ```console
> dotnet ef database drop
> ```

<span data-ttu-id="64452-350">Veritabanı adı değiştirilmiş veya silinmiş veritabanı sonra çalıştırmak `database update` geçişler yürütülecek komut penceresinde komutu.</span><span class="sxs-lookup"><span data-stu-id="64452-350">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="64452-351">Uygulama neden çalıştırma `DbInitializer.Initialize` çalıştırın ve yeni veritabanı doldurmak için yöntem.</span><span class="sxs-lookup"><span data-stu-id="64452-351">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="64452-352">Daha önce yaptığınız gibi veritabanı içinde SSOX açın ve genişletin **tabloları** tabloların tümü oluşturulmuş olduğunu görmek için düğüm.</span><span class="sxs-lookup"><span data-stu-id="64452-352">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="64452-353">(Önceki süreyi açmak SSOX hala varsa, tıklatın **yenileme** düğmesini.)</span><span class="sxs-lookup"><span data-stu-id="64452-353">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![SSOX tablolarında](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="64452-355">Veritabanı çekirdeğini oluşturur Başlatıcı kodu tetiklemek için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="64452-355">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="64452-356">Sağ **CourseAssignment** tablo ve seçin **görünüm verilerini** veri içinde olduğunu doğrulamak için.</span><span class="sxs-lookup"><span data-stu-id="64452-356">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![SSOX CourseAssignment verileri](complex-data-model/_static/ssox-ci-data.png)

## <a name="summary"></a><span data-ttu-id="64452-358">Özet</span><span class="sxs-lookup"><span data-stu-id="64452-358">Summary</span></span>

<span data-ttu-id="64452-359">Artık daha karmaşık veri modeli ve karşılık gelen veritabanı vardır.</span><span class="sxs-lookup"><span data-stu-id="64452-359">You now have a more complex data model and corresponding database.</span></span> <span data-ttu-id="64452-360">Aşağıdaki öğreticide, ilgili veri erişimi hakkında daha fazla bilgi edineceksiniz.</span><span class="sxs-lookup"><span data-stu-id="64452-360">In the following tutorial, you'll learn more about how to access related data.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="64452-361">[Önceki](migrations.md)
[sonraki](read-related-data.md)</span><span class="sxs-lookup"><span data-stu-id="64452-361">[Previous](migrations.md)
[Next](read-related-data.md)</span></span>  
