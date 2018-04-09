---
title: Razor sayfalarının ASP.NET Core - EF çekirdek ile ilgili veriler - 8'in 7 güncelleştir
author: rick-anderson
description: Bu öğreticide yabancı anahtar alanları ve gezinti özellikleri güncelleştirerek ilgili verileri güncelleştirin.
manager: wpickett
ms.author: riande
ms.date: 11/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-rp/update-related-data
ms.openlocfilehash: d793a7ca3635108ed7941ccc8578572afd79c305
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a><span data-ttu-id="ab28b-103">Razor sayfalarının ASP.NET Core - EF çekirdek ile ilgili veriler - 8'in 7 güncelleştir</span><span class="sxs-lookup"><span data-stu-id="ab28b-103">Razor Pages with EF Core in ASP.NET Core - Update Related Data - 7 of 8</span></span>

<span data-ttu-id="ab28b-104">Tarafından [zel Dykstra](https://github.com/tdykstra), ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ab28b-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="ab28b-105">Bu öğretici, ilgili verileri güncelleştirme gösterir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-105">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="ab28b-106">Olamaz çözmek sorunlarla karşılaşırsanız, indirme [Bu aşama için tamamlanan uygulama](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part7).</span><span class="sxs-lookup"><span data-stu-id="ab28b-106">If you run into problems you can't solve, download the [completed app for this stage](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part7).</span></span>

<span data-ttu-id="ab28b-107">Aşağıdaki çizimler tamamlanmış sayfaları bazıları gösterir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-107">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="ab28b-108">![İndirmelere düzenleme sayfasını](update-related-data/_static/course-edit.png)
![sayfasını Eğitmen Düzenle](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="ab28b-108">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="ab28b-109">İnceleyin ve oluşturma ve düzenleme indirmelere sayfaları test edin.</span><span class="sxs-lookup"><span data-stu-id="ab28b-109">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="ab28b-110">Yeni bir indirmelere oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ab28b-110">Create a new course.</span></span> <span data-ttu-id="ab28b-111">Departman birincil anahtara göre (tamsayı) adı değil seçilir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-111">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="ab28b-112">Yeni indirmelere düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="ab28b-112">Edit the new course.</span></span> <span data-ttu-id="ab28b-113">Testi tamamladığınızda, yeni indirmelere silin.</span><span class="sxs-lookup"><span data-stu-id="ab28b-113">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="ab28b-114">Ortak kodun paylaşmak için temel sınıf oluşturma</span><span class="sxs-lookup"><span data-stu-id="ab28b-114">Create a base class to share common code</span></span>

<span data-ttu-id="ab28b-115">Kurslar/oluşturma ve kurslar/düzenleme sayfaları her bölüm adlarının bir listesini gerekir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-115">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="ab28b-116">Create *Pages/Courses/DepartmentNamePageModel.cshtml.cs* oluşturma ve düzenleme sayfalar için temel sınıf:</span><span class="sxs-lookup"><span data-stu-id="ab28b-116">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="ab28b-117">Önceki kod oluşturur bir [SelectList](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) bölüm adları listesi içerecek şekilde.</span><span class="sxs-lookup"><span data-stu-id="ab28b-117">The preceding code creates a [SelectList](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="ab28b-118">Varsa `selectedDepartment` belirtilirse, bu bölüm seçildiğinde, `SelectList`.</span><span class="sxs-lookup"><span data-stu-id="ab28b-118">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="ab28b-119">Oluşturma ve düzenleme sayfası modeli sınıfları öğesinden türetilen `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="ab28b-119">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="ab28b-120">Kurslar sayfalarını özelleştirme</span><span class="sxs-lookup"><span data-stu-id="ab28b-120">Customize the Courses Pages</span></span>

<span data-ttu-id="ab28b-121">Yeni bir indirmelere varlık oluşturulduğunda, var olan bir bölüm için bir ilişki olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-121">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="ab28b-122">Bir indirmelere oluşturulurken bir bölüm eklemek için departman seçmek için aşağı açılan liste oluşturma ve düzenleme için temel sınıfı içerir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-122">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="ab28b-123">Aşağı açılan liste kümeleri `Course.DepartmentID` yabancı anahtar (FK) özelliği.</span><span class="sxs-lookup"><span data-stu-id="ab28b-123">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="ab28b-124">EF çekirdek kullanan `Course.DepartmentID` yüklemek için FK `Department` gezinti özelliği.</span><span class="sxs-lookup"><span data-stu-id="ab28b-124">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![İndirmelere oluşturma](update-related-data/_static/ddl.png)

<span data-ttu-id="ab28b-126">Oluştur sayfası modeli aşağıdaki kod ile güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ab28b-126">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="ab28b-127">Önceki kod:</span><span class="sxs-lookup"><span data-stu-id="ab28b-127">The preceding code:</span></span>

* <span data-ttu-id="ab28b-128">Türetilen `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="ab28b-128">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="ab28b-129">Kullanan `TryUpdateModelAsync` önlemek için [overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="ab28b-129">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="ab28b-130">Değiştirir `ViewData["DepartmentID"]` ile `DepartmentNameSL` (temel sınıfından).</span><span class="sxs-lookup"><span data-stu-id="ab28b-130">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="ab28b-131">`ViewData["DepartmentID"]` değiştirilir kesin türü belirtilmiş ile `DepartmentNameSL`.</span><span class="sxs-lookup"><span data-stu-id="ab28b-131">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="ab28b-132">Kesin türü belirtilmiş modeller üzerinden zayıf yazılmış tercih ettiği.</span><span class="sxs-lookup"><span data-stu-id="ab28b-132">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="ab28b-133">Daha fazla bilgi için bkz: [verileri (ViewData ve ViewBag)'zayıf yazılmış](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="ab28b-133">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="ab28b-134">Güncelleştirme kurslar oluşturma sayfası</span><span class="sxs-lookup"><span data-stu-id="ab28b-134">Update the Courses Create page</span></span>

<span data-ttu-id="ab28b-135">Güncelleştirme *Pages/Courses/Create.cshtml* aşağıdaki biçimlendirme ile:</span><span class="sxs-lookup"><span data-stu-id="ab28b-135">Update *Pages/Courses/Create.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="ab28b-136">Önceki biçimlendirme, aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="ab28b-136">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="ab28b-137">Resim yazısı gelen değişiklikler **DepartmentID** için **departmanı**.</span><span class="sxs-lookup"><span data-stu-id="ab28b-137">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="ab28b-138">Değiştirir `"ViewBag.DepartmentID"` ile `DepartmentNameSL` (temel sınıfından).</span><span class="sxs-lookup"><span data-stu-id="ab28b-138">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="ab28b-139">"Select departmanı" seçeneği ekler.</span><span class="sxs-lookup"><span data-stu-id="ab28b-139">Adds the "Select Department" option.</span></span> <span data-ttu-id="ab28b-140">Bu değişiklik "Select departman" yerine ilk bölümü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ab28b-140">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="ab28b-141">Departman seçili olmadığında bir doğrulama ileti ekler.</span><span class="sxs-lookup"><span data-stu-id="ab28b-141">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="ab28b-142">Razor sayfasını kullanan [seçin etiket Yardımcısı](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="ab28b-142">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="ab28b-143">Oluştur sayfası sınayın.</span><span class="sxs-lookup"><span data-stu-id="ab28b-143">Test the Create page.</span></span> <span data-ttu-id="ab28b-144">Oluştur sayfası bölüm kimliği yerine bölüm adını görüntüler</span><span class="sxs-lookup"><span data-stu-id="ab28b-144">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="ab28b-145">Sayfa kurslar Düzenle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="ab28b-145">Update the Courses Edit page.</span></span>

<span data-ttu-id="ab28b-146">Düzen sayfası modeli aşağıdaki kod ile güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ab28b-146">Update the edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="ab28b-147">Değişiklikleri Oluştur sayfası modelinde yapılan benzerdir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-147">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="ab28b-148">Önceki kod `PopulateDepartmentsDropDownList` , aşağı açılan listesinde belirtilen departmanı seçin bölüm kimliği, geçirir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-148">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="ab28b-149">Güncelleştirme *Pages/Courses/Edit.cshtml* aşağıdaki biçimlendirme ile:</span><span class="sxs-lookup"><span data-stu-id="ab28b-149">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="ab28b-150">Önceki biçimlendirme, aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="ab28b-150">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="ab28b-151">İndirmelere kimliğini görüntüler</span><span class="sxs-lookup"><span data-stu-id="ab28b-151">Displays the course ID.</span></span> <span data-ttu-id="ab28b-152">Birincil anahtarı (PK) bir varlığın genellikle görüntülenmiyor.</span><span class="sxs-lookup"><span data-stu-id="ab28b-152">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="ab28b-153">BA kullanıcılara genellikle anlamsızdır.</span><span class="sxs-lookup"><span data-stu-id="ab28b-153">PKs are usually meaningless to users.</span></span> <span data-ttu-id="ab28b-154">Bu durumda, PK indirmelere sayısıdır.</span><span class="sxs-lookup"><span data-stu-id="ab28b-154">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="ab28b-155">Resim yazısı gelen değişiklikler **DepartmentID** için **departmanı**.</span><span class="sxs-lookup"><span data-stu-id="ab28b-155">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="ab28b-156">Değiştirir `"ViewBag.DepartmentID"` ile `DepartmentNameSL` (temel sınıfından).</span><span class="sxs-lookup"><span data-stu-id="ab28b-156">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="ab28b-157">"Select departmanı" seçeneği ekler.</span><span class="sxs-lookup"><span data-stu-id="ab28b-157">Adds the "Select Department" option.</span></span> <span data-ttu-id="ab28b-158">Bu değişiklik "Select departman" yerine ilk bölümü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ab28b-158">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="ab28b-159">Departman seçili olmadığında bir doğrulama ileti ekler.</span><span class="sxs-lookup"><span data-stu-id="ab28b-159">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="ab28b-160">Sayfa gizli bir alan içeriyor (`<input type="hidden">`) indirmelere numarası.</span><span class="sxs-lookup"><span data-stu-id="ab28b-160">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="ab28b-161">Ekleme bir `<label>` Yardımcıyla etiketi `asp-for="Course.CourseID"` gizli alan gereksinimini ortadan kaldırmak değil.</span><span class="sxs-lookup"><span data-stu-id="ab28b-161">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="ab28b-162">`<input type="hidden">` Kullanıcı tıklattığında gönderilen veriler dahil edilecek indirmelere numarası gereklidir **kaydetmek**.</span><span class="sxs-lookup"><span data-stu-id="ab28b-162">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="ab28b-163">Güncelleştirilmiş kod sınayın.</span><span class="sxs-lookup"><span data-stu-id="ab28b-163">Test the updated code.</span></span> <span data-ttu-id="ab28b-164">Oluşturma, düzenleme ve bir indirmelere silin.</span><span class="sxs-lookup"><span data-stu-id="ab28b-164">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="ab28b-165">Ayrıntılara AsNoTracking ekleme ve silme sayfa modelleri</span><span class="sxs-lookup"><span data-stu-id="ab28b-165">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="ab28b-166">[AsNoTracking](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) izleme gerekli olmadığı durumlarda, performansı artırabilir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-166">[AsNoTracking](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="ab28b-167">Ekleme `AsNoTracking` silebilir ve Ayrıntılar sayfası modeli.</span><span class="sxs-lookup"><span data-stu-id="ab28b-167">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="ab28b-168">Aşağıdaki kod güncelleştirilmiş Delete sayfa modeli gösterir:</span><span class="sxs-lookup"><span data-stu-id="ab28b-168">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="ab28b-169">Güncelleştirme `OnGetAsync` yönteminde *Pages/Courses/Details.cshtml.cs* dosyası:</span><span class="sxs-lookup"><span data-stu-id="ab28b-169">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="ab28b-170">Sil ve ayrıntıları sayfaları değiştirme</span><span class="sxs-lookup"><span data-stu-id="ab28b-170">Modify the Delete and Details pages</span></span>

<span data-ttu-id="ab28b-171">Aşağıdaki biçimlendirmede silme Razor sayfasıyla güncelleştirmesi:</span><span class="sxs-lookup"><span data-stu-id="ab28b-171">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="ab28b-172">Ayrıntılar sayfası aynı değişiklikleri yapın.</span><span class="sxs-lookup"><span data-stu-id="ab28b-172">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="ab28b-173">İndirmelere sayfalarını test</span><span class="sxs-lookup"><span data-stu-id="ab28b-173">Test the Course pages</span></span>

<span data-ttu-id="ab28b-174">Test oluşturma, ayrıntı, düzenleme ve silme.</span><span class="sxs-lookup"><span data-stu-id="ab28b-174">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="ab28b-175">Eğitmen sayfaları güncelleştir</span><span class="sxs-lookup"><span data-stu-id="ab28b-175">Update the instructor pages</span></span>

<span data-ttu-id="ab28b-176">Aşağıdaki bölümlerde Eğitmen sayfaları güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="ab28b-176">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="ab28b-177">Office Konumu Ekle</span><span class="sxs-lookup"><span data-stu-id="ab28b-177">Add office location</span></span>

<span data-ttu-id="ab28b-178">Eğitmen kaydı düzenlerken, eğitmen office atama güncelleştirmek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ab28b-178">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="ab28b-179">`Instructor` Varlık bir tane sıfır-veya-bire ilişkisine sahip olan `OfficeAssignment` varlık.</span><span class="sxs-lookup"><span data-stu-id="ab28b-179">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="ab28b-180">Eğitmen kodu işlemesi gerekir:</span><span class="sxs-lookup"><span data-stu-id="ab28b-180">The instructor code must handle:</span></span>

* <span data-ttu-id="ab28b-181">Kullanıcı office atama temizler, silin `OfficeAssignment` varlık.</span><span class="sxs-lookup"><span data-stu-id="ab28b-181">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="ab28b-182">Kullanıcı bir office atama girer ve boş varsa, yeni bir oluşturun `OfficeAssignment` varlık.</span><span class="sxs-lookup"><span data-stu-id="ab28b-182">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="ab28b-183">Kullanıcı office atama değiştirirse, güncelleştirme `OfficeAssignment` varlık.</span><span class="sxs-lookup"><span data-stu-id="ab28b-183">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="ab28b-184">Eğitmen Düzenle sayfası modeli aşağıdaki kod ile güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ab28b-184">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="ab28b-185">Önceki kod:</span><span class="sxs-lookup"><span data-stu-id="ab28b-185">The preceding code:</span></span>

- <span data-ttu-id="ab28b-186">Geçerli alır `Instructor` istekli yükleme için kullanarak veritabanını varlıktan `OfficeAssignment` gezinti özelliği.</span><span class="sxs-lookup"><span data-stu-id="ab28b-186">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
- <span data-ttu-id="ab28b-187">Alınan güncelleştirmeleri `Instructor` model bağlayıcı değerlerle varlık.</span><span class="sxs-lookup"><span data-stu-id="ab28b-187">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="ab28b-188">`TryUpdateModel` engeller [overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="ab28b-188">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
- <span data-ttu-id="ab28b-189">Ofis konumu boş ise, ayarlar `Instructor.OfficeAssignment` null.</span><span class="sxs-lookup"><span data-stu-id="ab28b-189">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="ab28b-190">Zaman `Instructor.OfficeAssignment` null, ilgili satırda olduğundan `OfficeAssignment` tablo silinir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-190">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="ab28b-191">Eğitmen düzenleme sayfasını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="ab28b-191">Update the instructor Edit page</span></span>

<span data-ttu-id="ab28b-192">Güncelleştirme *Pages/Instructors/Edit.cshtml* office konum:</span><span class="sxs-lookup"><span data-stu-id="ab28b-192">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="ab28b-193">Eğitmen ofis konumu değiştirebilir doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="ab28b-193">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="ab28b-194">Eğitmen Düzenle sayfasına indirmelere atamaları ekleme</span><span class="sxs-lookup"><span data-stu-id="ab28b-194">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="ab28b-195">Eğitmen kurslar herhangi bir sayıda öğretir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-195">Instructors may teach any number of courses.</span></span> <span data-ttu-id="ab28b-196">Bu bölümde, indirmelere atamalarını değiştirme yeteneğini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ab28b-196">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="ab28b-197">Aşağıdaki resimde, düzenleme sayfasını güncelleştirilmiş Eğitmen gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ab28b-197">The following image shows the updated instructor Edit page:</span></span>

![Kurslar Eğitmen Düzenle sayfası](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="ab28b-199">`Course` ve `Instructor` bir çok-çok ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="ab28b-199">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="ab28b-200">İlişkileri kaldırın ve eklemek için ekleme ve kaldırma varlıklardan `CourseAssignments` katılma varlık kümesi.</span><span class="sxs-lookup"><span data-stu-id="ab28b-200">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="ab28b-201">Onay kutuları bir eğitmen atandığı kurslar değişiklikler etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="ab28b-201">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="ab28b-202">Her indirmelere veritabanındaki için bir onay kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-202">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="ab28b-203">Eğitmen atandığı kurslar denetlenir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-203">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="ab28b-204">Kullanıcı seçin veya indirmelere atamalarını değiştirmek için onay kutularını temizleyin.</span><span class="sxs-lookup"><span data-stu-id="ab28b-204">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="ab28b-205">Kurslar sayısı çok büyük ise:</span><span class="sxs-lookup"><span data-stu-id="ab28b-205">If the number of courses were much greater:</span></span>

* <span data-ttu-id="ab28b-206">Farklı bir kullanıcı arabirimi, kursları görüntülemek için büyük olasılıkla kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="ab28b-206">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="ab28b-207">İlişki oluşturma veya silme için birleştirme varlığa düzenleme yöntemi değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="ab28b-207">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="ab28b-208">Destek sınıfları eklemek oluşturma ve düzenleme Eğitmen sayfaları</span><span class="sxs-lookup"><span data-stu-id="ab28b-208">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="ab28b-209">Oluşturma *SchoolViewModels/AssignedCourseData.cs* aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="ab28b-209">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="ab28b-210">`AssignedCourseData` Sınıfı bir eğitmen tarafından atanan kurslar onay kutularını oluşturmak için verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-210">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="ab28b-211">Oluşturma *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* temel sınıfı:</span><span class="sxs-lookup"><span data-stu-id="ab28b-211">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="ab28b-212">`InstructorCoursesPageModel` Taban sınıf, düzenleme için kullanabilir ve sayfa modelleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ab28b-212">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="ab28b-213">`PopulateAssignedCourseData` Tüm okur `Course` doldurmak için varlıklar `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="ab28b-213">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="ab28b-214">Kod her Elbette, ayarlar `CourseID`, başlık ve karşılamadığını Eğitmen indirmelere atanır.</span><span class="sxs-lookup"><span data-stu-id="ab28b-214">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="ab28b-215">A [Hashset'i](https://docs.microsoft.com/dotnet/api/system.collections.generic.hashset-1) verimli aramalar oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ab28b-215">A [HashSet](https://docs.microsoft.com/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="ab28b-216">Eğitmen Düzenle sayfası modeli</span><span class="sxs-lookup"><span data-stu-id="ab28b-216">Instructors Edit page model</span></span>

<span data-ttu-id="ab28b-217">Eğitmen Düzenle sayfası modeli aşağıdaki kod ile güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ab28b-217">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="ab28b-218">Önceki kod office atama değişiklikleri işler.</span><span class="sxs-lookup"><span data-stu-id="ab28b-218">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="ab28b-219">Razor görünüm Eğitmen güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ab28b-219">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="ab28b-220">Visual Studio'da kodu yapıştırın, satır sonları kodu keser bir biçimde değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-220">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="ab28b-221">CTRL + Z otomatik biçimlendirme geri almak için bir kez basın.</span><span class="sxs-lookup"><span data-stu-id="ab28b-221">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="ab28b-222">Burada gördüğünüz gibi göründükleri böylece Ctrl + Z satır sonları giderir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-222">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="ab28b-223">Girinti kusursuz, olmak zorunda değildir ancak `@</tr><tr>`, `@:<td>`, `@:</td>`, ve `@:</tr>` satırlarını her olmalıdır tek bir satıra gösterildiği gibi.</span><span class="sxs-lookup"><span data-stu-id="ab28b-223">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="ab28b-224">Seçili yeni kod bloğu ile sekme yeni kodu var olan kodu ile hizalamak için üç kez basın.</span><span class="sxs-lookup"><span data-stu-id="ab28b-224">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="ab28b-225">Oy ya da bu hata durumunu gözden [bu bağlantıyla](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="ab28b-225">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="ab28b-226">Önceki kod üç sütunları içeren bir HTML tablo oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ab28b-226">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="ab28b-227">Her sütunun bir onay kutusu ve indirmelere numarası ve başlığı içeren bir resim yazısı vardır.</span><span class="sxs-lookup"><span data-stu-id="ab28b-227">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="ab28b-228">Tüm onay kutularını ("selectedCourses") aynı ada sahip.</span><span class="sxs-lookup"><span data-stu-id="ab28b-228">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="ab28b-229">Aynı adı kullanarak bir grup olarak işlemek için model bağlayıcı bildirir.</span><span class="sxs-lookup"><span data-stu-id="ab28b-229">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="ab28b-230">Value özniteliği her onay kutusunun kümesine `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="ab28b-230">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="ab28b-231">Sayfa gönderildiğinde, model bağlayıcı oluşan bir dizi geçirir `CourseID` yalnızca seçilen onay kutuları için değerleri.</span><span class="sxs-lookup"><span data-stu-id="ab28b-231">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="ab28b-232">Onay kutularını başlangıçta işlendiğinde, eğitmen atanan kurslar öznitelikleri kullanıma.</span><span class="sxs-lookup"><span data-stu-id="ab28b-232">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="ab28b-233">Uygulamayı çalıştırın ve güncelleştirilmiş Eğitmen düzenleme sayfasını sınayın.</span><span class="sxs-lookup"><span data-stu-id="ab28b-233">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="ab28b-234">Bazı indirmelere atamalarını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ab28b-234">Change some course assignments.</span></span> <span data-ttu-id="ab28b-235">Dizin sayfasında değişiklikleri yansıtır.</span><span class="sxs-lookup"><span data-stu-id="ab28b-235">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="ab28b-236">Not: Eğitmen indirmelere verileri düzenlemek için burada uygulanan yaklaşıma iyi kurslar sınırlı sayıda olduğunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="ab28b-236">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="ab28b-237">Çok daha büyük olan koleksiyonları için farklı bir kullanıcı Arabirimi ve farklı bir güncelleştirme yöntemi daha kullanışlı ve etkili olacaktır.</span><span class="sxs-lookup"><span data-stu-id="ab28b-237">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="ab28b-238">Güncelleştirme Eğitmen Oluştur sayfası</span><span class="sxs-lookup"><span data-stu-id="ab28b-238">Update the instructors Create page</span></span>

<span data-ttu-id="ab28b-239">Eğitmen Oluştur sayfası modeli aşağıdaki kod ile güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ab28b-239">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="ab28b-240">Önceki kod benzer *Pages/Instructors/Edit.cshtml.cs* kodu.</span><span class="sxs-lookup"><span data-stu-id="ab28b-240">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="ab28b-241">Eğitmen oluşturmak Razor sayfasını aşağıdaki biçimlendirme ile güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ab28b-241">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="ab28b-242">Eğitmen Oluştur sayfası sınayın.</span><span class="sxs-lookup"><span data-stu-id="ab28b-242">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="ab28b-243">Güncelleştirme Sil sayfası</span><span class="sxs-lookup"><span data-stu-id="ab28b-243">Update the Delete page</span></span>

<span data-ttu-id="ab28b-244">Delete sayfa modeli aşağıdaki kod ile güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ab28b-244">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="ab28b-245">Yukarıdaki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="ab28b-245">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ab28b-246">İstekli yükleme için kullandığı `CourseAssignments` gezinti özelliği.</span><span class="sxs-lookup"><span data-stu-id="ab28b-246">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="ab28b-247">`CourseAssignments` dahil edilmelidir veya Eğitmen silindiğinde silinmez.</span><span class="sxs-lookup"><span data-stu-id="ab28b-247">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="ab28b-248">Bunları okuyun gerek önlemek için veritabanında art arda silme yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ab28b-248">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="ab28b-249">Silinecek Eğitmen herhangi Departmanlar yönetici olarak atanmış ise bu bölümlerden Eğitmen atama kaldırır.</span><span class="sxs-lookup"><span data-stu-id="ab28b-249">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ab28b-250">[Önceki](xref:data/ef-rp/read-related-data)
> [sonraki](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="ab28b-250">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>
