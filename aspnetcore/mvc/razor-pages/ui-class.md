---
title: Yeniden kullanılabilir Razor kullanıcı Arabiriminde sınıf kitaplıkları ile ASP.NET çekirdek
author: Rick-Anderson
description: Bir sınıf kitaplığı'nda yeniden kullanılabilir Razor kullanıcı Arabirimi oluşturma açıklanmaktadır.
manager: wpickett
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 4/31/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: advanced
uid: mvc/razor-pages/ui-class
ms.openlocfilehash: 731d37a8f4983b18ded114f05470f8a408deb7cd
ms.sourcegitcommit: 5130b3034165f5cf49d829fe7475a84aa33d2693
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/03/2018
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="cac04-103">Yeniden kullanılabilir kullanıcı Arabirimi kullanarak ASP.NET Core Razor sınıf kitaplığı projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cac04-103">Create reusable UI using the Razor Class Library project in ASP.NET Core.</span></span>

<span data-ttu-id="cac04-104">tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cac04-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cac04-105">Razor görünümleri, sayfalar, denetleyicileri, sayfa modelleri ve veri modelleri Razor sınıfı Library(RCL) oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="cac04-105">Razor views, pages, controllers, page models, and data models can be built into a Razor Class Library(RCL).</span></span> <span data-ttu-id="cac04-106">RCL olabilir ve paketlenmiş ve yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cac04-106">The RCL can be and packaged and reused.</span></span> <span data-ttu-id="cac04-107">Uygulamalar görünümler ve içerdiği sayfaları geçersiz kılmak ve RCL içerir.</span><span class="sxs-lookup"><span data-stu-id="cac04-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="cac04-108">Ne zaman görünümü, kısmi görünümü veya Razor sayfasını web uygulaması ve Razor biçimlendirme RCL bulunduğunda (*.cshtml* dosyası) web uygulaması önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="cac04-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="cac04-109">Bu özellik gerektirir [!INCLUDE[](~/includes/2.1-SDK.md)]</span><span class="sxs-lookup"><span data-stu-id="cac04-109">This feature requires [!INCLUDE[](~/includes/2.1-SDK.md)]</span></span>

[!INCLUDE[](~/includes/2.1.md)]

<span data-ttu-id="cac04-110">[Görüntülemek veya karşıdan örnek kod](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/ui-class/samples) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cac04-110">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/ui-class/samples) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="cac04-111">Razor UI içeren bir sınıf kitaplığı oluşturun</span><span class="sxs-lookup"><span data-stu-id="cac04-111">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cac04-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac04-112">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cac04-113">Visual Studio'dan **dosya** menüsünde, select **yeni** > **proje**.</span><span class="sxs-lookup"><span data-stu-id="cac04-113">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="cac04-114">Seçin **ASP.NET Core Web uygulaması**.</span><span class="sxs-lookup"><span data-stu-id="cac04-114">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="cac04-115">Doğrulama **ASP.NET Core 2.1** veya daha sonra seçilir.</span><span class="sxs-lookup"><span data-stu-id="cac04-115">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="cac04-116">Seçin **Razor sınıf kitaplığı** > **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="cac04-116">Select **Razor Class Library** > **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="cac04-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac04-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cac04-118">Komut satırı ' çalıştırma `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="cac04-118">From the commandline, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="cac04-119">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="cac04-119">For example:</span></span>

``` CLI
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="cac04-120">Daha fazla bilgi için bkz: [dotnet yeni](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="cac04-120">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span>

------
<span data-ttu-id="cac04-121">Razor dosyaları RCL ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cac04-121">Add Razor files to the RCL.</span></span>

<span data-ttu-id="cac04-122">İçinde içerik Git RCL öneririz *alanları* klasör.</span><span class="sxs-lookup"><span data-stu-id="cac04-122">We recommend RCL content go in the *Areas* folder.</span></span> 


## <a name="referencing-razor-class-library-content"></a><span data-ttu-id="cac04-123">Razor sınıf kitaplığı içerik başvurma</span><span class="sxs-lookup"><span data-stu-id="cac04-123">Referencing Razor Class Library content</span></span>

<span data-ttu-id="cac04-124">RCL tarafından başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="cac04-124">The RCL can be referenced by:</span></span>

* <span data-ttu-id="cac04-125">NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="cac04-125">NuGet package.</span></span> <span data-ttu-id="cac04-126">Bkz: [oluşturma NuGet paketlerini](/nuget/create-packages/creating-a-package) ve [dotnet paket ekleme](/dotnet/core/tools/dotnet-add-package) ve [oluşturma ve bir NuGet Paketi Yayımlama](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="cac04-126">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="cac04-127">*{ProjectName} .csproj*.</span><span class="sxs-lookup"><span data-stu-id="cac04-127">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="cac04-128">Bkz: [dotnet-Başvuru Ekle](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="cac04-128">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

### <a name="partial-files-access-in-the-rcl"></a><span data-ttu-id="cac04-129">RCL kısmi dosya erişimi</span><span class="sxs-lookup"><span data-stu-id="cac04-129">Partial files access in the RCL</span></span>

<span data-ttu-id="cac04-130">RCL dışında içerik için ASP.NET çekirdeği çalışma zamanı RCL kısmi dosyalarında aramaz.</span><span class="sxs-lookup"><span data-stu-id="cac04-130">For content outside the RCL, the ASP.NET Core runtime does not search for partial files in the RCL.</span></span>

<span data-ttu-id="cac04-131">Örneğin, örnek indirme içinde *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* kısmi görüntüleyebilecek **değil** başvuruda bulunulamıyor *WebApp1\Pages\About.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="cac04-131">For example, in the sample download, the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view can **not** be referenced in *WebApp1\Pages\About.cshtml*.</span></span> <span data-ttu-id="cac04-132">Ancak, RCL sayfaları ( *RazorUIClassLib /* **için** erişim *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="cac04-132">However, pages in the RCL ( *RazorUIClassLib/* **can** access *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

## <a name="walkthrough-create-a-razor-class-library-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="cac04-133">İzlenecek yol: bir Razor sınıf kitaplığı proje oluşturma ve Razor sayfalarının projeden kullanma</span><span class="sxs-lookup"><span data-stu-id="cac04-133">Walkthrough: Create a Razor Class Library project and use from a Razor Pages project</span></span>

<span data-ttu-id="cac04-134">İndirebilirsiniz [tam projesini](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/ui-class/samples) ve yerine bunu oluşturmayı sınayın.</span><span class="sxs-lookup"><span data-stu-id="cac04-134">You can download the [complete project](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="cac04-135">Örnek indirme ek kod ve projeyi test kolaylaştırmak bağlantılar içerir.</span><span class="sxs-lookup"><span data-stu-id="cac04-135">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="cac04-136">Geri bildirim bırakabilirsiniz [bu GitHub sorunu](https://github.com/aspnet/Docs/issues/6098) yorumlarınızı adım adım yönergeler karşı indirme örnekleri üzerinde ile.</span><span class="sxs-lookup"><span data-stu-id="cac04-136">You can leave feedback in [this GitHub issue](https://github.com/aspnet/Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="cac04-137">İndirme uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="cac04-137">Test the download app</span></span>

<span data-ttu-id="cac04-138">Tamamlanan uygulama indirilen henüz ve gözden geçirme proje yerine oluşturacak, geçin [sonraki bölümde](#create-a-razor-class-library).</span><span class="sxs-lookup"><span data-stu-id="cac04-138">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-a-razor-class-library).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cac04-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac04-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cac04-140">Açık *.sln* dosyasını Visual Studio'da.</span><span class="sxs-lookup"><span data-stu-id="cac04-140">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="cac04-141">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="cac04-141">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="cac04-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac04-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cac04-143">Bir komut isteminden *CLI* dizin RCL oluşturmak ve web uygulaması.</span><span class="sxs-lookup"><span data-stu-id="cac04-143">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

``` CLI
dotnet build
```

<span data-ttu-id="cac04-144">Taşıma *WebApp1* dizin ve uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="cac04-144">Move to the *WebApp1* directory and run the app:</span></span>

``` CLI
dotnet run
```
------

<span data-ttu-id="cac04-145">' Ndaki yönergeleri izleyin [Test WebApp1](#test)</span><span class="sxs-lookup"><span data-stu-id="cac04-145">Follow the instructions in [Test WebApp1](#test)</span></span>

## <a name="create-a-razor-class-library"></a><span data-ttu-id="cac04-146">Bir Razor sınıf kitaplığı oluşturun</span><span class="sxs-lookup"><span data-stu-id="cac04-146">Create a Razor Class Library</span></span>

<span data-ttu-id="cac04-147">Bu bölümde, bir Razor sınıf kitaplığı (RCL) oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="cac04-147">In this section, a Razor Class Library (RCL) is created.</span></span> <span data-ttu-id="cac04-148">Razor dosyaları RCL eklenir.</span><span class="sxs-lookup"><span data-stu-id="cac04-148">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cac04-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac04-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cac04-150">RCL projesi oluşturun:</span><span class="sxs-lookup"><span data-stu-id="cac04-150">Create the RCL project:</span></span>

* <span data-ttu-id="cac04-151">Visual Studio'dan **dosya** menüsünde, select **yeni** > **proje**.</span><span class="sxs-lookup"><span data-stu-id="cac04-151">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="cac04-152">Seçin **ASP.NET Core Web uygulaması**.</span><span class="sxs-lookup"><span data-stu-id="cac04-152">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="cac04-153">Uygulama adı **RazorUIClassLib**.</span><span class="sxs-lookup"><span data-stu-id="cac04-153">Name the app **RazorUIClassLib**.</span></span>
* <span data-ttu-id="cac04-154">Doğrulama **ASP.NET Core 2.1** veya daha sonra seçilir.</span><span class="sxs-lookup"><span data-stu-id="cac04-154">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="cac04-155">Seçin **Razor sınıf kitaplığı** > **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="cac04-155">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="cac04-156">Razor sayfalarının web uygulaması oluşturun:</span><span class="sxs-lookup"><span data-stu-id="cac04-156">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="cac04-157">Gelen **Çözüm Gezgini**, çözüme sağ tıklayın > **Ekle** >  **yeni proje**.</span><span class="sxs-lookup"><span data-stu-id="cac04-157">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="cac04-158">Seçin **ASP.NET Core Web uygulaması**.</span><span class="sxs-lookup"><span data-stu-id="cac04-158">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="cac04-159">Uygulama adı **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="cac04-159">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="cac04-160">Doğrulama **ASP.NET Core 2.1** veya daha sonra seçilir.</span><span class="sxs-lookup"><span data-stu-id="cac04-160">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="cac04-161">Seçin **Web uygulaması** > **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="cac04-161">Select **Web Application** > **OK**.</span></span>

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="cac04-162">Razor dosyaları ve klasörleri projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cac04-162">Add Razor files and folders to the project.</span></span>

* <span data-ttu-id="cac04-163">Adlı bir Razor kısmi görünüm dosya eklemek *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="cac04-163">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>
* <span data-ttu-id="cac04-164">Biçimlendirme Değiştir *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="cac04-164">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

[!code-html[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="cac04-165">Kopya *_ViewStart.cshtml* WebApp1 proje dosyasından *RazorUIClassLib/Areas/MyFeature/Pages/_ViewStart.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="cac04-165">Copy the *_ViewStart.cshtml* file from the WebApp1 project to  *RazorUIClassLib/Areas/MyFeature/Pages/_ViewStart.cshtml*.</span></span>

  <span data-ttu-id="cac04-166">[Viewstart](xref:mvc/views/layout#running-code-before-each-view) dosya Razor sayfalarının proje düzeni kullanmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="cac04-166">The [viewstart](xref:mvc/views/layout#running-code-before-each-view) file is required to use the layout of the Razor Pages project.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="cac04-167">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac04-167">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cac04-168">Komut satırından aşağıdakileri çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="cac04-168">From the command line, run the following:</span></span>

``` CLI
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="cac04-169">Yukarıdaki komutlar:</span><span class="sxs-lookup"><span data-stu-id="cac04-169">The preceding commands:</span></span>

* <span data-ttu-id="cac04-170">Oluşturur `RazorUIClassLib` Razor sınıf kitaplığı (RCL).</span><span class="sxs-lookup"><span data-stu-id="cac04-170">Creates the `RazorUIClassLib` Razor Class Library (RCL).</span></span>
* <span data-ttu-id="cac04-171">Bir Razor _Message sayfası oluşturur ve RCL ekler.</span><span class="sxs-lookup"><span data-stu-id="cac04-171">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="cac04-172">`-np` Parametresi oluşturur sayfa olmadan bir `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="cac04-172">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="cac04-173">Oluşturur bir [viewstart](xref:mvc/views/layout#running-code-before-each-view) dosya ve RCL ekler.</span><span class="sxs-lookup"><span data-stu-id="cac04-173">Creates a [viewstart](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="cac04-174">Viewstart dosyası (sonraki bölümde eklenir) Razor sayfalarının projesi düzenini kullanmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="cac04-174">The viewstart file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

<span data-ttu-id="cac04-175">Razor sayfalarının güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cac04-175">Update the Razor Pages:</span></span>

* <span data-ttu-id="cac04-176">Biçimlendirme Değiştir *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="cac04-176">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

[!code-html[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="cac04-177">Biçimlendirme Değiştir *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="cac04-177">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

[!code-html[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

<span data-ttu-id="cac04-178">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` Kısmi görünümü kullanmak için gereklidir (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="cac04-178">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="cac04-179">Dahil olmak üzere yerine `@addTagHelper` yönergesi ekleyebileceğiniz bir *_viewımports.cshtml* dosya.</span><span class="sxs-lookup"><span data-stu-id="cac04-179">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="cac04-180">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="cac04-180">For example:</span></span>

``` CLI
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="cac04-181">Viewimports hakkında daha fazla bilgi için bkz: [paylaşılan yönergeleri alma](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="cac04-181">For more information on viewimports, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="cac04-182">Derleyici hatası olmadığını doğrulamak için sınıf kitaplığı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="cac04-182">Build the class library to verify there are no compiler errors:</span></span>

``` CLI
dotnet build RazorUIClassLib
```

<span data-ttu-id="cac04-183">Derleme çıktı içeren *RazorUIClassLib.dll* ve *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="cac04-183">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="cac04-184">*RazorUIClassLib.Views.dll* derlenmiş Razor içeriği içerir.</span><span class="sxs-lookup"><span data-stu-id="cac04-184">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

------

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="cac04-185">Bir Razor sayfalarının projeden Razor kullanıcı Arabirimi kitaplığı kullanma</span><span class="sxs-lookup"><span data-stu-id="cac04-185">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cac04-186">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac04-186">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cac04-187">Gelen **Çözüm Gezgini**, sağ tıklayın **WebApp1** seçip **başlangıç projesi olarak ayarla**.</span><span class="sxs-lookup"><span data-stu-id="cac04-187">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="cac04-188">Gelen **Çözüm Gezgini**, sağ tıklayın **WebApp1** seçip **derleme bağımlılıkları** > **proje bağımlılıkları**.</span><span class="sxs-lookup"><span data-stu-id="cac04-188">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="cac04-189">Denetleme **RazorUIClassLib** bir bağımlılık olarak **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="cac04-189">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="cac04-190">Gelen **Çözüm Gezgini**, sağ tıklayın **WebApp1** seçip **Ekle** > **başvuru**.</span><span class="sxs-lookup"><span data-stu-id="cac04-190">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="cac04-191">İçinde **başvuru Yöneticisi** iletişim kutusunda, onay **RazorUIClassLib** > **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="cac04-191">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="cac04-192">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="cac04-192">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="cac04-193">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac04-193">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cac04-194">Razor sayfalarının web app ve Razor sayfalarının uygulama ve Razor sınıf kitaplığı içeren bir çözüm dosyasını oluşturun:</span><span class="sxs-lookup"><span data-stu-id="cac04-194">Create a Razor Pages web app and a solution file containing the Razor Pages app and the Razor Class Library:</span></span>

``` CLI
dotnet new razor -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="cac04-195">Derleme ve web uygulaması çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="cac04-195">Build and run the web app:</span></span>

``` CLI
cd WebApp1
dotnet run
```

------

<a name="test"></a>

### <a name="test-webapp1"></a><span data-ttu-id="cac04-196">Test WebApp1</span><span class="sxs-lookup"><span data-stu-id="cac04-196">Test WebApp1</span></span>

<span data-ttu-id="cac04-197">Razor UI sınıf kitaplığı kullanılan doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="cac04-197">Verify the Razor UI class library is being used.</span></span>

* <span data-ttu-id="cac04-198">Gözat `/MyFeature/Page1`.</span><span class="sxs-lookup"><span data-stu-id="cac04-198">Browse to `/MyFeature/Page1`.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="cac04-199">Görünümleri, kısmi görünümleri ve sayfaları geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="cac04-199">Override views, partial views, and pages</span></span>

<span data-ttu-id="cac04-200">Ne zaman görünümü, kısmi görünümü veya Razor sayfasını bulundu hem web uygulamasını hem de Razor sınıf kitaplığı, Razor biçimlendirme (*.cshtml* dosyası) web uygulaması önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="cac04-200">When a view, partial view, or Razor Page is found in both the web app and the Razor Class Library, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="cac04-201">Örneğin, ekleyin *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* WebApp1 için ve WebApp1 Page1 sürer öncelik Page1in Razor sınıf kitaplığı.</span><span class="sxs-lookup"><span data-stu-id="cac04-201">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1in the Razor Class Library.</span></span>

<span data-ttu-id="cac04-202">Örnek indirme yeniden adlandırma *alanları/WebApp1/MyFeature2* için *alanları/WebApp1/MyFeature* öncelik test etmek için.</span><span class="sxs-lookup"><span data-stu-id="cac04-202">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="cac04-203">Kopya *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* kısmi görünüme *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="cac04-203">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="cac04-204">Yeni konumu belirtmek için biçimlendirme güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="cac04-204">Update the markup to indicate the new location.</span></span> <span data-ttu-id="cac04-205">Derleme ve kısmi uygulamanın sürümü kullanılan doğrulamak üzere uygulamasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="cac04-205">Build and run the app to verify the app's version of the partial is being used.</span></span>
