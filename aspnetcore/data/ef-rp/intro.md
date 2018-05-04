---
title: Razor sayfalarının Entity Framework Çekirdek ASP.NET Core - 8'in Öğreticisi 1 ile
author: rick-anderson
description: Entity Framework Çekirdek kullanarak Razor sayfalarının uygulamasının nasıl oluşturulacağını gösterir
manager: wpickett
ms.author: riande
ms.date: 11/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-rp/intro
ms.openlocfilehash: 99a8d158c896566c2f6e6c22e4b37b1956e21cbf
ms.sourcegitcommit: 5130b3034165f5cf49d829fe7475a84aa33d2693
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/03/2018
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="64acf-103">Razor sayfalarının Entity Framework Çekirdek ASP.NET Core - 8'in Öğreticisi 1 ile</span><span class="sxs-lookup"><span data-stu-id="64acf-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="64acf-104">Tarafından [zel Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="64acf-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="64acf-105">Contoso University örnek web uygulaması Entity Framework (EF) çekirdek 2.0 ve Visual Studio 2017 kullanarak ASP.NET Core 2.0 MVC web uygulamalarının nasıl oluşturulacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="64acf-105">The Contoso University sample web app demonstrates how to create ASP.NET Core 2.0 MVC web applications using Entity Framework (EF) Core 2.0 and Visual Studio 2017.</span></span>

<span data-ttu-id="64acf-106">Örnek uygulaması, kurgusal bir Contoso üniversite için bir web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="64acf-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="64acf-107">Öğrenci giriş, indirmelere oluşturma ve Eğitmen atamaları gibi işlevselliği içerir.</span><span class="sxs-lookup"><span data-stu-id="64acf-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="64acf-108">Contoso University örnek uygulamasının nasıl oluşturulacağını açıklayan eğitim serileri ilk sayfasıdır.</span><span class="sxs-lookup"><span data-stu-id="64acf-108">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="64acf-109">İndirme veya tamamlanan uygulama görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="64acf-109">Download or view the completed app.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="64acf-110">[Yükleme yönergeleri](xref:tutorials/index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="64acf-110">[Download instructions](xref:tutorials/index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="64acf-111">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="64acf-111">Prerequisites</span></span>

[!INCLUDE [](~/includes/net-core-prereqs.md)]

<span data-ttu-id="64acf-112">Aşina [Razor sayfalarının](xref:mvc/razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="64acf-112">Familiarity with [Razor Pages](xref:mvc/razor-pages/index).</span></span> <span data-ttu-id="64acf-113">Yeni programcıları tamamlamanız gereken [Razor sayfalarının ile çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) bu seri başlatmadan önce.</span><span class="sxs-lookup"><span data-stu-id="64acf-113">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="64acf-114">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="64acf-114">Troubleshooting</span></span>

<span data-ttu-id="64acf-115">Olamaz çözmek bir sorunla çalıştırırsanız, genellikle çözümün kodunuzu karşılaştırarak bulabilirsiniz [tamamlandı aşama](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots).</span><span class="sxs-lookup"><span data-stu-id="64acf-115">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed stage](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots).</span></span> <span data-ttu-id="64acf-116">Sık karşılaşılan hataları ve bunları çözmek nasıl listesi için bkz: [serideki son Öğreticisi sorun giderme bölümüne](xref:data/ef-mvc/advanced#common-errors).</span><span class="sxs-lookup"><span data-stu-id="64acf-116">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](xref:data/ef-mvc/advanced#common-errors).</span></span> <span data-ttu-id="64acf-117">Var. gerekenler bulamazsanız, bir soru nakledebilirsiniz [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) için [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF çekirdek](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="64acf-117">If you don't find what you need there, you can post a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="64acf-118">Bu öğreticiler dizi önceki eğitimlerine bitti üzerinde oluşturur.</span><span class="sxs-lookup"><span data-stu-id="64acf-118">This series of tutorials builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="64acf-119">Her başarılı öğretici tamamlandıktan sonra projeyi bir kopyasını kaydetme göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="64acf-119">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="64acf-120">Sorunlarla karşılaşırsanız, başlangıcına geri dönerseniz yerine önceki öğreticiden üzerinden başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="64acf-120">If you run into problems, you can start over from the previous tutorial instead of going back to the beginning.</span></span> <span data-ttu-id="64acf-121">Alternatif olarak, karşıdan yükleyebileceğiniz bir [tamamlandı aşama](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots) ve tamamlanmış aşama kullanarak üzerinden başlatın.</span><span class="sxs-lookup"><span data-stu-id="64acf-121">Alternatively, you can download a [completed stage](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots) and start over using the completed stage.</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="64acf-122">Contoso University web uygulaması</span><span class="sxs-lookup"><span data-stu-id="64acf-122">The Contoso University web app</span></span>

<span data-ttu-id="64acf-123">Bu öğreticiler oluşturulmuş uygulamaya bir temel university web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="64acf-123">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="64acf-124">Kullanıcıların görüntüleyebileceği ve Öğrenci, indirmelere ve Eğitmen bilgileri güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="64acf-124">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="64acf-125">Öğreticide oluşturulan ekranlar bazılarını aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="64acf-125">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler dizin sayfası](intro/_static/students-index.png)

![Öğrenciler Düzenle sayfası](intro/_static/student-edit.png)

<span data-ttu-id="64acf-128">Bu sitenin UI Stili yerleşik şablonlar tarafından oluşturulan yakın ' dir.</span><span class="sxs-lookup"><span data-stu-id="64acf-128">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="64acf-129">Razor sayfalarının, UI EF çekirdek üzerinde öğretici odak noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="64acf-129">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="64acf-130">Bir Razor sayfalarının web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="64acf-130">Create a Razor Pages web app</span></span>

* <span data-ttu-id="64acf-131">Visual Studio'dan **dosya** menüsünde, select **yeni** > **proje**.</span><span class="sxs-lookup"><span data-stu-id="64acf-131">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="64acf-132">Yeni bir ASP.NET çekirdek Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="64acf-132">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="64acf-133">Proje adı **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="64acf-133">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="64acf-134">Proje adı önemlidir *ContosoUniversity* kod kopyalanıp yapıştırılmış ad alanları eşleştirmek için.</span><span class="sxs-lookup"><span data-stu-id="64acf-134">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
 <span data-ttu-id="64acf-135">![Yeni ASP.NET çekirdek Web uygulaması](intro/_static/np.png)</span><span class="sxs-lookup"><span data-stu-id="64acf-135">![new ASP.NET Core Web Application](intro/_static/np.png)</span></span>
* <span data-ttu-id="64acf-136">Seçin **ASP.NET Core 2.0** açılır ve ardından **Web uygulaması**.</span><span class="sxs-lookup"><span data-stu-id="64acf-136">Select **ASP.NET Core 2.0** in the dropdown, and then select **Web Application**.</span></span>
 <span data-ttu-id="64acf-137">![Web uygulaması (Razor sayfalarının)](../../mvc/razor-pages/index/_static/np2.png)</span><span class="sxs-lookup"><span data-stu-id="64acf-137">![Web Application (Razor Pages)](../../mvc/razor-pages/index/_static/np2.png)</span></span>

<span data-ttu-id="64acf-138">Tuşuna **F5** uygulamayı hata ayıklama modunda çalıştırmak için veya **Ctrl-F5** hata ayıklayıcı eklemeden çalıştırmak için</span><span class="sxs-lookup"><span data-stu-id="64acf-138">Press **F5** to run the app in debug mode or **Ctrl-F5** to run without attaching the debugger</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="64acf-139">Site stil ayarlama</span><span class="sxs-lookup"><span data-stu-id="64acf-139">Set up the site style</span></span>

<span data-ttu-id="64acf-140">Birkaç değişiklik sitesi menüsü, Düzen ve giriş sayfası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="64acf-140">A few changes set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="64acf-141">Açık *Pages/_Layout.cshtml* ve aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="64acf-141">Open *Pages/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="64acf-142">"Contoso University." "ContosoUniversity" her oluşumu değiştirme</span><span class="sxs-lookup"><span data-stu-id="64acf-142">Change each occurrence of "ContosoUniversity" to "Contoso University."</span></span> <span data-ttu-id="64acf-143">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="64acf-143">There are three occurrences.</span></span>

* <span data-ttu-id="64acf-144">Menü girişlerini eklemek **Öğrenciler**, **kurslar**, **Eğitmen**, ve **Departmanlar**ve silme **kişi** menü girişi.</span><span class="sxs-lookup"><span data-stu-id="64acf-144">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="64acf-145">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="64acf-145">The changes are highlighted.</span></span> <span data-ttu-id="64acf-146">(Tüm biçimlendirme *değil* görüntülenir.)</span><span class="sxs-lookup"><span data-stu-id="64acf-146">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu/Pages/_Layout.cshtml?highlight=6,29,35-38,47&range=1-50)]

<span data-ttu-id="64acf-147">İçinde *Pages/Index.cshtml*, dosyanın içeriğini bu uygulama hakkında metinle ASP.NET MVC hakkında metni değiştirmek için aşağıdaki kod ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="64acf-147">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu/Pages/Index.cshtml)]

<span data-ttu-id="64acf-148">Projeyi çalıştırmak için CTRL + F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="64acf-148">Press CTRL+F5 to run the project.</span></span> <span data-ttu-id="64acf-149">Giriş sayfası aşağıdaki öğreticilerde oluşturulan sekmelerle görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="64acf-149">The home page is displayed with tabs created in the following tutorials:</span></span>

![Contoso University giriş sayfası](intro/_static/home-page.png)

## <a name="create-the-data-model"></a><span data-ttu-id="64acf-151">Veri modeli oluşturma</span><span class="sxs-lookup"><span data-stu-id="64acf-151">Create the data model</span></span>

<span data-ttu-id="64acf-152">Sınıflar Contoso University uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="64acf-152">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="64acf-153">Aşağıdaki üç varlıklarıyla başlatın:</span><span class="sxs-lookup"><span data-stu-id="64acf-153">Start with the following three entities:</span></span>

![İndirmelere kayıt Öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="64acf-155">Bir-çok ilişkisi arasında `Student` ve `Enrollment` varlıklar.</span><span class="sxs-lookup"><span data-stu-id="64acf-155">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="64acf-156">Bir-çok ilişkisi arasında `Course` ve `Enrollment` varlıklar.</span><span class="sxs-lookup"><span data-stu-id="64acf-156">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="64acf-157">Öğrencinin herhangi bir sayıda kurslar kaydedebilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-157">A student can enroll in any number of courses.</span></span> <span data-ttu-id="64acf-158">Bir indirmelere içinde kayıtlı Öğrenciler herhangi bir sayıda olabilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-158">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="64acf-159">Aşağıdaki bölümlerde, bu varlıkların her biri için bir sınıf oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="64acf-159">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="64acf-160">Öğrenci varlık</span><span class="sxs-lookup"><span data-stu-id="64acf-160">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

<span data-ttu-id="64acf-162">Oluşturma bir *modelleri* klasör.</span><span class="sxs-lookup"><span data-stu-id="64acf-162">Create a *Models* folder.</span></span> <span data-ttu-id="64acf-163">İçinde *modelleri* klasörünü adlı bir sınıf dosyası oluşturma *Student.cs* aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="64acf-163">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="64acf-164">`ID` Özelliği bu sınıfa karşılık gelen veritabanı (DB) tablosunun birincil anahtar sütunu haline gelir.</span><span class="sxs-lookup"><span data-stu-id="64acf-164">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="64acf-165">Varsayılan olarak, EF çekirdek adlı bir özellik yorumlar `ID` veya `classnameID` birincil anahtar olarak.</span><span class="sxs-lookup"><span data-stu-id="64acf-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="64acf-166">İçinde `classnameID`, `classname` sınıfın adı olduğu gibi `Student` önceki örnekte.</span><span class="sxs-lookup"><span data-stu-id="64acf-166">In `classnameID`, `classname` is the name of the class, such as `Student` in the preceding example.</span></span>

<span data-ttu-id="64acf-167">`Enrollments` Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="64acf-167">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="64acf-168">Bu varlığa ilgili diğer varlıklar için Gezinti özellikleri bağlayın.</span><span class="sxs-lookup"><span data-stu-id="64acf-168">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="64acf-169">Bu durumda, `Enrollments` özelliği bir `Student entity` tüm tutan `Enrollment` için ilişkili olan varlıkların `Student`.</span><span class="sxs-lookup"><span data-stu-id="64acf-169">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="64acf-170">Örneğin, bir öğrenci satır varsa DB iki kayıt, ilişkili satırları sahip `Enrollments` gezinti özelliği içerdiğinden bu iki `Enrollment` varlıklar.</span><span class="sxs-lookup"><span data-stu-id="64acf-170">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="64acf-171">İlgili `Enrollment` satırıdır bu öğrencinin birincil anahtar değerini içeren bir satır `StudentID` sütun.</span><span class="sxs-lookup"><span data-stu-id="64acf-171">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="64acf-172">Örneğin, Öğrenci kimlikli varsayalım = 1 sahip iki satır `Enrollment` tablo.</span><span class="sxs-lookup"><span data-stu-id="64acf-172">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="64acf-173">`Enrollment` Tablolu sahip iki satır `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="64acf-173">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="64acf-174">`StudentID` bir yabancı anahtar `Enrollment` Öğrenci belirtir tablo `Student` tablo.</span><span class="sxs-lookup"><span data-stu-id="64acf-174">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="64acf-175">Bir gezinme özelliği birden çok varlık tutarsanız gezinti özelliği bir liste türü gibi olmalıdır `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="64acf-175">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="64acf-176">`ICollection<T>` belirtilebilir, ya da bir türü `List<T>` veya `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="64acf-176">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="64acf-177">Zaman `ICollection<T>` olduğu EF çekirdek oluşturur kullanıldığında, bir `HashSet<T>` varsayılan koleksiyon.</span><span class="sxs-lookup"><span data-stu-id="64acf-177">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="64acf-178">Birden çok varlık tutun Gezinti özellikleri çok- ve -çok ilişkileri gelir.</span><span class="sxs-lookup"><span data-stu-id="64acf-178">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="64acf-179">Kayıt varlık</span><span class="sxs-lookup"><span data-stu-id="64acf-179">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="64acf-181">İçinde *modelleri* klasörü oluşturmak *Enrollment.cs* aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="64acf-181">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="64acf-182">`EnrollmentID` Birincil anahtarı bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="64acf-182">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="64acf-183">Bu varlığı kullanan `classnameID` yerine desen `ID` gibi `Student` varlık.</span><span class="sxs-lookup"><span data-stu-id="64acf-183">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="64acf-184">Genellikle geliştiriciler bir deseni seçin ve veri modelini kullanır.</span><span class="sxs-lookup"><span data-stu-id="64acf-184">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="64acf-185">Sonraki öğreticide, veri modelinde devralma uygulamak daha kolay classname Kimliğini kullanarak gösterilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-185">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="64acf-186">`Grade` Özelliği bir `enum`.</span><span class="sxs-lookup"><span data-stu-id="64acf-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="64acf-187">Soru işareti sonra `Grade` türü bildirimi gösterir `Grade` özelliği boş değer atanabilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="64acf-188">Null bir düzeyde bir sıfır ataması farklı.--null anlamına gelir bir düzeyde bilinen değil veya henüz atanmamış.</span><span class="sxs-lookup"><span data-stu-id="64acf-188">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="64acf-189">`StudentID` Özelliği bir yabancı anahtar ve karşılık gelen gezinme özelliğini `Student`.</span><span class="sxs-lookup"><span data-stu-id="64acf-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="64acf-190">Bir `Enrollment` varlıktır biriyle ilişkili `Student` tek bir özelliği içerecek şekilde varlık `Student` varlık.</span><span class="sxs-lookup"><span data-stu-id="64acf-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="64acf-191">`Student` Varlık farklı olarak `Student.Enrollments` birden çok içeren gezinti özelliği `Enrollment` varlıklar.</span><span class="sxs-lookup"><span data-stu-id="64acf-191">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="64acf-192">`CourseID` Özelliği bir yabancı anahtar ve karşılık gelen gezinme özelliğini `Course`.</span><span class="sxs-lookup"><span data-stu-id="64acf-192">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="64acf-193">Bir `Enrollment` varlıktır biriyle ilişkili `Course` varlık.</span><span class="sxs-lookup"><span data-stu-id="64acf-193">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="64acf-194">EF çekirdek, ise bu özellik yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="64acf-194">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="64acf-195">Örneğin,`StudentID` için `Student` gezinti özelliği, bu yana `Student` varlığın birincil anahtarının `ID`.</span><span class="sxs-lookup"><span data-stu-id="64acf-195">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="64acf-196">Yabancı anahtar özellikleri de adlı `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="64acf-196">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="64acf-197">Örneğin, `CourseID` beri `Course` varlığın birincil anahtarının `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="64acf-197">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="64acf-198">İndirmelere varlık</span><span class="sxs-lookup"><span data-stu-id="64acf-198">The Course entity</span></span>

![İndirmelere varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="64acf-200">İçinde *modelleri* klasörü oluşturmak *Course.cs* aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="64acf-200">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="64acf-201">`Enrollments` Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="64acf-201">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="64acf-202">A `Course` varlık herhangi bir sayıda için ilgili olabileceğini `Enrollment` varlıklar.</span><span class="sxs-lookup"><span data-stu-id="64acf-202">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="64acf-203">`DatabaseGenerated` DB sahip, oluşturmak, yerine özniteliği birincil anahtarı belirtmek uygulama izin verir.</span><span class="sxs-lookup"><span data-stu-id="64acf-203">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="create-the-schoolcontext-db-context"></a><span data-ttu-id="64acf-204">SchoolContext DB bağlamı oluşturma</span><span class="sxs-lookup"><span data-stu-id="64acf-204">Create the SchoolContext DB context</span></span>

<span data-ttu-id="64acf-205">Verilen veri modeli için EF temel işlevleri koordinatları ana sınıfı DB bağlamı sınıftır.</span><span class="sxs-lookup"><span data-stu-id="64acf-205">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="64acf-206">Veri bağlamı türetilir `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="64acf-206">The data context is derived from `Microsoft.EntityFrameworkCore.DbContext`.</span></span> <span data-ttu-id="64acf-207">Veri bağlamı hangi varlıkların veri modelinde dahil edildiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="64acf-207">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="64acf-208">Bu projede adlı sınıfı `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="64acf-208">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="64acf-209">Proje klasöründe adlı bir klasör oluşturun *veri*.</span><span class="sxs-lookup"><span data-stu-id="64acf-209">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="64acf-210">İçinde *veri* klasör oluşturma *SchoolContext.cs* aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="64acf-210">In the *Data* folder create *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="64acf-211">Bu kod oluşturur bir `DbSet` özelliği her bir varlık kümesi.</span><span class="sxs-lookup"><span data-stu-id="64acf-211">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="64acf-212">EF çekirdek terminolojisinde:</span><span class="sxs-lookup"><span data-stu-id="64acf-212">In EF Core terminology:</span></span>

* <span data-ttu-id="64acf-213">Bir varlık kümesine genellikle DB tabloya karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="64acf-213">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="64acf-214">Bir varlık tablosunda bir satırı karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="64acf-214">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="64acf-215">`DbSet<Enrollment>` ve `DbSet<Course>` atlanabilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-215">`DbSet<Enrollment>` and `DbSet<Course>` can be omitted.</span></span> <span data-ttu-id="64acf-216">EF çekirdek içerir bunları örtük olarak çünkü `Student` varlık başvuruları `Enrollment` varlık ve `Enrollment` varlık başvuruları `Course` varlık.</span><span class="sxs-lookup"><span data-stu-id="64acf-216">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="64acf-217">Bu öğretici için tutmak `DbSet<Enrollment>` ve `DbSet<Course>` içinde `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="64acf-217">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

<span data-ttu-id="64acf-218">DB oluşturulduğunda EF çekirdek aynı adlara sahip tablolar oluşturur `DbSet` özellik adları.</span><span class="sxs-lookup"><span data-stu-id="64acf-218">When the DB is created, EF Core creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="64acf-219">Koleksiyonlar için özellik adları genellikle çoğul (Öğrenciler yerine Öğrenci).</span><span class="sxs-lookup"><span data-stu-id="64acf-219">Property names for collections are typically plural (Students rather than Student).</span></span> <span data-ttu-id="64acf-220">Geliştiriciler olup tablo adlarının çoğul olmalıdır hakkında katılmıyorum.</span><span class="sxs-lookup"><span data-stu-id="64acf-220">Developers disagree about whether table names should be plural.</span></span> <span data-ttu-id="64acf-221">Bu öğreticileri için DbContext tekil tablo adları belirterek varsayılan davranışı geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="64acf-221">For these tutorials, the default behavior is overridden by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="64acf-222">Tekil tablo adları belirtmek için aşağıdaki vurgulanmış kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="64acf-222">To specify singular table names, add the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-context-with-dependency-injection"></a><span data-ttu-id="64acf-223">Bağlam bağımlılık ekleme ile kaydetme</span><span class="sxs-lookup"><span data-stu-id="64acf-223">Register the context with dependency injection</span></span>

<span data-ttu-id="64acf-224">ASP.NET Core içeren [bağımlılık ekleme](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="64acf-224">ASP.NET Core includes [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="64acf-225">Hizmetleri (örneğin, EF çekirdek DB bağlamı), uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-225">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="64acf-226">Bu Hizmetleri (örneğin, Razor sayfalarının) gerektiren bileşenler bu hizmetlere Oluşturucu parametreleri yoluyla sağlanır.</span><span class="sxs-lookup"><span data-stu-id="64acf-226">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="64acf-227">Bir db bağlamı örneği alır Oluşturucusu kodu daha sonra öğreticide gösterilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-227">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="64acf-228">Kaydetmek için `SchoolContext` bir hizmet olarak açmak *haline*, vurgulanan satırlar ekleyin `ConfigureServices` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="64acf-228">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

<span data-ttu-id="64acf-229">Bağlantı dizesinin adını bağlamına üzerinde bir yöntemini çağırarak geçirilen bir `DbContextOptionsBuilder` nesnesi.</span><span class="sxs-lookup"><span data-stu-id="64acf-229">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="64acf-230">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) bağlantı dizesinden okur *appsettings.json* dosya.</span><span class="sxs-lookup"><span data-stu-id="64acf-230">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="64acf-231">Ekleme `using` deyimleri için `ContosoUniversity.Data` ve `Microsoft.EntityFrameworkCore` ad alanları.</span><span class="sxs-lookup"><span data-stu-id="64acf-231">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces.</span></span> <span data-ttu-id="64acf-232">Projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="64acf-232">Build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="64acf-233">Açık *appsettings.json* dosyası ve bir bağlantı dizesi aşağıdaki kodda gösterildiği gibi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="64acf-233">Open the *appsettings.json* file and add a connection string as shown in the following code:</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

<span data-ttu-id="64acf-234">Önceki bağlantı dizesini kullanır `ConnectRetryCount=0` önlemek için [SQLClient](/dotnet/framework/data/adonet/ef/sqlclient-for-the-entity-framework) asılı gelen.</span><span class="sxs-lookup"><span data-stu-id="64acf-234">The preceding connection string uses `ConnectRetryCount=0` to prevent [SQLClient](/dotnet/framework/data/adonet/ef/sqlclient-for-the-entity-framework) from hanging.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="64acf-235">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="64acf-235">SQL Server Express LocalDB</span></span>

<span data-ttu-id="64acf-236">Bağlantı dizesini bir SQL Server yerel veritabanı DB belirtir.</span><span class="sxs-lookup"><span data-stu-id="64acf-236">The connection string specifies a SQL Server LocalDB DB.</span></span> <span data-ttu-id="64acf-237">Yerel veritabanı, SQL Server Express veritabanı Motoru'nu hafif sürümüdür ve uygulama geliştirme, üretim kullanımı için amaçlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="64acf-237">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="64acf-238">Yerel veritabanı isteğe bağlı olarak başlar ve bu yüzden karmaşık yapılandırma kullanıcı modunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="64acf-238">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="64acf-239">Varsayılan olarak, yerel veritabanı oluşturur *.mdf* DB dosyaları `C:/Users/<user>` dizin.</span><span class="sxs-lookup"><span data-stu-id="64acf-239">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="64acf-240">DB test verileri ile başlatmak için kod ekleme</span><span class="sxs-lookup"><span data-stu-id="64acf-240">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="64acf-241">EF çekirdek boş bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="64acf-241">EF Core creates an empty DB.</span></span> <span data-ttu-id="64acf-242">Bu bölümde, bir *çekirdek* yöntemi test veri ile doldurulacak yazılır.</span><span class="sxs-lookup"><span data-stu-id="64acf-242">In this section, a *Seed* method is written to populate it with test data.</span></span>

<span data-ttu-id="64acf-243">İçinde *veri* klasörünü adlı yeni bir sınıf dosyası oluşturma *DbInitializer.cs* ve aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="64acf-243">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="64acf-244">Kod DB'de herhangi Öğrenciler olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="64acf-244">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="64acf-245">DB içinde hiçbir Öğrenciler varsa, DB test verilerle sağlanmış.</span><span class="sxs-lookup"><span data-stu-id="64acf-245">If there are no students in the DB, the DB is seeded with test data.</span></span> <span data-ttu-id="64acf-246">Diziye test verileri yükler yerine `List<T>` performansı iyileştirmek için koleksiyonları.</span><span class="sxs-lookup"><span data-stu-id="64acf-246">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="64acf-247">`EnsureCreated` Yöntemi DB DB bağlamı için otomatik olarak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="64acf-247">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="64acf-248">Veritabanı varsa `EnsureCreated` DB değiştirmeden döndürür.</span><span class="sxs-lookup"><span data-stu-id="64acf-248">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="64acf-249">İçinde *Program.cs*, değişiklik `Main` yöntemi aşağıdakileri yapın:</span><span class="sxs-lookup"><span data-stu-id="64acf-249">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="64acf-250">Bir DB bağlamı örneği bağımlılık ekleme kapsayıcıdan alın.</span><span class="sxs-lookup"><span data-stu-id="64acf-250">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="64acf-251">İçin bağlamı geçirme çekirdek yöntemini çağırın.</span><span class="sxs-lookup"><span data-stu-id="64acf-251">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="64acf-252">Seed yöntemi tamamlandığında bağlamını siler.</span><span class="sxs-lookup"><span data-stu-id="64acf-252">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="64acf-253">Aşağıdaki kod güncelleştirilmiş gösterir *Program.cs* dosya.</span><span class="sxs-lookup"><span data-stu-id="64acf-253">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu/ProgramOriginal.cs?name=snippet)]

<span data-ttu-id="64acf-254">Uygulama bir ilk çalıştırıldığında DB oluşturulur ve test verilerle sağlanmış.</span><span class="sxs-lookup"><span data-stu-id="64acf-254">The first time the app is run, the DB is created and seeded with test data.</span></span> <span data-ttu-id="64acf-255">Veri modeli güncelleştirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="64acf-255">When the data model is updated:</span></span>
* <span data-ttu-id="64acf-256">DB silin.</span><span class="sxs-lookup"><span data-stu-id="64acf-256">Delete the DB.</span></span>
* <span data-ttu-id="64acf-257">Seed yöntemi güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="64acf-257">Update the seed method.</span></span>
* <span data-ttu-id="64acf-258">Uygulamayı çalıştırın ve yeni bir kapsanan veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="64acf-258">Run the app and a new seeded DB is created.</span></span> 

<span data-ttu-id="64acf-259">Sonraki öğreticileri, veri değişiklikleri, silme ve DB yeniden oluşturma modeli DB güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-259">In later tutorials, the DB is updated when the data model changes, without deleting and re-creating the DB.</span></span>

<a name="pmc"></a>
## <a name="add-scaffold-tooling"></a><span data-ttu-id="64acf-260">İskele araçları Ekle</span><span class="sxs-lookup"><span data-stu-id="64acf-260">Add scaffold tooling</span></span>

<span data-ttu-id="64acf-261">Bu bölümde, Visual Studio web kod oluşturma paketi eklemek için Paket Yöneticisi Konsolu (PMC) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="64acf-261">In this section, the Package Manager Console (PMC) is used to add the Visual Studio web code generation package.</span></span> <span data-ttu-id="64acf-262">Bu paket, yapı iskelesi altyapısı çalıştırmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="64acf-262">This package is required to run the scaffolding engine.</span></span>

<span data-ttu-id="64acf-263">Gelen **Araçları** menüsünde, select **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu**.</span><span class="sxs-lookup"><span data-stu-id="64acf-263">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

<span data-ttu-id="64acf-264">Paket Yöneticisi Konsolu (PMC)'da, aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="64acf-264">In the Package Manager Console (PMC), enter the following commands:</span></span>

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Utils
```

<span data-ttu-id="64acf-265">Önceki komutu NuGet paketlerini \*.csproj dosyasına ekler:</span><span class="sxs-lookup"><span data-stu-id="64acf-265">The previous command adds the NuGet packages to the \*.csproj file:</span></span>

[!code-csharp[](intro/samples/cu/ContosoUniversity1_csproj.txt?highlight=7-8)]

<a name="scaffold"></a>
## <a name="scaffold-the-model"></a><span data-ttu-id="64acf-266">İskele modeli</span><span class="sxs-lookup"><span data-stu-id="64acf-266">Scaffold the model</span></span>

* <span data-ttu-id="64acf-267">Proje dizininde bir komut penceresi açın (içeren dizine *Program.cs*, *haline*, ve *.csproj* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="64acf-267">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="64acf-268">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="64acf-268">Run the following commands:</span></span>


 ```console
dotnet restore
dotnet aspnet-codegenerator razorpage -m Student -dc SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
 ```

<span data-ttu-id="64acf-269">Hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="64acf-269">If you get the error:</span></span>
  ```
No executable found matching command "dotnet-aspnet-codegenerator"
  ```

<span data-ttu-id="64acf-270">Proje dizininde bir komut penceresi açın (içeren dizine *Program.cs*, *haline*, ve *.csproj* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="64acf-270">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>


<span data-ttu-id="64acf-271">Projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="64acf-271">Build the project.</span></span> <span data-ttu-id="64acf-272">Derleme hataları aşağıdaki gibi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="64acf-272">The build generates errors like the following:</span></span>

 `1>Pages\Students\Index.cshtml.cs(26,38,26,45): error CS1061: 'SchoolContext' does not contain a definition for 'Student'`

 <span data-ttu-id="64acf-273">Genel olarak değiştirmek `_context.Student` için `_context.Students` ("s" eklemek diğer bir deyişle, `Student`).</span><span class="sxs-lookup"><span data-stu-id="64acf-273">Globally change `_context.Student` to `_context.Students` (that is, add an "s" to `Student`).</span></span> <span data-ttu-id="64acf-274">7 oluşumu bulundu ve güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="64acf-274">7 occurrences are found and updated.</span></span> <span data-ttu-id="64acf-275">Düzeltme umuyoruz [bu hatayı](https://github.com/aspnet/Scaffolding/issues/633) sonraki sürümdeki.</span><span class="sxs-lookup"><span data-stu-id="64acf-275">We hope to fix [this bug](https://github.com/aspnet/Scaffolding/issues/633) in the next release.</span></span>

[!INCLUDE [model4tbl](../../includes/RP/model4tbl.md)]

 <a name="test"></a>
### <a name="test-the-app"></a><span data-ttu-id="64acf-276">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="64acf-276">Test the app</span></span>

<span data-ttu-id="64acf-277">Uygulamayı çalıştırın ve seçin **Öğrenciler** bağlantı.</span><span class="sxs-lookup"><span data-stu-id="64acf-277">Run the app and select the **Students** link.</span></span> <span data-ttu-id="64acf-278">Tarayıcı genişliği bağlı olarak **Öğrenciler** bağlantı sayfanın en üstünde görünür.</span><span class="sxs-lookup"><span data-stu-id="64acf-278">Depending on the browser width, the **Students** link appears at the top of the page.</span></span> <span data-ttu-id="64acf-279">Varsa **Öğrenciler** bağlantı görünür değilse, sağ üst köşesinde Gezinti simgeyi tıklatın.</span><span class="sxs-lookup"><span data-stu-id="64acf-279">If the **Students** link isn't visible, click the navigation icon in the upper right corner.</span></span>

![Contoso University giriş sayfası dar](intro/_static/home-page-narrow.png)

<span data-ttu-id="64acf-281">Test **oluşturma**, **Düzenle**, ve **ayrıntıları** bağlantılar.</span><span class="sxs-lookup"><span data-stu-id="64acf-281">Test the **Create**, **Edit**, and **Details** links.</span></span>

## <a name="view-the-db"></a><span data-ttu-id="64acf-282">DB görüntüleyin</span><span class="sxs-lookup"><span data-stu-id="64acf-282">View the DB</span></span>

<span data-ttu-id="64acf-283">Uygulama başlatıldığında `DbInitializer.Initialize` çağrıları `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="64acf-283">When the app is started, `DbInitializer.Initialize` calls `EnsureCreated`.</span></span> <span data-ttu-id="64acf-284">`EnsureCreated` DB var ve bir gerekirse oluşturur olmadığını algılar.</span><span class="sxs-lookup"><span data-stu-id="64acf-284">`EnsureCreated` detects if the DB exists, and creates one if necessary.</span></span> <span data-ttu-id="64acf-285">DB içinde hiçbir Öğrenciler varsa `Initialize` yöntemi Öğrenciler ekler.</span><span class="sxs-lookup"><span data-stu-id="64acf-285">If there are no Students in the DB, the `Initialize` method adds students.</span></span>

<span data-ttu-id="64acf-286">Açık **SQL Server Nesne Gezgini** (SSOX) gelen **Görünüm** Visual Studio menüsünde.</span><span class="sxs-lookup"><span data-stu-id="64acf-286">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="64acf-287">SSOX içinde tıklatın **(localdb) \MSSQLLocalDB > veritabanları > ContosoUniversity1**.</span><span class="sxs-lookup"><span data-stu-id="64acf-287">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > ContosoUniversity1**.</span></span>

<span data-ttu-id="64acf-288">Genişletme **tabloları** düğümü.</span><span class="sxs-lookup"><span data-stu-id="64acf-288">Expand the **Tables** node.</span></span>

<span data-ttu-id="64acf-289">Sağ **Öğrenci** tablosu ve'ı tıklatın **görünüm verilerini** oluşturulan sütunlar ve tabloya eklenen satır görmek için.</span><span class="sxs-lookup"><span data-stu-id="64acf-289">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

<span data-ttu-id="64acf-290"><em>.Mdf</em> ve <em>.ldf</em> DB dosyalarıdır içinde <em>C:\Users\\ <yourusername> </em> klasör.</span><span class="sxs-lookup"><span data-stu-id="64acf-290">The <em>.mdf</em> and <em>.ldf</em> DB files are in the <em>C:\Users\\<yourusername></em> folder.</span></span>

<span data-ttu-id="64acf-291">`EnsureCreated` Aşağıdaki iş akışı sağlayan uygulama Başlat'çağrılır:</span><span class="sxs-lookup"><span data-stu-id="64acf-291">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="64acf-292">DB silin.</span><span class="sxs-lookup"><span data-stu-id="64acf-292">Delete the DB.</span></span>
* <span data-ttu-id="64acf-293">DB şeması değiştirin (örneğin, bir `EmailAddress` alan).</span><span class="sxs-lookup"><span data-stu-id="64acf-293">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="64acf-294">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="64acf-294">Run the app.</span></span>

<span data-ttu-id="64acf-295">`EnsureCreated` bir DB ile oluşturur`EmailAddress` sütun.</span><span class="sxs-lookup"><span data-stu-id="64acf-295">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

## <a name="conventions"></a><span data-ttu-id="64acf-296">Kurallar</span><span class="sxs-lookup"><span data-stu-id="64acf-296">Conventions</span></span>

<span data-ttu-id="64acf-297">EF tam bir veritabanı oluşturmak çekirdek için sırayla yazılan kod miktarını kuralları veya EF çekirdek yapar varsayımlar kullanımı nedeniyle düzeydedir.</span><span class="sxs-lookup"><span data-stu-id="64acf-297">The amount of code written in order for EF Core to create a complete DB is minimal because of the use of conventions, or assumptions that EF Core makes.</span></span>

* <span data-ttu-id="64acf-298">Adlarını `DbSet` özellikleri Tablo adları olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="64acf-298">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="64acf-299">Tarafından başvurulan olmayan varlıklar için bir `DbSet` özelliği, varlık sınıfı adları tablo adları olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="64acf-299">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="64acf-300">Varlık özellik adlarını sütun adları için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="64acf-300">Entity property names are used for column names.</span></span>

* <span data-ttu-id="64acf-301">Kimliği veya classnameID adlı varlık özellikleri birincil anahtar özelliği kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-301">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="64acf-302">Bu adlı bir özelliği bir yabancı anahtar özellik olarak yorumlanır *<navigation property name> <primary key property name>* (örneğin, `StudentID` için `Student` gezinti özelliği bu yana `Student` varlığın birincil anahtar `ID`).</span><span class="sxs-lookup"><span data-stu-id="64acf-302">A property is interpreted as a foreign key property if it's named *<navigation property name><primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="64acf-303">Yabancı anahtar özellikleri adlı *<primary key property name>* (örneğin, `EnrollmentID` beri `Enrollment` varlığın birincil anahtarının `EnrollmentID`).</span><span class="sxs-lookup"><span data-stu-id="64acf-303">Foreign key properties can be named *<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="64acf-304">Geleneksel davranışı geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-304">Conventional behavior can be overridden.</span></span> <span data-ttu-id="64acf-305">Örneğin, tablo adlarının açıkça, bu öğreticide daha önce gösterildiği gibi belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-305">For example, the table names can be explicitly specified, as shown earlier in this tutorial.</span></span> <span data-ttu-id="64acf-306">Sütun adlarını açıkça ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="64acf-306">The column names can be explicitly set.</span></span> <span data-ttu-id="64acf-307">Birincil anahtarları ve yabancı anahtarları açıkça ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-307">Primary keys and foreign keys can be explicitly set.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="64acf-308">Zaman uyumsuz kodu</span><span class="sxs-lookup"><span data-stu-id="64acf-308">Asynchronous code</span></span>

<span data-ttu-id="64acf-309">Zaman uyumsuz programlama ASP.NET Core ve EF çekirdek için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="64acf-309">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="64acf-310">Bir web sunucusu kullanılabilir iş parçacıklarının sınırlı sayıda sahiptir ve yüksek yük durumlarda tüm kullanılabilir iş parçacıklarının kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-310">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="64acf-311">Bu durum oluştuğunda sunucu yukarı serbest iş parçacığı kadar yeni istekleri işleyemiyor.</span><span class="sxs-lookup"><span data-stu-id="64acf-311">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="64acf-312">G/ç tamamlamak bekliyorsunuz çünkü bunlar herhangi bir iş gerçekte yapmamanız sırasında zaman uyumlu koduyla birçok iş parçacığı bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="64acf-312">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="64acf-313">Bir işlemin g/ç tamamlanması beklenirken zaman uyumsuz koduyla diğer istekleri işlemek için kullanılacak sunucuyu kaydınızı kendi iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="64acf-313">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="64acf-314">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarını daha verimli bir şekilde kullanılmak üzere etkinleştirir ve sunucu gecikme olmadan daha fazla trafik işlemek için etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="64acf-314">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="64acf-315">Zaman uyumsuz kod yükünü az miktarda çalışma zamanında tanıtır.</span><span class="sxs-lookup"><span data-stu-id="64acf-315">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="64acf-316">Trafiğin düşük durumlar için performans isabet yüksek trafik durumlarda düşünülerek, çalışırken, olası performans geliştirmesi önemli.</span><span class="sxs-lookup"><span data-stu-id="64acf-316">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="64acf-317">Aşağıdaki kodda, `async` anahtar sözcüğü, `Task<T>` dönüş değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi zaman uyumsuz yürütme kodu olun.</span><span class="sxs-lookup"><span data-stu-id="64acf-317">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="64acf-318">`async` Anahtar sözcüğü derleyiciye bildirir:</span><span class="sxs-lookup"><span data-stu-id="64acf-318">The `async` keyword tells the compiler to:</span></span>

  * <span data-ttu-id="64acf-319">Geri aramalar yöntemi gövde bölümlerinin oluşturur.</span><span class="sxs-lookup"><span data-stu-id="64acf-319">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="64acf-320">Otomatik olarak oluştur [görev](/dotnet/api/system.threading.tasks.task?view=netframework-4.7) döndürülen nesne.</span><span class="sxs-lookup"><span data-stu-id="64acf-320">Automatically create the [Task](/dotnet/api/system.threading.tasks.task?view=netframework-4.7) object that's returned.</span></span> <span data-ttu-id="64acf-321">Daha fazla bilgi için bkz: [görev dönüş türü](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="64acf-321">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="64acf-322">Örtük dönüş türü `Task` devam eden iş temsil eder.</span><span class="sxs-lookup"><span data-stu-id="64acf-322">The implicit return type `Task` represents ongoing work.</span></span>

* <span data-ttu-id="64acf-323">`await` Anahtar sözcüğü yöntemi iki parçalara bölmek derleyici neden olur.</span><span class="sxs-lookup"><span data-stu-id="64acf-323">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="64acf-324">İlk bölümü ile zaman uyumsuz olarak başlatıldığında işlemi sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="64acf-324">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="64acf-325">İkinci bölümü işlem tamamlandığında çağrılan bir geri çağırma yöntemi konur.</span><span class="sxs-lookup"><span data-stu-id="64acf-325">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="64acf-326">`ToListAsync` zaman uyumsuz sürümü `ToList` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="64acf-326">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="64acf-327">EF çekirdek kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı noktalar:</span><span class="sxs-lookup"><span data-stu-id="64acf-327">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="64acf-328">Sorguları ya da Veritabanına gönderilen komutları neden deyimleri zaman uyumsuz olarak çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="64acf-328">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="64acf-329">İçeren, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, ve `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="64acf-329">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="64acf-330">Yalnızca değiştirme deyimleri içermeyen bir `IQueryable`, gibi `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="64acf-330">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="64acf-331">İş parçacığı içinde korumalı bir EF çekirdek bağlamı değil: paralel birden çok işlemleri yapmak denemeyin.</span><span class="sxs-lookup"><span data-stu-id="64acf-331">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span> 

* <span data-ttu-id="64acf-332">Zaman uyumsuz kod performans yararlarını yararlanmak için DB sorguları göndermek EF çekirdek yöntemleri çağırırsanız paketleri kitaplığı (disk belleği ettirilmesi gibi) zaman uyumsuz kullandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="64acf-332">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="64acf-333">. NET'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz: [zaman uyumsuz genel bakış](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="64acf-333">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

<span data-ttu-id="64acf-334">Sonraki öğreticide, temel CRUD (Oluştur, oku, Güncelleştir, Sil) işlemleri incelenmesini.</span><span class="sxs-lookup"><span data-stu-id="64acf-334">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="64acf-335">Next</span><span class="sxs-lookup"><span data-stu-id="64acf-335">Next</span></span>](xref:data/ef-rp/crud)
