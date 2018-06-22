---
title: Veri - 10 7 ASP.NET Core MVC EF çekirdek - güncelleştirme ile ilgili
author: rick-anderson
description: Bu öğreticide yabancı anahtar alanları ve gezinti özellikleri güncelleştirerek ilgili verileri güncelleştirin.
ms.author: tdykstra
ms.date: 03/15/2017
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 53f1607d96a9a1db98f4e80e9582c124cedf6c8d
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36272656"
---
# <a name="aspnet-core-mvc-with-ef-core---update-related-data---7-of-10"></a><span data-ttu-id="ada84-103">Veri - 10 7 ASP.NET Core MVC EF çekirdek - güncelleştirme ile ilgili</span><span class="sxs-lookup"><span data-stu-id="ada84-103">ASP.NET Core MVC with EF Core - Update Related Data - 7 of 10</span></span>

<span data-ttu-id="ada84-104">Tarafından [zel Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ada84-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ada84-105">Contoso University örnek web uygulaması Entity Framework Çekirdek ve Visual Studio kullanarak ASP.NET Core MVC web uygulamalarının nasıl oluşturulacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="ada84-105">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="ada84-106">Eğitmen serisi hakkında daha fazla bilgi için bkz: [serideki ilk öğreticide](intro.md).</span><span class="sxs-lookup"><span data-stu-id="ada84-106">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="ada84-107">Önceki öğreticide ilgili verileri görüntülenir; Bu öğreticide yabancı anahtar alanları ve gezinti özellikleri güncelleştirerek ilgili verileri güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="ada84-107">In the previous tutorial you displayed related data; in this tutorial you'll update related data by updating foreign key fields and navigation properties.</span></span>

<span data-ttu-id="ada84-108">Aşağıdaki çizimler ile çalışma sayfaları bazıları göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="ada84-108">The following illustrations show some of the pages that you'll work with.</span></span>

![İndirmelere Düzenle sayfası](update-related-data/_static/course-edit.png)

![Eğitmen Düzenle sayfası](update-related-data/_static/instructor-edit-courses.png)

## <a name="customize-the-create-and-edit-pages-for-courses"></a><span data-ttu-id="ada84-111">Oluşturma ve düzenleme sayfaları kursları özelleştirme</span><span class="sxs-lookup"><span data-stu-id="ada84-111">Customize the Create and Edit Pages for Courses</span></span>

<span data-ttu-id="ada84-112">Yeni bir indirmelere varlık oluşturulduğunda, var olan bir bölüm için bir ilişki olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ada84-112">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="ada84-113">Bu kolaylaştırmak için kurulmuş kodu denetleyicisi yöntemleri ve departman seçmek için aşağı açılan listede yer oluşturma ve düzenleme görünümleri içerir.</span><span class="sxs-lookup"><span data-stu-id="ada84-113">To facilitate this, the scaffolded code includes controller methods and Create and Edit views that include a drop-down list for selecting the department.</span></span> <span data-ttu-id="ada84-114">Aşağı açılan liste kümeleri `Course.DepartmentID` yabancı anahtar özellik ve tüm Entity Framework gereken yüklemek için olan `Department` uygun departmanı varlığı ile gezinti özelliği.</span><span class="sxs-lookup"><span data-stu-id="ada84-114">The drop-down list sets the `Course.DepartmentID` foreign key property, and that's all the Entity Framework needs in order to load the `Department` navigation property with the appropriate Department entity.</span></span> <span data-ttu-id="ada84-115">Kurulmuş kodu kullanın ancak biraz hata işleme ekleme ve açılan listeyi sıralayın değiştirmeniz.</span><span class="sxs-lookup"><span data-stu-id="ada84-115">You'll use the scaffolded code, but change it slightly to add error handling and sort the drop-down list.</span></span>

<span data-ttu-id="ada84-116">İçinde *CoursesController.cs*, dört oluşturma ve düzenleme yöntemlerini silin ve bunları aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="ada84-116">In *CoursesController.cs*, delete the four Create and Edit methods and replace them with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

<span data-ttu-id="ada84-117">Sonra `Edit` HttpPost yöntemi, aşağı açılan liste departmanı bilgilerini yükler yeni bir yöntem oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ada84-117">After the `Edit` HttpPost method, create a new method that loads department info for the drop-down list.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

<span data-ttu-id="ada84-118">`PopulateDepartmentsDropDownList` Yöntemi adına göre sıralanmış tüm bölümleri listesini alır, oluşturur bir `SelectList` açılır listesi, koleksiyon ve koleksiyon görünümüne geçirir `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="ada84-118">The `PopulateDepartmentsDropDownList` method gets a list of all departments sorted by name, creates a `SelectList` collection for a drop-down list, and passes the collection to the view in `ViewBag`.</span></span> <span data-ttu-id="ada84-119">Yöntemi isteğe bağlı kabul `selectedDepartment` aşağı açılan liste işlendiğinde seçilir öğeyi seçmek arama kodu verir parametresi.</span><span class="sxs-lookup"><span data-stu-id="ada84-119">The method accepts the optional `selectedDepartment` parameter that allows the calling code to specify the item that will be selected when the drop-down list is rendered.</span></span> <span data-ttu-id="ada84-120">Adı "DepartmentID" geçirir görünümü `<select>` etiket Yardımcısı ve yardımcı sonra bilir Bakılacak `ViewBag` için nesne bir `SelectList` "DepartmentID" adlı.</span><span class="sxs-lookup"><span data-stu-id="ada84-120">The view will pass the name "DepartmentID" to the `<select>` tag helper, and the helper then knows to look in the `ViewBag` object for a `SelectList` named "DepartmentID".</span></span>

<span data-ttu-id="ada84-121">HttpGet `Create` yöntem çağrılarını `PopulateDepartmentsDropDownList` için yeni bir indirmelere departman henüz kurulan değil çünkü seçili öğe ayarlama olmadan yöntemi:</span><span class="sxs-lookup"><span data-stu-id="ada84-121">The HttpGet `Create` method calls the `PopulateDepartmentsDropDownList` method without setting the selected item, because for a new course the department isn't established yet:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

<span data-ttu-id="ada84-122">HttpGet `Edit` yöntemi, seçili öğe düzenlenmekte indirmelere zaten atanmış bölüm Kimliğini temel ayarlar:</span><span class="sxs-lookup"><span data-stu-id="ada84-122">The HttpGet `Edit` method sets the selected item, based on the ID of the department that's already assigned to the course being edited:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

<span data-ttu-id="ada84-123">Her ikisi için de HttpPost yöntemleri `Create` ve `Edit` da bunlar sayfa bir hatanın ardından yeniden görüntüleyin seçili öğe ayarlar kodunu içerir.</span><span class="sxs-lookup"><span data-stu-id="ada84-123">The HttpPost methods for both `Create` and `Edit` also include code that sets the selected item when they redisplay the page after an error.</span></span> <span data-ttu-id="ada84-124">Bu hata iletisini görüntülemek için sayfanın görüntülendiğinde için hangi departmanı seçilmedi seçili kalmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="ada84-124">This ensures that when the page is redisplayed to show the error message, whatever department was selected stays selected.</span></span>

### <a name="add-asnotracking-to-details-and-delete-methods"></a><span data-ttu-id="ada84-125">Ekleyin. Ayrıntılar için AsNoTracking ve silme yöntemleri</span><span class="sxs-lookup"><span data-stu-id="ada84-125">Add .AsNoTracking to Details and Delete methods</span></span>

<span data-ttu-id="ada84-126">İndirmelere ayrıntıları ve Delete sayfaları performansını iyileştirmek için ekleme `AsNoTracking` çağrıları `Details` ve HttpGet `Delete` yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="ada84-126">To optimize performance of the Course Details and Delete pages, add `AsNoTracking` calls in the `Details` and HttpGet `Delete` methods.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a><span data-ttu-id="ada84-127">İndirmelere görünümleri değiştirme</span><span class="sxs-lookup"><span data-stu-id="ada84-127">Modify the Course views</span></span>

<span data-ttu-id="ada84-128">İçinde *Views/Courses/Create.cshtml*, "Select departmanı" seçeneğine ekleyin **departmanı** aşağı açılan listesinde, gelen başlığını değiştirme **DepartmentID** için  **Departman**ve bir doğrulama ileti ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ada84-128">In *Views/Courses/Create.cshtml*, add a "Select Department" option to the **Department** drop-down list, change the caption from **DepartmentID** to **Department**, and add a validation message.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

<span data-ttu-id="ada84-129">İçinde *Views/Courses/Edit.cshtml*, yaptığınız yalnızca bölüm alan için aynı değişiklik *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ada84-129">In *Views/Courses/Edit.cshtml*, make the same change for the Department field that you just did in *Create.cshtml*.</span></span>

<span data-ttu-id="ada84-130">De *Views/Courses/Edit.cshtml*, önce bir indirmelere numarası alanı ekleme **başlık** alan.</span><span class="sxs-lookup"><span data-stu-id="ada84-130">Also in *Views/Courses/Edit.cshtml*, add a course number field before the **Title** field.</span></span> <span data-ttu-id="ada84-131">İndirmelere birincil anahtar olduğundan, bu görüntülenir, ancak değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="ada84-131">Because the course number is the primary key, it's displayed, but it can't be changed.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

<span data-ttu-id="ada84-132">Gizli bir alan zaten var. (`<input type="hidden">`) düzenleme görünümü indirmelere numaralı.</span><span class="sxs-lookup"><span data-stu-id="ada84-132">There's already a hidden field (`<input type="hidden">`) for the course number in the Edit view.</span></span> <span data-ttu-id="ada84-133">Ekleme bir `<label>` kullanıcı tıklattığında gönderilen veriler dahil edilecek indirmelere numarası neden değil, etiket Yardımcısı gizli alan gereksinimini ortadan değil **kaydetmek** üzerinde **Düzenle** sayfası.</span><span class="sxs-lookup"><span data-stu-id="ada84-133">Adding a `<label>` tag helper doesn't eliminate the need for the hidden field because it doesn't cause the course number to be included in the posted data when the user clicks **Save** on the **Edit** page.</span></span>

<span data-ttu-id="ada84-134">İçinde *Views/Courses/Delete.cshtml*, indirmelere sayı alanı üstüne ekleyin ve departman adına bölüm Kimliğini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ada84-134">In *Views/Courses/Delete.cshtml*, add a course number field at the top and change department ID to department name.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

<span data-ttu-id="ada84-135">İçinde *Views/Courses/Details.cshtml*, yalnızca yaptığınız için aynı değişiklik *Delete.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ada84-135">In *Views/Courses/Details.cshtml*, make the same change that you just did for *Delete.cshtml*.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="ada84-136">İndirmelere sayfalarını test</span><span class="sxs-lookup"><span data-stu-id="ada84-136">Test the Course pages</span></span>

<span data-ttu-id="ada84-137">Uygulama, belirleyin **kurslar** sekmesini tıklatın, **Yeni Oluştur**ve verileri için yeni bir indirmelere girin:</span><span class="sxs-lookup"><span data-stu-id="ada84-137">Run the app, select the **Courses** tab, click **Create New**, and enter data for a new course:</span></span>

![İndirmelere Oluştur sayfası](update-related-data/_static/course-create.png)

<span data-ttu-id="ada84-139">**Oluştur**'u tıklatın.</span><span class="sxs-lookup"><span data-stu-id="ada84-139">Click **Create**.</span></span> <span data-ttu-id="ada84-140">Listeye eklenen yeni indirmelere kurslar dizin sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ada84-140">The Courses Index page is displayed with the new course added to the list.</span></span> <span data-ttu-id="ada84-141">Bölüm adı dizin sayfası listesinde ilişki doğru şekilde kurulduğundan gösteren Gezinti özelliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="ada84-141">The department name in the Index page list comes from the navigation property, showing that the relationship was established correctly.</span></span>

<span data-ttu-id="ada84-142">Tıklatın **Düzenle** indirmelere kurslar dizin sayfasındaki üzerinde.</span><span class="sxs-lookup"><span data-stu-id="ada84-142">Click **Edit** on a course in the Courses Index page.</span></span>

![İndirmelere Düzenle sayfası](update-related-data/_static/course-edit.png)

<span data-ttu-id="ada84-144">Sayfadaki verileri değiştirip'ı **kaydetmek**.</span><span class="sxs-lookup"><span data-stu-id="ada84-144">Change data on the page and click **Save**.</span></span> <span data-ttu-id="ada84-145">Güncelleştirilmiş indirmelere verilerle kurslar dizin sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ada84-145">The Courses Index page is displayed with the updated course data.</span></span>

## <a name="add-an-edit-page-for-instructors"></a><span data-ttu-id="ada84-146">Eğitmen için bir düzen sayfası Ekle</span><span class="sxs-lookup"><span data-stu-id="ada84-146">Add an Edit Page for Instructors</span></span>

<span data-ttu-id="ada84-147">Eğitmen kaydı düzenlediğinizde, eğitmen office atama güncelleştirmek kullanabilmek ister.</span><span class="sxs-lookup"><span data-stu-id="ada84-147">When you edit an instructor record, you want to be able to update the instructor's office assignment.</span></span> <span data-ttu-id="ada84-148">Eğitmen entity aşağıdaki durumlarda işlemek kodunuzu olduğu anlamına gelir OfficeAssignment varlık ile bir-sıfır-veya-bir ilişkisi vardır:</span><span class="sxs-lookup"><span data-stu-id="ada84-148">The Instructor entity has a one-to-zero-or-one relationship with the OfficeAssignment entity, which means your code has to handle the following situations:</span></span>

* <span data-ttu-id="ada84-149">İlk olarak bir değere sahip ve kullanıcı office atama temizler, OfficeAssignment varlığı silin.</span><span class="sxs-lookup"><span data-stu-id="ada84-149">If the user clears the office assignment and it originally had a value, delete the OfficeAssignment entity.</span></span>

* <span data-ttu-id="ada84-150">Kullanıcı bir office atama değeri girer ve başlangıçta boş varsa, yeni bir OfficeAssignment varlık oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ada84-150">If the user enters an office assignment value and it originally was empty, create a new OfficeAssignment entity.</span></span>

* <span data-ttu-id="ada84-151">Kullanıcı bir office atama değeri değiştirirse, var olan bir OfficeAssignment varlığı değeri değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ada84-151">If the user changes the value of an office assignment, change the value in an existing OfficeAssignment entity.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="ada84-152">Eğitmen denetleyicisini güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ada84-152">Update the Instructors controller</span></span>

<span data-ttu-id="ada84-153">İçinde *InstructorsController.cs*, HttpGet kodda değişiklik `Edit` onun Eğitmen varlığın yükler şekilde yöntemi `OfficeAssignment` gezinti özelliği ve çağrıları `AsNoTracking`:</span><span class="sxs-lookup"><span data-stu-id="ada84-153">In *InstructorsController.cs*, change the code in the HttpGet `Edit` method so that it loads the Instructor entity's `OfficeAssignment` navigation property and calls `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]

<span data-ttu-id="ada84-154">HttpPost Değiştir `Edit` office atama güncelleştirmeleri işlemek için aşağıdaki kod ile yöntemi:</span><span class="sxs-lookup"><span data-stu-id="ada84-154">Replace the HttpPost `Edit` method with the following code to handle office assignment updates:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

<span data-ttu-id="ada84-155">Kod şunları yapar:</span><span class="sxs-lookup"><span data-stu-id="ada84-155">The code does the following:</span></span>

-  <span data-ttu-id="ada84-156">Yöntem adı değiştirir `EditPost` imza artık HttpGet aynı olduğundan `Edit` yöntemi ( `ActionName` özniteliği belirtir `/Edit/` URL hala kullanılan).</span><span class="sxs-lookup"><span data-stu-id="ada84-156">Changes the method name to `EditPost` because the signature is now the same as the HttpGet `Edit` method (the `ActionName` attribute specifies that the `/Edit/` URL is still used).</span></span>

-  <span data-ttu-id="ada84-157">Veritabanı kullanımından geçerli Eğitmen varlık için yükleme istekli alır `OfficeAssignment` gezinti özelliği.</span><span class="sxs-lookup"><span data-stu-id="ada84-157">Gets the current Instructor entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span> <span data-ttu-id="ada84-158">Bu HttpGet yaptıklarınızın aynıdır `Edit` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="ada84-158">This is the same as what you did in the HttpGet `Edit` method.</span></span>

-  <span data-ttu-id="ada84-159">Model bağlayıcı değerlerle alınan Eğitmen varlığı güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="ada84-159">Updates the retrieved Instructor entity with values from the model binder.</span></span> <span data-ttu-id="ada84-160">`TryUpdateModel` Aşırı sağlar beyaz liste ile dahil etmek istediğiniz özellikleri.</span><span class="sxs-lookup"><span data-stu-id="ada84-160">The `TryUpdateModel` overload enables you to whitelist the properties you want to include.</span></span> <span data-ttu-id="ada84-161">Bu açıklandığı gibi atlayarak nakil engeller [ikinci Öğreticisi](crud.md).</span><span class="sxs-lookup"><span data-stu-id="ada84-161">This prevents over-posting, as explained in the [second tutorial](crud.md).</span></span>

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```
    
-   <span data-ttu-id="ada84-162">Ofis konumu boş ise, böylece OfficeAssignment tablodaki ilgili satırda silinecek null olarak Instructor.OfficeAssignment özelliğini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ada84-162">If the office location is blank, sets the Instructor.OfficeAssignment property to null so that the related row in the OfficeAssignment table will be deleted.</span></span>

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

- <span data-ttu-id="ada84-163">Değişiklikleri veritabanına kaydeder.</span><span class="sxs-lookup"><span data-stu-id="ada84-163">Saves the changes to the database.</span></span>

### <a name="update-the-instructor-edit-view"></a><span data-ttu-id="ada84-164">Güncelleştirme görünümü Eğitmen Düzenle</span><span class="sxs-lookup"><span data-stu-id="ada84-164">Update the Instructor Edit view</span></span>

<span data-ttu-id="ada84-165">İçinde *Views/Instructors/Edit.cshtml*, ofis konumunda sona önce düzenlemek için yeni bir alan eklemek **kaydetmek** düğmesi:</span><span class="sxs-lookup"><span data-stu-id="ada84-165">In *Views/Instructors/Edit.cshtml*, add a new field for editing the office location, at the end before the **Save** button:</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

<span data-ttu-id="ada84-166">Uygulama, belirleyin **Eğitmen** sekmesini ve ardından **Düzenle** bir eğitmen üzerinde.</span><span class="sxs-lookup"><span data-stu-id="ada84-166">Run the app, select the **Instructors** tab, and then click **Edit** on an instructor.</span></span> <span data-ttu-id="ada84-167">Değişiklik **ofis konumu** tıklatıp **kaydetmek**.</span><span class="sxs-lookup"><span data-stu-id="ada84-167">Change the **Office Location** and click **Save**.</span></span>

![Eğitmen Düzenle sayfası](update-related-data/_static/instructor-edit-office.png)

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="ada84-169">Eğitmen Düzenle sayfasına indirmelere atamaları ekleme</span><span class="sxs-lookup"><span data-stu-id="ada84-169">Add Course assignments to the Instructor Edit page</span></span>

<span data-ttu-id="ada84-170">Eğitmen kurslar herhangi bir sayıda öğretir.</span><span class="sxs-lookup"><span data-stu-id="ada84-170">Instructors may teach any number of courses.</span></span> <span data-ttu-id="ada84-171">Şimdi aşağıdaki ekran görüntüsünde gösterildiği gibi onay kutuları, bir grup kullanarak indirmelere atamalarını değiştirme yeteneğini ekleyerek sayfasını Eğitmen Düzenle geliştirmek:</span><span class="sxs-lookup"><span data-stu-id="ada84-171">Now you'll enhance the Instructor Edit page by adding the ability to change course assignments using a group of check boxes, as shown in the following screen shot:</span></span>

![Kurslar Eğitmen Düzenle sayfası](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="ada84-173">Çok-indirmelere ve Eğitmen varlıklar arasındaki ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="ada84-173">The relationship between the Course and Instructor entities is many-to-many.</span></span> <span data-ttu-id="ada84-174">İlişkileri kaldırın ve eklemek için Ekle ve varlıkları CourseAssignments birleştirme varlık kümesinden ve kaldırın.</span><span class="sxs-lookup"><span data-stu-id="ada84-174">To add and remove relationships, you add and remove entities to and from the CourseAssignments join entity set.</span></span>

<span data-ttu-id="ada84-175">Hangi kurslar değiştirmenize olanak sağlayan bir eğitmen UI'dir atanan onay kutularını grubudur.</span><span class="sxs-lookup"><span data-stu-id="ada84-175">The UI that enables you to change which courses an instructor is assigned to is a group of check boxes.</span></span> <span data-ttu-id="ada84-176">Her indirmelere veritabanındaki bir onay kutusu görüntülenir ve Eğitmen şu anda atanmış olanları seçilir.</span><span class="sxs-lookup"><span data-stu-id="ada84-176">A check box for every course in the database is displayed, and the ones that the instructor is currently assigned to are selected.</span></span> <span data-ttu-id="ada84-177">Kullanıcı seçin veya indirmelere atamalarını değiştirmek için onay kutularını temizleyin.</span><span class="sxs-lookup"><span data-stu-id="ada84-177">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="ada84-178">Kurslar sayısı çok büyük, büyük olasılıkla veri görünümü sunan, farklı bir yöntem kullanmak isteyebilirsiniz, ancak ilişki oluşturma veya silme için bir birleşim varlık düzenleme için aynı yöntem kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="ada84-178">If the number of courses were much greater, you would probably want to use a different method of presenting the data in the view, but you'd use the same method of manipulating a join entity to create or delete relationships.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="ada84-179">Eğitmen denetleyicisini güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ada84-179">Update the Instructors controller</span></span>

<span data-ttu-id="ada84-180">Onay kutuları listesi için veri görünümüne sağlamak için bir görünüm model sınıfı kullanacaksınız.</span><span class="sxs-lookup"><span data-stu-id="ada84-180">To provide data to the view for the list of check boxes, you'll use a view model class.</span></span>

<span data-ttu-id="ada84-181">Oluşturma *AssignedCourseData.cs* içinde *SchoolViewModels* klasörü ve var olan kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="ada84-181">Create *AssignedCourseData.cs* in the *SchoolViewModels* folder and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="ada84-182">İçinde *InstructorsController.cs*, HttpGet Değiştir `Edit` aşağıdaki kod ile yöntemi.</span><span class="sxs-lookup"><span data-stu-id="ada84-182">In *InstructorsController.cs*, replace the HttpGet `Edit` method with the following code.</span></span> <span data-ttu-id="ada84-183">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="ada84-183">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

<span data-ttu-id="ada84-184">İstekli yükleme için kod ekler `Courses` gezinti özelliği ve yeni çağırır `PopulateAssignedCourseData` onay kutusunu dizi kullanma hakkında bilgi sağlamak için yöntemi `AssignedCourseData` görüntülemek model sınıfı.</span><span class="sxs-lookup"><span data-stu-id="ada84-184">The code adds eager loading for the `Courses` navigation property and calls the new `PopulateAssignedCourseData` method to provide information for the check box array using the `AssignedCourseData` view model class.</span></span>

<span data-ttu-id="ada84-185">Kodda `PopulateAssignedCourseData` yöntemi görünüm model sınıfı kullanarak kurslar yüklenmesi için tüm indirmelere varlıklar okur.</span><span class="sxs-lookup"><span data-stu-id="ada84-185">The code in the `PopulateAssignedCourseData` method reads through all Course entities in order to load a list of courses using the view model class.</span></span> <span data-ttu-id="ada84-186">Her Elbette, kod indirmelere Eğitmen içinde var olup olmadığını denetler `Courses` gezinti özelliği.</span><span class="sxs-lookup"><span data-stu-id="ada84-186">For each course, the code checks whether the course exists in the instructor's `Courses` navigation property.</span></span> <span data-ttu-id="ada84-187">Bir indirmelere Eğitmen atanmış olup olmadığını denetlerken verimli arama oluşturmak için Eğitmen atanan kurslar içine konur bir `HashSet` koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="ada84-187">To create efficient lookup when checking whether a course is assigned to the instructor, the courses assigned to the instructor are put into a `HashSet` collection.</span></span> <span data-ttu-id="ada84-188">`Assigned` Özelliği ayarlanmış kurslara true Eğitmen atanır.</span><span class="sxs-lookup"><span data-stu-id="ada84-188">The `Assigned` property  is set to true for courses the instructor is assigned to.</span></span> <span data-ttu-id="ada84-189">Görünüm, hangi onay kutuları olarak görüntülenmelidir seçili belirlemek için bu özelliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="ada84-189">The view will use this property to determine which check boxes must be displayed as selected.</span></span> <span data-ttu-id="ada84-190">Son olarak, liste görünümünde geçirilecek `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="ada84-190">Finally, the list is passed to the view in `ViewData`.</span></span>

<span data-ttu-id="ada84-191">Ardından, kullanıcı tıklattığında yürütülen kod ekleme **kaydetmek**.</span><span class="sxs-lookup"><span data-stu-id="ada84-191">Next, add the code that's executed when the user clicks **Save**.</span></span> <span data-ttu-id="ada84-192">Değiştir `EditPost` yöntemi aşağıdaki kod ve güncelleştirmeleri yeni bir yöntem ekleyin `Courses` Eğitmen varlık gezinti özelliği.</span><span class="sxs-lookup"><span data-stu-id="ada84-192">Replace the `EditPost` method with the following code, and add a new method that updates the `Courses` navigation property of the Instructor entity.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

<span data-ttu-id="ada84-193">Yöntem imzası HttpGet farklıdır `Edit` yöntem adını değişiklikleri şekilde yöntemi `EditPost` geri `Edit`.</span><span class="sxs-lookup"><span data-stu-id="ada84-193">The method signature is now different from the HttpGet `Edit` method, so the method name changes from `EditPost` back to `Edit`.</span></span>

<span data-ttu-id="ada84-194">Görünüm indirmelere varlıklar koleksiyonu sahip olmadığından, model bağlayıcı otomatik olarak güncelleştirilemiyor `CourseAssignments` gezinti özelliği.</span><span class="sxs-lookup"><span data-stu-id="ada84-194">Since the view doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="ada84-195">Güncelleştirilecek model bağlayıcısını kullanmak yerine `CourseAssignments` gezinti özelliği, yeni bunu `UpdateInstructorCourses` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="ada84-195">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="ada84-196">Bu nedenle hariç gerek `CourseAssignments` model bağlama özelliğinden.</span><span class="sxs-lookup"><span data-stu-id="ada84-196">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="ada84-197">Bu çağıran kodu herhangi bir değişiklik gerektirmez `TryUpdateModel` uygulamaları güvenilir listeye almayı aşırı kullandığımızdan ve `CourseAssignments` ekleme listesinde değil.</span><span class="sxs-lookup"><span data-stu-id="ada84-197">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="ada84-198">Hiçbir onay kutuları seçilmedi, varsa kodda `UpdateInstructorCourses` başlatır `CourseAssignments` gezinti özelliği boş bir koleksiyon ve döndürür:</span><span class="sxs-lookup"><span data-stu-id="ada84-198">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

<span data-ttu-id="ada84-199">Kod sonra veritabanındaki tüm kursları aracılığıyla döngüler ve her indirmelere görünümünde seçilen dosyalardan karşı Eğitmen atanmış olanları karşı denetler.</span><span class="sxs-lookup"><span data-stu-id="ada84-199">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the view.</span></span> <span data-ttu-id="ada84-200">Verimli aramalar kolaylaştırmak için sonraki iki koleksiyon depolanmış `HashSet` nesneleri.</span><span class="sxs-lookup"><span data-stu-id="ada84-200">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="ada84-201">Bir indirmelere onay kutusunu seçildi ancak indirmelere değil `Instructor.CourseAssignments` gezinti özelliği, koleksiyon gezinti özelliği içinde indirmelere eklenir.</span><span class="sxs-lookup"><span data-stu-id="ada84-201">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

<span data-ttu-id="ada84-202">Bir indirmelere onay kutusunun seçili edilmedi ancak indirmelere bulunduğu `Instructor.CourseAssignments` gezinti özelliği, indirmelere Gezinti özelliğinden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="ada84-202">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a><span data-ttu-id="ada84-203">Eğitmen görünümleri güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="ada84-203">Update the Instructor views</span></span>

<span data-ttu-id="ada84-204">İçinde *Views/Instructors/Edit.cshtml*, ekleme bir **kurslar** aşağıdakileri ekleyerek onay kutularını dizisi ile alan hemen sonra kod `div` için öğeleri **Office**  alan ve önce `div` öğesi için **kaydetmek** düğmesi.</span><span class="sxs-lookup"><span data-stu-id="ada84-204">In *Views/Instructors/Edit.cshtml*, add a **Courses** field with an array of check boxes by adding the following code immediately after the `div` elements for the **Office** field and before the `div` element for the **Save** button.</span></span>

<a id="notepad"></a>
> [!NOTE] 
> <span data-ttu-id="ada84-205">Visual Studio'da kodu yapıştırın, satır sonları kodu keser bir biçimde değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="ada84-205">When you paste the code in Visual Studio, line breaks will be changed in a way that breaks the code.</span></span>  <span data-ttu-id="ada84-206">CTRL + Z otomatik biçimlendirme geri almak için bir kez basın.</span><span class="sxs-lookup"><span data-stu-id="ada84-206">Press Ctrl+Z one time to undo the automatic formatting.</span></span>  <span data-ttu-id="ada84-207">Burada gördüğünüz gibi göründükleri böylece satır sonları düzeltir.</span><span class="sxs-lookup"><span data-stu-id="ada84-207">This will fix the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="ada84-208">Girinti kusursuz, olmak zorunda değildir ancak `@</tr><tr>`, `@:<td>`, `@:</td>`, ve `@:</tr>` satırlarını her tek bir satıra gösterildiği gibi veya olmalıdır, bir çalışma zamanı hatasıyla karşılaşırsınız.</span><span class="sxs-lookup"><span data-stu-id="ada84-208">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown or you'll get a runtime error.</span></span> <span data-ttu-id="ada84-209">Seçili yeni kod bloğu ile sekme yeni kodu var olan kodu ile hizalamak için üç kez basın.</span><span class="sxs-lookup"><span data-stu-id="ada84-209">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="ada84-210">Bu sorunu durumunu kontrol edebilirsiniz [burada](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="ada84-210">You can check the status of this problem [here](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

<span data-ttu-id="ada84-211">Bu kod, üç sütun içeren bir HTML tablosu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ada84-211">This code creates an HTML table that has three columns.</span></span> <span data-ttu-id="ada84-212">Her sütunda, indirmelere numarası ve başlığı oluşan bir resim yazısı tarafından izlenen bir onay kutusudur.</span><span class="sxs-lookup"><span data-stu-id="ada84-212">In each column is a check box followed by a caption that consists of the course number and title.</span></span> <span data-ttu-id="ada84-213">Tüm onay kutularını grup olarak kabul edilmesi için oldukları model Bağlayıcısı bilgi veren aynı adı ("selectedCourses") sahip.</span><span class="sxs-lookup"><span data-stu-id="ada84-213">The check boxes all have the same name ("selectedCourses"), which informs the model binder that they're to be treated as a group.</span></span> <span data-ttu-id="ada84-214">Her onay kutusunun değer özniteliği değerine ayarlanmış `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="ada84-214">The value attribute of each check box is set to the value of `CourseID`.</span></span> <span data-ttu-id="ada84-215">Sayfa gönderildiğinde, model bağlayıcı bir dizi oluşan denetleyicisine geçirir. `CourseID` yalnızca, seçilen onay kutuları için değerleri.</span><span class="sxs-lookup"><span data-stu-id="ada84-215">When the page is posted, the model binder passes an array to the controller that consists of the `CourseID` values for only the check boxes which are selected.</span></span>

<span data-ttu-id="ada84-216">Onay kutularını başlangıçta işlendiğinde, eğitmen atanan kurslara olanlar bunları (bunları denetlenen gösterir) seçer öznitelikleri kullanıma.</span><span class="sxs-lookup"><span data-stu-id="ada84-216">When the check boxes are initially rendered, those that are for courses assigned to the instructor have checked attributes, which selects them (displays them checked).</span></span>

<span data-ttu-id="ada84-217">Uygulama, belirleyin **Eğitmen** sekmesine ve tıklayın **Düzenle** görmek için bir eğitmen üzerinde **Düzenle** sayfası.</span><span class="sxs-lookup"><span data-stu-id="ada84-217">Run the app, select the **Instructors** tab, and click **Edit** on an instructor to see the **Edit** page.</span></span>

![Kurslar Eğitmen Düzenle sayfası](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="ada84-219">Bazı indirmelere atamalarını değiştirin ve Kaydet'e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="ada84-219">Change some course assignments and click Save.</span></span> <span data-ttu-id="ada84-220">Dizin sayfasında yaptığınız değişiklikleri yansıtır.</span><span class="sxs-lookup"><span data-stu-id="ada84-220">The changes you make are reflected on the Index page.</span></span>

> [!NOTE] 
> <span data-ttu-id="ada84-221">Eğitmen indirmelere verileri düzenlemek için burada uygulanan yaklaşıma iyi kurslar sınırlı sayıda olduğunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="ada84-221">The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="ada84-222">Farklı bir kullanıcı Arabirimi ve farklı bir güncelleştirme yöntemi, çok daha büyük olan koleksiyonları için gerekli olacak.</span><span class="sxs-lookup"><span data-stu-id="ada84-222">For collections that are much larger, a different UI and a different updating method would be required.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="ada84-223">Güncelleştirme Sil sayfası</span><span class="sxs-lookup"><span data-stu-id="ada84-223">Update the Delete page</span></span>

<span data-ttu-id="ada84-224">İçinde *InstructorsController.cs*, silme `DeleteConfirmed` aşağıdaki kodu yerine yöntemi ve Ekle.</span><span class="sxs-lookup"><span data-stu-id="ada84-224">In *InstructorsController.cs*, delete the `DeleteConfirmed` method and insert the following code in its place.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

<span data-ttu-id="ada84-225">Bu kod, aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="ada84-225">This code makes the following changes:</span></span>

* <span data-ttu-id="ada84-226">İçin yükleme mu eager `CourseAssignments` gezinti özelliği.</span><span class="sxs-lookup"><span data-stu-id="ada84-226">Does eager loading for the `CourseAssignments` navigation property.</span></span>  <span data-ttu-id="ada84-227">Bu eklemek zorunda veya EF olmaz biliyorsanız hakkında ilgili `CourseAssignment` varlıkları ve bunları silmez.</span><span class="sxs-lookup"><span data-stu-id="ada84-227">You have to include this or EF won't know about related `CourseAssignment` entities and won't delete them.</span></span>  <span data-ttu-id="ada84-228">Bunları okuyun gerek önlemek için burada, art arda silme veritabanında yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ada84-228">To avoid needing to read them here you could configure cascade delete in the database.</span></span>

* <span data-ttu-id="ada84-229">Silinecek Eğitmen herhangi Departmanlar yönetici olarak atanmış ise bu bölümlerden Eğitmen atama kaldırır.</span><span class="sxs-lookup"><span data-stu-id="ada84-229">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="add-office-location-and-courses-to-the-create-page"></a><span data-ttu-id="ada84-230">Ofis konumu ve kurslar Oluştur sayfası ekleme</span><span class="sxs-lookup"><span data-stu-id="ada84-230">Add office location and courses to the Create page</span></span>

<span data-ttu-id="ada84-231">İçinde *InstructorsController.cs*, HttpGet silip HttpPost `Create` yöntemleri ve ardından yerine aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ada84-231">In *InstructorsController.cs*, delete the HttpGet and HttpPost `Create` methods, and then add the following code in their place:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

<span data-ttu-id="ada84-232">Bu kod ne için gördüğünüz için benzer `Edit` , başlangıçta hiçbir kurslar dışındaki yöntemleri seçilir.</span><span class="sxs-lookup"><span data-stu-id="ada84-232">This code is similar to what you saw for the `Edit` methods except that initially no courses are selected.</span></span> <span data-ttu-id="ada84-233">HttpGet `Create` yöntem çağrılarını `PopulateAssignedCourseData` değil olabileceğinden ancak seçilen kurslar yöntemi sipariş için boş bir koleksiyon sağlamak için `foreach` (Aksi halde görünümü kodu throw bir null başvuru özel durumu) görünümünde döngü.</span><span class="sxs-lookup"><span data-stu-id="ada84-233">The HttpGet `Create` method calls the `PopulateAssignedCourseData` method not because there might be courses selected but in order to provide an empty collection for the `foreach` loop in the view (otherwise the view code would throw a null reference exception).</span></span>

<span data-ttu-id="ada84-234">HttpPost `Create` yöntemi her seçili indirmelere ekler `CourseAssignments` doğrulama hatalarını denetler ve yeni Eğitmen veritabanına ekler önce gezinti özelliği.</span><span class="sxs-lookup"><span data-stu-id="ada84-234">The HttpPost `Create` method adds each selected course to the `CourseAssignments` navigation property before it checks for validation errors and adds the new instructor to the database.</span></span> <span data-ttu-id="ada84-235">Bile model hatalar varsa (örneğin bir, geçersiz bir tarih anahtarlı kullanıcı) modeli hataları var ve sayfaya bir hata iletisi ile yeniden yapılan indirmelere seçimleri otomatik olarak geri böylece kurslar eklenir.</span><span class="sxs-lookup"><span data-stu-id="ada84-235">Courses are added even if there are model errors so that when there are model errors (for an example, the user keyed an invalid date), and the page is redisplayed with an error message, any course selections that were made are automatically restored.</span></span>

<span data-ttu-id="ada84-236">Kurslara eklemek için dikkat `CourseAssignments` sahip özellik boş bir koleksiyon olarak başlatmak için gezinti özelliği:</span><span class="sxs-lookup"><span data-stu-id="ada84-236">Notice that in order to be able to add courses to the `CourseAssignments` navigation property you have to initialize the property as an empty collection:</span></span>

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

<span data-ttu-id="ada84-237">Denetleyici kodda bunu alternatif olarak, eğitmen modelinde yoksa, otomatik olarak koleksiyonu aşağıdaki örnekte gösterildiği gibi oluşturmak için özellik alıcısı değiştirerek bunu:</span><span class="sxs-lookup"><span data-stu-id="ada84-237">As an alternative to doing this in controller code, you could do it in the Instructor model by changing the property getter to automatically create the collection if it doesn't exist, as shown in the following example:</span></span>

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

<span data-ttu-id="ada84-238">Değiştirirseniz `CourseAssignments` özelliği bu şekilde, denetleyici açık özellik başlatma kodda kaldırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ada84-238">If you modify the `CourseAssignments` property in this way, you can remove the explicit property initialization code in the controller.</span></span>

<span data-ttu-id="ada84-239">İçinde *Views/Instructor/Create.cshtml*, bir office konumu metin kutusu ekleyin ve Gönder düğmesine önce kurslara onay kutularını işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="ada84-239">In *Views/Instructor/Create.cshtml*, add an office location text box and check boxes for courses before the Submit button.</span></span> <span data-ttu-id="ada84-240">Düzen sayfası olduğu gibi söz konusu olduğunda [kopyalayıp yapıştırın, Visual Studio kodu yeniden biçimlendirir, biçimlendirme düzeltme](#notepad).</span><span class="sxs-lookup"><span data-stu-id="ada84-240">As in the case of the Edit page, [fix the formatting if Visual Studio reformats the code when you paste it](#notepad).</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

<span data-ttu-id="ada84-241">Uygulama çalışırken ve bir eğitmen oluşturma sınayın.</span><span class="sxs-lookup"><span data-stu-id="ada84-241">Test by running the app and creating an instructor.</span></span> 

## <a name="handling-transactions"></a><span data-ttu-id="ada84-242">İşleme işlemleri</span><span class="sxs-lookup"><span data-stu-id="ada84-242">Handling Transactions</span></span>

<span data-ttu-id="ada84-243">İçinde anlatıldığı gibi [CRUD öğretici](crud.md), Entity Framework örtük olarak işlemleri uygular.</span><span class="sxs-lookup"><span data-stu-id="ada84-243">As explained in the [CRUD tutorial](crud.md), the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="ada84-244">Burada, daha fazla denetim--Örneğin, bir işlem içinde--Entity Framework dışında yapılan işlemleri dahil etmek istiyorsanız senaryolar görmek için [işlemleri](https://docs.microsoft.com/ef/core/saving/transactions).</span><span class="sxs-lookup"><span data-stu-id="ada84-244">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](https://docs.microsoft.com/ef/core/saving/transactions).</span></span>

## <a name="summary"></a><span data-ttu-id="ada84-245">Özet</span><span class="sxs-lookup"><span data-stu-id="ada84-245">Summary</span></span>

<span data-ttu-id="ada84-246">İlgili verilerle çalışmaya giriş tamamladınız.</span><span class="sxs-lookup"><span data-stu-id="ada84-246">You have now completed the introduction to working with related data.</span></span> <span data-ttu-id="ada84-247">Sonraki öğreticide eşzamanlılık çakışmaları nasıl ele alınacağını görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="ada84-247">In the next tutorial you'll see how to handle concurrency conflicts.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ada84-248">[Önceki](read-related-data.md)
> [sonraki](concurrency.md)</span><span class="sxs-lookup"><span data-stu-id="ada84-248">[Previous](read-related-data.md)
[Next](concurrency.md)</span></span>  
