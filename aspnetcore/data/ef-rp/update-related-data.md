---
<span data-ttu-id="e8850-101">title:ASP.NET Core 中的 Razor Pages 和 EF Core - 更新相关数据 - 第 7 个教程（共 8 个）author: rick-anderson description:本教程将通过更新外键字段和导航属性来更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="e8850-101">title: Razor Pages with EF Core in ASP.NET Core - Update Related Data - 7 of 8 author: rick-anderson description: In this tutorial, you update related data by updating foreign key fields and navigation properties.</span></span>
<span data-ttu-id="e8850-102">ms.author: riande ms.date:07/22/2019 no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR] uid: data/ef-rp/update-related-data</span><span class="sxs-lookup"><span data-stu-id="e8850-102">ms.author: riande ms.date: 07/22/2019 no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR] uid: data/ef-rp/update-related-data</span></span>
---

# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a><span data-ttu-id="e8850-103">ASP.NET Core 中的 Razor Pages 和 EF Core - 更新相关数据 - 第 7 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="e8850-103">Razor Pages with EF Core in ASP.NET Core - Update Related Data - 7 of 8</span></span>

<span data-ttu-id="e8850-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e8850-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e8850-105">本教程将介绍如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="e8850-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="e8850-106">下图显示了部分已完成页面。</span><span class="sxs-lookup"><span data-stu-id="e8850-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="e8850-107">![课程“编辑”页](update-related-data/_static/course-edit30.png)
![讲师”编辑”页](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="e8850-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="e8850-108">更新课程“创建”和“编辑”页</span><span class="sxs-lookup"><span data-stu-id="e8850-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="e8850-109">课程“创建”和“编辑”页的基架搭建代码具有显示院系 ID（整数）的“院系”下拉列表。</span><span class="sxs-lookup"><span data-stu-id="e8850-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="e8850-110">下拉列表应显示院系名称，因此这两个页面都需要院系名称列表。</span><span class="sxs-lookup"><span data-stu-id="e8850-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="e8850-111">若要提供该列表，请使用“创建”和“编辑”页的基类。</span><span class="sxs-lookup"><span data-stu-id="e8850-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="e8850-112">创建课程“创建”和“编辑”的基类</span><span class="sxs-lookup"><span data-stu-id="e8850-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="e8850-113">使用以下代码创建 Pages/Courses/DepartmentNamePageModel.cs 文件  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="e8850-114">上面的代码创建 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) 以包含系名称列表。</span><span class="sxs-lookup"><span data-stu-id="e8850-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="e8850-115">如果指定了 `selectedDepartment`，可在 `SelectList` 中选择该系。</span><span class="sxs-lookup"><span data-stu-id="e8850-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="e8850-116">“创建”和“编辑”页模型类将派生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e8850-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="e8850-117">更新课程“创建”页模型</span><span class="sxs-lookup"><span data-stu-id="e8850-117">Update the Course Create page model</span></span>

<span data-ttu-id="e8850-118">课程分配给院系。</span><span class="sxs-lookup"><span data-stu-id="e8850-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="e8850-119">“创建”和“编辑”页的基类提供 `SelectList`，用于选择院系。</span><span class="sxs-lookup"><span data-stu-id="e8850-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="e8850-120">采用 `SelectList` 的下拉列表设置 `Course.DepartmentID` 外键 (FK) 属性。</span><span class="sxs-lookup"><span data-stu-id="e8850-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="e8850-121">EF Core 使用 `Course.DepartmentID` FK 加载 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="e8850-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![创建课程](update-related-data/_static/ddl30.png)

<span data-ttu-id="e8850-123">使用以下代码更新 Pages/Courses/Create.cshtml.cs  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e8850-124">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e8850-124">The preceding code:</span></span>

* <span data-ttu-id="e8850-125">派生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e8850-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="e8850-126">使用 `TryUpdateModelAsync` 防止[过多发布](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="e8850-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e8850-127">删除 `ViewData["DepartmentID"]`。</span><span class="sxs-lookup"><span data-stu-id="e8850-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="e8850-128">基类中的 `DepartmentNameSL` 是强类型模型，将用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="e8850-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="e8850-129">建议使用强类型而非弱类型。</span><span class="sxs-lookup"><span data-stu-id="e8850-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="e8850-130">有关详细信息，请参阅[弱类型数据（ViewData 和 ViewBag）](xref:mvc/views/overview#VD_VB)。</span><span class="sxs-lookup"><span data-stu-id="e8850-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="e8850-131">更新“课程创建”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e8850-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="e8850-132">使用以下代码更新 Pages/Courses/Create.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="e8850-133">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e8850-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e8850-134">将标题从“DepartmentID”更改为“Department”   。</span><span class="sxs-lookup"><span data-stu-id="e8850-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e8850-135">将 `"ViewBag.DepartmentID"` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="e8850-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="e8850-136">添加“选择系”选项。</span><span class="sxs-lookup"><span data-stu-id="e8850-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="e8850-137">如果尚未选择院系（而不是已选中首个院系），此更改将在下拉列表中显示“选择院系”。</span><span class="sxs-lookup"><span data-stu-id="e8850-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="e8850-138">在未选择系时添加验证消息。</span><span class="sxs-lookup"><span data-stu-id="e8850-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="e8850-139">Razor 页面使用[选择标记帮助程序](xref:mvc/views/working-with-forms#the-select-tag-helper)：</span><span class="sxs-lookup"><span data-stu-id="e8850-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="e8850-140">测试“创建”页。</span><span class="sxs-lookup"><span data-stu-id="e8850-140">Test the Create page.</span></span> <span data-ttu-id="e8850-141">“创建”页显示系名称，而不是系 ID。</span><span class="sxs-lookup"><span data-stu-id="e8850-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="e8850-142">更新课程“编辑”页模型</span><span class="sxs-lookup"><span data-stu-id="e8850-142">Update the Course Edit page model</span></span>

<span data-ttu-id="e8850-143">使用以下代码更新 Pages/Courses/Edit.cshtml.cs  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="e8850-144">这些更改与在“创建”页模型中所做的更改相似。</span><span class="sxs-lookup"><span data-stu-id="e8850-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="e8850-145">在上面的代码中，`PopulateDepartmentsDropDownList` 在院系 ID 中传递并将在下拉列表中选择该院系。</span><span class="sxs-lookup"><span data-stu-id="e8850-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="e8850-146">更新“课程编辑”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e8850-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="e8850-147">使用以下代码更新 Pages/Courses/Edit.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="e8850-148">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e8850-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e8850-149">显示课程 ID。</span><span class="sxs-lookup"><span data-stu-id="e8850-149">Displays the course ID.</span></span> <span data-ttu-id="e8850-150">通常不显示实体的主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="e8850-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="e8850-151">PK 对用户不具有任何意义。</span><span class="sxs-lookup"><span data-stu-id="e8850-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="e8850-152">在这种情况下，PK 就是课程编号。</span><span class="sxs-lookup"><span data-stu-id="e8850-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="e8850-153">将“院系”下拉列表的标题从“DepartmentID”更改为“Department”   。</span><span class="sxs-lookup"><span data-stu-id="e8850-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e8850-154">将 `"ViewBag.DepartmentID"` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="e8850-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="e8850-155">该页面包含课程编号的隐藏域 (`<input type="hidden">`)。</span><span class="sxs-lookup"><span data-stu-id="e8850-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="e8850-156">添加具有 `asp-for="Course.CourseID"` 的 `<label>` 标记帮助器也同样需要隐藏域。</span><span class="sxs-lookup"><span data-stu-id="e8850-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="e8850-157">用户单击“保存”时，需要 `<input type="hidden">` ，以便在已发布的数据中包括课程编号。</span><span class="sxs-lookup"><span data-stu-id="e8850-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="e8850-158">更新课程“详细信息”和“删除”页</span><span class="sxs-lookup"><span data-stu-id="e8850-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="e8850-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 可以在不需要跟踪时提高性能。</span><span class="sxs-lookup"><span data-stu-id="e8850-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="e8850-160">更新“课程”页模型</span><span class="sxs-lookup"><span data-stu-id="e8850-160">Update the Course page models</span></span>

<span data-ttu-id="e8850-161">使用以下代码更新 Pages/Courses/Delete.cshtml.cs 以添加 `AsNoTracking` ：</span><span class="sxs-lookup"><span data-stu-id="e8850-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="e8850-162">在 Pages/Courses/Details.cshtml.cs 文件中进行相同的更改  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="e8850-163">更新“课程”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e8850-163">Update the Course Razor pages</span></span>

<span data-ttu-id="e8850-164">使用以下代码更新 Pages/Courses/Delete.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="e8850-165">对“详细信息”页执行相同更改。</span><span class="sxs-lookup"><span data-stu-id="e8850-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="e8850-166">测试“课程”页</span><span class="sxs-lookup"><span data-stu-id="e8850-166">Test the Course pages</span></span>

<span data-ttu-id="e8850-167">测试“创建”、“编辑”、“详细信息”和“删除”页面。</span><span class="sxs-lookup"><span data-stu-id="e8850-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="e8850-168">更新讲师“创建”和“编辑”页</span><span class="sxs-lookup"><span data-stu-id="e8850-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="e8850-169">讲师可能教授任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="e8850-170">下图显示包含一系列课程复选框的讲师“编辑”页。</span><span class="sxs-lookup"><span data-stu-id="e8850-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![带课程信息的讲师“编辑”页](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="e8850-172">通过复选框可对分配给讲师的课程进行更改。</span><span class="sxs-lookup"><span data-stu-id="e8850-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="e8850-173">数据库中的每一门课程均有对应显示的复选框。</span><span class="sxs-lookup"><span data-stu-id="e8850-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="e8850-174">已分配给讲师的课程将会被选中。</span><span class="sxs-lookup"><span data-stu-id="e8850-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="e8850-175">用户可以通过选择或清除复选框来更改课程分配。</span><span class="sxs-lookup"><span data-stu-id="e8850-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="e8850-176">如果课程数过多，另一个 UI 的使用效果可能更好。</span><span class="sxs-lookup"><span data-stu-id="e8850-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="e8850-177">但此处所示的用于管理多对多关系的方法不会发生变化。</span><span class="sxs-lookup"><span data-stu-id="e8850-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="e8850-178">若要创建或删除关系，则需要使用联接实体。</span><span class="sxs-lookup"><span data-stu-id="e8850-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="e8850-179">为已分配的课程数据创建类</span><span class="sxs-lookup"><span data-stu-id="e8850-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="e8850-180">使用以下代码创建 SchoolViewModels/AssignedCourseData.cs  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="e8850-181">`AssignedCourseData` 类包含的数据可用于为已分配给讲师的课程创建复选框。</span><span class="sxs-lookup"><span data-stu-id="e8850-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="e8850-182">创建“讲师”页模型基类</span><span class="sxs-lookup"><span data-stu-id="e8850-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="e8850-183">创建 Pages/Instructors/InstructorCoursesPageModel.cs 基类  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="e8850-184">`InstructorCoursesPageModel` 是将用于“编辑”和“创建”页模型的基类。</span><span class="sxs-lookup"><span data-stu-id="e8850-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="e8850-185">`PopulateAssignedCourseData` 读取所有 `Course` 实体以填充 `AssignedCourseDataList`。</span><span class="sxs-lookup"><span data-stu-id="e8850-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="e8850-186">该代码将设置每门课程的 `CourseID` 和标题，并决定是否为讲师分配该课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="e8850-187">[HashSet](/dotnet/api/system.collections.generic.hashset-1) 用于高效查找。</span><span class="sxs-lookup"><span data-stu-id="e8850-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="e8850-188">Razor 页面没有 Course 实体的集合，因此模型绑定器无法自动更新 `CourseAssignments` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="e8850-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="e8850-189">可在新的 `UpdateInstructorCourses` 方法中更新 `CourseAssignments` 导航属性，而不必使用模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="e8850-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="e8850-190">为此，需要从模型绑定中排除 `CourseAssignments` 属性。</span><span class="sxs-lookup"><span data-stu-id="e8850-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="e8850-191">此操作无需对调用 `TryUpdateModel` 的代码进行任何更改，因为使用的是允许列表重载，并且 `CourseAssignments` 不包括在该列表中。</span><span class="sxs-lookup"><span data-stu-id="e8850-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="e8850-192">如果未选中任何复选框，则 `UpdateInstructorCourses` 中的代码将使用空集合初始化 `CourseAssignments` 导航属性，并返回以下内容：</span><span class="sxs-lookup"><span data-stu-id="e8850-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="e8850-193">之后，代码会循环访问数据库中的所有课程，并逐一检查当前分配给讲师的课程和页面中处于选中状态的课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="e8850-194">为便于高效查找，后两个集合存储在 `HashSet` 对象中。</span><span class="sxs-lookup"><span data-stu-id="e8850-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="e8850-195">如果某课程的复选框处于选中状态，但该课程不在 `Instructor.CourseAssignments` 导航属性中，则会将该课程添加到导航属性中的集合中。</span><span class="sxs-lookup"><span data-stu-id="e8850-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="e8850-196">如果某课程的复选框未处于选中状态，但该课程存在 `Instructor.CourseAssignments` 导航属性中，则会从导航属性中删除该课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="e8850-197">处理办公室位置</span><span class="sxs-lookup"><span data-stu-id="e8850-197">Handle office location</span></span>

<span data-ttu-id="e8850-198">“编辑”页必须处理的另一个关系是 Instructor 实体与 `OfficeAssignment` 实体之间的一对零或一对一关系。</span><span class="sxs-lookup"><span data-stu-id="e8850-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="e8850-199">讲师编辑代码必须处理以下场景：</span><span class="sxs-lookup"><span data-stu-id="e8850-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="e8850-200">如果用户清除办公室分配，则需删除 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e8850-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e8850-201">如果用户输入办公室分配，且办公室分配原本为空，则需创建一个新 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e8850-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e8850-202">如果用户更改办公室分配，则需更新 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e8850-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="e8850-203">更新讲师“编辑”页模型</span><span class="sxs-lookup"><span data-stu-id="e8850-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="e8850-204">使用以下代码更新 Pages/Instructors/Edit.cshtml.cs  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="e8850-205">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e8850-205">The preceding code:</span></span>

* <span data-ttu-id="e8850-206">使用 `OfficeAssignment`、`CourseAssignment` 和 `CourseAssignment.Course` 导航属性的预先加载从数据库获取当前的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="e8850-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="e8850-207">用模型绑定器中的值更新检索到的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="e8850-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="e8850-208">`TryUpdateModel` 可防止[过多发布](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="e8850-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e8850-209">如果办公室位置为空，则将 `Instructor.OfficeAssignment` 设置为 null。</span><span class="sxs-lookup"><span data-stu-id="e8850-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="e8850-210">当 `Instructor.OfficeAssignment` 为 null 时，`OfficeAssignment` 表中的相关行将会删除。</span><span class="sxs-lookup"><span data-stu-id="e8850-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="e8850-211">调用 `OnGetAsync` 中的 `PopulateAssignedCourseData`，使用 `AssignedCourseData` 视图模型类为复选框提供信息。</span><span class="sxs-lookup"><span data-stu-id="e8850-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="e8850-212">调用 `OnPostAsync` 中的 `UpdateInstructorCourses`，将复选框中的信息应用于将要编辑的 Instructor 实体。</span><span class="sxs-lookup"><span data-stu-id="e8850-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="e8850-213">如果 `TryUpdateModel` 失败，则调用 `OnPostAsync` 中的 `PopulateAssignedCourseData` 和 `UpdateInstructorCourses`。</span><span class="sxs-lookup"><span data-stu-id="e8850-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="e8850-214">这些方法调用将在页面重新显示错误消息时还原页面上所输入的已分配课程数据。</span><span class="sxs-lookup"><span data-stu-id="e8850-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="e8850-215">更新“讲师编辑”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e8850-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="e8850-216">使用以下代码更新 Pages/Instructors/Edit.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="e8850-217">上面的代码将创建一个具有三列的 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="e8850-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="e8850-218">每列均具有一个复选框和包含课程编号及标题的标题。</span><span class="sxs-lookup"><span data-stu-id="e8850-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="e8850-219">所有复选框均具有相同的名称（“selectedCourses”）。</span><span class="sxs-lookup"><span data-stu-id="e8850-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="e8850-220">使用相同名称可指示模型绑定器将它们视为一个组。</span><span class="sxs-lookup"><span data-stu-id="e8850-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="e8850-221">每个复选框的值特性都设置为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="e8850-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="e8850-222">发布页面时，模型绑定器会传递一个数组，该数组只包括所选复选框的 `CourseID` 值。</span><span class="sxs-lookup"><span data-stu-id="e8850-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="e8850-223">初次呈现复选框时，分配给讲师的课程均已选中。</span><span class="sxs-lookup"><span data-stu-id="e8850-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="e8850-224">注意：此处所使用的编辑讲师课程数据的方法适用于数量有限的课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="e8850-225">对于规模远大于此的集合，则使用不同的 UI 和不同的更新方法会更实用和更高效。</span><span class="sxs-lookup"><span data-stu-id="e8850-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="e8850-226">运行应用并测试更新的讲师“编辑”页。</span><span class="sxs-lookup"><span data-stu-id="e8850-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="e8850-227">更改某些课程分配。</span><span class="sxs-lookup"><span data-stu-id="e8850-227">Change some course assignments.</span></span> <span data-ttu-id="e8850-228">这些更改将反映在“索引”页上。</span><span class="sxs-lookup"><span data-stu-id="e8850-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="e8850-229">更新讲师“创建”页</span><span class="sxs-lookup"><span data-stu-id="e8850-229">Update the Instructor Create page</span></span>

<span data-ttu-id="e8850-230">使用类似于“编辑”页面的代码更新“讲师创建”页面模型和 Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="e8850-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="e8850-231">测试讲师“创建”页。</span><span class="sxs-lookup"><span data-stu-id="e8850-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="e8850-232">更新讲师“删除”页</span><span class="sxs-lookup"><span data-stu-id="e8850-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="e8850-233">使用以下代码更新 Pages/Instructors/Delete.cshtml.cs  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="e8850-234">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e8850-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e8850-235">对 `CourseAssignments` 导航属性使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="e8850-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="e8850-236">必须包含 `CourseAssignments`，否则删除讲师时将不会删除课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="e8850-237">为避免阅读它们，可以在数据库中配置级联删除。</span><span class="sxs-lookup"><span data-stu-id="e8850-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="e8850-238">如果要删除的讲师被指派为任何系的管理员，则需从这些系中删除该讲师分配。</span><span class="sxs-lookup"><span data-stu-id="e8850-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="e8850-239">运行应用并测试“删除”页。</span><span class="sxs-lookup"><span data-stu-id="e8850-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e8850-240">后续步骤</span><span class="sxs-lookup"><span data-stu-id="e8850-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e8850-241">[上一个教程](xref:data/ef-rp/read-related-data)
> [下一个教程](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="e8850-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e8850-242">本教程演示如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="e8850-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="e8850-243">如果遇到无法解决的问题，请[下载或查看已完成的应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="e8850-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="e8850-244">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="e8850-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="e8850-245">下图显示了部分已完成页面。</span><span class="sxs-lookup"><span data-stu-id="e8850-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="e8850-246">![课程“编辑”页](update-related-data/_static/course-edit.png)
![讲师”编辑”页](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="e8850-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="e8850-247">查看并测试“创建”和“编辑”课程页。</span><span class="sxs-lookup"><span data-stu-id="e8850-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="e8850-248">创建新课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-248">Create a new course.</span></span> <span data-ttu-id="e8850-249">系按其主键（一个整数）进行选择，而不是按名称。</span><span class="sxs-lookup"><span data-stu-id="e8850-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="e8850-250">编辑新课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-250">Edit the new course.</span></span> <span data-ttu-id="e8850-251">完成测试后，请删除新课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="e8850-252">创建基类以共享通用代码</span><span class="sxs-lookup"><span data-stu-id="e8850-252">Create a base class to share common code</span></span>

<span data-ttu-id="e8850-253">“课程/创建”和“课程/编辑”页分别需要一个系名称列表。</span><span class="sxs-lookup"><span data-stu-id="e8850-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="e8850-254">针对“创建”和“编辑”页创建 Pages/Courses/DepartmentNamePageModel.cshtml.cs 基类  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="e8850-255">上面的代码创建 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) 以包含系名称列表。</span><span class="sxs-lookup"><span data-stu-id="e8850-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="e8850-256">如果指定了 `selectedDepartment`，可在 `SelectList` 中选择该系。</span><span class="sxs-lookup"><span data-stu-id="e8850-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="e8850-257">“创建”和“编辑”页模型类将派生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e8850-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="e8850-258">自定义课程页</span><span class="sxs-lookup"><span data-stu-id="e8850-258">Customize the Courses Pages</span></span>

<span data-ttu-id="e8850-259">创建新的课程实体时，新实体必须与现有院系有关系。</span><span class="sxs-lookup"><span data-stu-id="e8850-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="e8850-260">若要在创建课程的同时添加系，则“创建”和“编辑”的基类必须包含用于选择系的下拉列表。</span><span class="sxs-lookup"><span data-stu-id="e8850-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="e8850-261">该下拉列表设置 `Course.DepartmentID` 外键 (FK) 属性。</span><span class="sxs-lookup"><span data-stu-id="e8850-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="e8850-262">EF Core 使用 `Course.DepartmentID` FK 加载 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="e8850-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![创建课程](update-related-data/_static/ddl.png)

<span data-ttu-id="e8850-264">使用以下代码更新“创建”页模型：</span><span class="sxs-lookup"><span data-stu-id="e8850-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="e8850-265">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e8850-265">The preceding code:</span></span>

* <span data-ttu-id="e8850-266">派生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e8850-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="e8850-267">使用 `TryUpdateModelAsync` 防止[过多发布](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="e8850-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e8850-268">将 `ViewData["DepartmentID"]` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="e8850-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="e8850-269">`ViewData["DepartmentID"]` 将替换为强类型 `DepartmentNameSL`。</span><span class="sxs-lookup"><span data-stu-id="e8850-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="e8850-270">建议使用强类型而非弱类型。</span><span class="sxs-lookup"><span data-stu-id="e8850-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="e8850-271">有关详细信息，请参阅[弱类型数据（ViewData 和 ViewBag）](xref:mvc/views/overview#VD_VB)。</span><span class="sxs-lookup"><span data-stu-id="e8850-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="e8850-272">更新“课程创建”页</span><span class="sxs-lookup"><span data-stu-id="e8850-272">Update the Courses Create page</span></span>

<span data-ttu-id="e8850-273">使用以下代码更新 Pages/Courses/Create.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="e8850-274">上述标记进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e8850-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="e8850-275">将标题从“DepartmentID”更改为“Department”   。</span><span class="sxs-lookup"><span data-stu-id="e8850-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e8850-276">将 `"ViewBag.DepartmentID"` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="e8850-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="e8850-277">添加“选择系”选项。</span><span class="sxs-lookup"><span data-stu-id="e8850-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="e8850-278">此更改将导致呈现“选择系”而不是第一个系。</span><span class="sxs-lookup"><span data-stu-id="e8850-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="e8850-279">在未选择系时添加验证消息。</span><span class="sxs-lookup"><span data-stu-id="e8850-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="e8850-280">Razor 页面使用[选择标记帮助程序](xref:mvc/views/working-with-forms#the-select-tag-helper)：</span><span class="sxs-lookup"><span data-stu-id="e8850-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="e8850-281">测试“创建”页。</span><span class="sxs-lookup"><span data-stu-id="e8850-281">Test the Create page.</span></span> <span data-ttu-id="e8850-282">“创建”页显示系名称，而不是系 ID。</span><span class="sxs-lookup"><span data-stu-id="e8850-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="e8850-283">更新“课程编辑”页。</span><span class="sxs-lookup"><span data-stu-id="e8850-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="e8850-284">使用以下代码替换 Pages/Courses/Edit.cshtml.cs 中的代码  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="e8850-285">这些更改与在“创建”页模型中所做的更改相似。</span><span class="sxs-lookup"><span data-stu-id="e8850-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="e8850-286">在上面的代码中，`PopulateDepartmentsDropDownList` 在系 ID 中传递并将选择下拉列表中指定的系。</span><span class="sxs-lookup"><span data-stu-id="e8850-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="e8850-287">使用以下标记更新 Pages/Courses/Edit.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="e8850-288">上述标记进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e8850-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="e8850-289">显示课程 ID。</span><span class="sxs-lookup"><span data-stu-id="e8850-289">Displays the course ID.</span></span> <span data-ttu-id="e8850-290">通常不显示实体的主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="e8850-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="e8850-291">PK 对用户不具有任何意义。</span><span class="sxs-lookup"><span data-stu-id="e8850-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="e8850-292">在这种情况下，PK 就是课程编号。</span><span class="sxs-lookup"><span data-stu-id="e8850-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="e8850-293">将标题从“DepartmentID”更改为“Department”   。</span><span class="sxs-lookup"><span data-stu-id="e8850-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e8850-294">将 `"ViewBag.DepartmentID"` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="e8850-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="e8850-295">该页面包含课程编号的隐藏域 (`<input type="hidden">`)。</span><span class="sxs-lookup"><span data-stu-id="e8850-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="e8850-296">添加具有 `asp-for="Course.CourseID"` 的 `<label>` 标记帮助器也同样需要隐藏域。</span><span class="sxs-lookup"><span data-stu-id="e8850-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="e8850-297">用户单击“保存”时，需要 `<input type="hidden">` ，以便在已发布的数据中包括课程编号。</span><span class="sxs-lookup"><span data-stu-id="e8850-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="e8850-298">测试更新的代码。</span><span class="sxs-lookup"><span data-stu-id="e8850-298">Test the updated code.</span></span> <span data-ttu-id="e8850-299">创建、编辑和删除课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="e8850-300">将 AsNoTracking 添加到“详细信息”和“删除”页模型</span><span class="sxs-lookup"><span data-stu-id="e8850-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="e8850-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 可以在不需要跟踪时提高性能。</span><span class="sxs-lookup"><span data-stu-id="e8850-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="e8850-302">将 `AsNoTracking` 添加到“删除”和“详细信息”页模型。</span><span class="sxs-lookup"><span data-stu-id="e8850-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="e8850-303">下面的代码显示更新的“删除”页模型：</span><span class="sxs-lookup"><span data-stu-id="e8850-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="e8850-304">在 Pages/Courses/Details.cshtml.cs 文件中更新 `OnGetAsync` 方法  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="e8850-305">修改“删除”和“详细信息”页</span><span class="sxs-lookup"><span data-stu-id="e8850-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="e8850-306">使用以下标记更新“删除”Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="e8850-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="e8850-307">对“详细信息”页执行相同更改。</span><span class="sxs-lookup"><span data-stu-id="e8850-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="e8850-308">测试“课程”页</span><span class="sxs-lookup"><span data-stu-id="e8850-308">Test the Course pages</span></span>

<span data-ttu-id="e8850-309">测试“创建”、“编辑”、“详细信息”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="e8850-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="e8850-310">更新“讲师”页</span><span class="sxs-lookup"><span data-stu-id="e8850-310">Update the instructor pages</span></span>

<span data-ttu-id="e8850-311">以下各部分介绍更新“讲师”页。</span><span class="sxs-lookup"><span data-stu-id="e8850-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="e8850-312">添加办公室位置</span><span class="sxs-lookup"><span data-stu-id="e8850-312">Add office location</span></span>

<span data-ttu-id="e8850-313">编辑讲师记录时，可能希望更新讲师的办公室分配。</span><span class="sxs-lookup"><span data-stu-id="e8850-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="e8850-314">`Instructor` 实体与 `OfficeAssignment` 实体是一对零或一关系。</span><span class="sxs-lookup"><span data-stu-id="e8850-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="e8850-315">讲师代码必须处理：</span><span class="sxs-lookup"><span data-stu-id="e8850-315">The instructor code must handle:</span></span>

* <span data-ttu-id="e8850-316">如果用户清除办公室分配，则需删除 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e8850-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e8850-317">如果用户输入办公室分配，且办公室分配原本为空，则需创建一个新 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e8850-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e8850-318">如果用户更改办公室分配，则需更新 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e8850-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="e8850-319">使用以下代码更新讲师“编辑”页模型：</span><span class="sxs-lookup"><span data-stu-id="e8850-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="e8850-320">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e8850-320">The preceding code:</span></span>

* <span data-ttu-id="e8850-321">使用 `OfficeAssignment` 导航属性的预先加载从数据库获取当前的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="e8850-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="e8850-322">用模型绑定器中的值更新检索到的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="e8850-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="e8850-323">`TryUpdateModel` 可防止[过多发布](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="e8850-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e8850-324">如果办公室位置为空，则将 `Instructor.OfficeAssignment` 设置为 null。</span><span class="sxs-lookup"><span data-stu-id="e8850-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="e8850-325">当 `Instructor.OfficeAssignment` 为 null 时，`OfficeAssignment` 表中的相关行将会删除。</span><span class="sxs-lookup"><span data-stu-id="e8850-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="e8850-326">更新讲师“编辑”页</span><span class="sxs-lookup"><span data-stu-id="e8850-326">Update the instructor Edit page</span></span>

<span data-ttu-id="e8850-327">使用办公室位置更新 Pages/Instructors/Edit.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="e8850-328">验证是否可以更改讲师办公室位置。</span><span class="sxs-lookup"><span data-stu-id="e8850-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="e8850-329">向“讲师编辑”页添加课程分配</span><span class="sxs-lookup"><span data-stu-id="e8850-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="e8850-330">讲师可能教授任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="e8850-331">本部分将添加更改课程分配的功能。</span><span class="sxs-lookup"><span data-stu-id="e8850-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="e8850-332">下图显示更新的讲师“编辑”页：</span><span class="sxs-lookup"><span data-stu-id="e8850-332">The following image shows the updated instructor Edit page:</span></span>

![带课程信息的讲师“编辑”页](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="e8850-334">`Course` 和 `Instructor` 具有多对多关系。</span><span class="sxs-lookup"><span data-stu-id="e8850-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="e8850-335">若要添加和删除关系，可以从 `CourseAssignments` 联接实体集中添加和删除实体。</span><span class="sxs-lookup"><span data-stu-id="e8850-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="e8850-336">通过复选框可对分配给讲师的课程进行更改。</span><span class="sxs-lookup"><span data-stu-id="e8850-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="e8850-337">数据库中的每一门课程均有对应显示的复选框。</span><span class="sxs-lookup"><span data-stu-id="e8850-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="e8850-338">已分配给讲师的课程将会被勾选。</span><span class="sxs-lookup"><span data-stu-id="e8850-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="e8850-339">用户可以通过选择或清除复选框来更改课程分配。</span><span class="sxs-lookup"><span data-stu-id="e8850-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="e8850-340">如果课程数量过多：</span><span class="sxs-lookup"><span data-stu-id="e8850-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="e8850-341">可以使用其他用户界面显示课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="e8850-342">使用联接实体创建或删除关系的方法不会发生更改。</span><span class="sxs-lookup"><span data-stu-id="e8850-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="e8850-343">添加类以支持“创建”和“编辑”讲师页</span><span class="sxs-lookup"><span data-stu-id="e8850-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="e8850-344">使用以下代码创建 SchoolViewModels/AssignedCourseData.cs  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="e8850-345">`AssignedCourseData` 类包含的数据可用于为讲师已分配的课程创建复选框。</span><span class="sxs-lookup"><span data-stu-id="e8850-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="e8850-346">创建 Pages/Instructors/InstructorCoursesPageModel.cshtml.cs 基类  ：</span><span class="sxs-lookup"><span data-stu-id="e8850-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="e8850-347">`InstructorCoursesPageModel` 是将用于“编辑”和“创建”页模型的基类。</span><span class="sxs-lookup"><span data-stu-id="e8850-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="e8850-348">`PopulateAssignedCourseData` 读取所有 `Course` 实体以填充 `AssignedCourseDataList`。</span><span class="sxs-lookup"><span data-stu-id="e8850-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="e8850-349">该代码将设置每门课程的 `CourseID` 和标题，并决定是否为讲师分配该课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="e8850-350">[HashSet](/dotnet/api/system.collections.generic.hashset-1) 用于创建高效查找。</span><span class="sxs-lookup"><span data-stu-id="e8850-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="e8850-351">讲师“编辑”页模型</span><span class="sxs-lookup"><span data-stu-id="e8850-351">Instructors Edit page model</span></span>

<span data-ttu-id="e8850-352">使用以下代码更新讲师“编辑”页模型：</span><span class="sxs-lookup"><span data-stu-id="e8850-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="e8850-353">上面的代码处理办公室分配更改。</span><span class="sxs-lookup"><span data-stu-id="e8850-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="e8850-354">更新讲师 Razor 视图：</span><span class="sxs-lookup"><span data-stu-id="e8850-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="e8850-355">将代码粘贴到 Visual Studio 中时，换行符会发生更改，其更改方式会导致代码中断。</span><span class="sxs-lookup"><span data-stu-id="e8850-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="e8850-356">按 Ctrl+Z 一次可撤消自动格式设置。</span><span class="sxs-lookup"><span data-stu-id="e8850-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="e8850-357">按 Ctrl+Z 可以修复换行符，使其看起来如此处所示。</span><span class="sxs-lookup"><span data-stu-id="e8850-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="e8850-358">缩进不一定要呈现完美形式，但 `@:</tr><tr>`、`@:<td>`、`@:</td>` 和 `@:</tr>` 行必须各成一行（如下所示）。</span><span class="sxs-lookup"><span data-stu-id="e8850-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="e8850-359">选中新的代码块后，按 Tab 三次，使新代码与现有代码对齐。</span><span class="sxs-lookup"><span data-stu-id="e8850-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="e8850-360">[通过此链接](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html)投票或查看此 bug 的状态。</span><span class="sxs-lookup"><span data-stu-id="e8850-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="e8850-361">上面的代码将创建一个具有三列的 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="e8850-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="e8850-362">每列均具有一个复选框和包含课程编号及标题的标题。</span><span class="sxs-lookup"><span data-stu-id="e8850-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="e8850-363">所有复选框均具有相同的名称（“selectedCourses”）。</span><span class="sxs-lookup"><span data-stu-id="e8850-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="e8850-364">使用相同名称可指示模型绑定器将它们视为一个组。</span><span class="sxs-lookup"><span data-stu-id="e8850-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="e8850-365">每个复选框的值特性都设置为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="e8850-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="e8850-366">发布页面时，模型绑定器会传递一个数组，该数组只包括所选复选框的 `CourseID` 值。</span><span class="sxs-lookup"><span data-stu-id="e8850-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="e8850-367">初次呈现复选框时，分配给讲师的课程具有已勾选的特性。</span><span class="sxs-lookup"><span data-stu-id="e8850-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="e8850-368">运行应用并测试更新的讲师“编辑”页。</span><span class="sxs-lookup"><span data-stu-id="e8850-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="e8850-369">更改某些课程分配。</span><span class="sxs-lookup"><span data-stu-id="e8850-369">Change some course assignments.</span></span> <span data-ttu-id="e8850-370">这些更改将反映在“索引”页上。</span><span class="sxs-lookup"><span data-stu-id="e8850-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="e8850-371">注意：此处所使用的编辑讲师课程数据的方法适用于数量有限的课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="e8850-372">对于规模远大于此的集合，则使用不同的 UI 和不同的更新方法会更实用和更高效。</span><span class="sxs-lookup"><span data-stu-id="e8850-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="e8850-373">更新讲师“创建”页</span><span class="sxs-lookup"><span data-stu-id="e8850-373">Update the instructors Create page</span></span>

<span data-ttu-id="e8850-374">使用以下代码更新讲师“创建”页模型：</span><span class="sxs-lookup"><span data-stu-id="e8850-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="e8850-375">前面的代码与 Pages/Instructors/Edit.cshtml.cs 代码类似  。</span><span class="sxs-lookup"><span data-stu-id="e8850-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="e8850-376">使用以下标记更新“讲师创建”Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="e8850-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="e8850-377">测试讲师“创建”页。</span><span class="sxs-lookup"><span data-stu-id="e8850-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="e8850-378">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="e8850-378">Update the Delete page</span></span>

<span data-ttu-id="e8850-379">使用以下代码更新“删除”页模型：</span><span class="sxs-lookup"><span data-stu-id="e8850-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="e8850-380">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e8850-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e8850-381">对 `CourseAssignments` 导航属性使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="e8850-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="e8850-382">必须包含 `CourseAssignments`，否则删除讲师时将不会删除课程。</span><span class="sxs-lookup"><span data-stu-id="e8850-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="e8850-383">为避免阅读它们，可以在数据库中配置级联删除。</span><span class="sxs-lookup"><span data-stu-id="e8850-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="e8850-384">如果要删除的讲师被指派为任何系的管理员，则需从这些系中删除该讲师分配。</span><span class="sxs-lookup"><span data-stu-id="e8850-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8850-385">其他资源</span><span class="sxs-lookup"><span data-stu-id="e8850-385">Additional resources</span></span>

* [<span data-ttu-id="e8850-386">本教程的 YouTube 版本（第 1 部分）</span><span class="sxs-lookup"><span data-stu-id="e8850-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="e8850-387">本教程的 YouTube 版本（第 2 部分）</span><span class="sxs-lookup"><span data-stu-id="e8850-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="e8850-388">[上一页](xref:data/ef-rp/read-related-data)
> [下一页](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="e8850-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
