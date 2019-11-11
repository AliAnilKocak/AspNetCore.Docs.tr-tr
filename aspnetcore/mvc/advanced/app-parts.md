---
title: ASP.NET Core içindeki uygulama bölümleri
author: rick-anderson
description: ASP.NET Core içindeki uygulama bölümleriyle denetleyicileri, görünümü, Razor Pages ve daha fazlasını paylaşma
ms.author: riande
ms.date: 11/10/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 92c408adfad8af3dfd2572b625ae28ef24f64948
ms.sourcegitcommit: a7bbe3890befead19440075b05b9674351f98872
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905760"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a><span data-ttu-id="51b6d-103">ASP.NET Core içindeki uygulama bölümleriyle denetleyicileri, görünümleri, Razor Pages ve daha fazlasını paylaşma</span><span class="sxs-lookup"><span data-stu-id="51b6d-103">Share controllers, views, Razor Pages and more with Application Parts in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="51b6d-104">[Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="51b6d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="51b6d-105">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="51b6d-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="51b6d-106">*Uygulama bölümü* , bir uygulamanın kaynakları üzerinde soyutlamadır.</span><span class="sxs-lookup"><span data-stu-id="51b6d-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="51b6d-107">Uygulama bölümleri ASP.NET Core denetleyicileri bulmasına, bileşenleri, etiket yardımcılarını, Razor Pages, Razor derleme kaynaklarını ve daha fazlasını bulmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="51b6d-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="51b6d-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) bir uygulama bölümüdür.</span><span class="sxs-lookup"><span data-stu-id="51b6d-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="51b6d-109">`AssemblyPart` bir derleme başvurusunu kapsüller ve türleri ve derleme başvurularını ortaya koyar.</span><span class="sxs-lookup"><span data-stu-id="51b6d-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="51b6d-110">*Özellik sağlayıcıları* , uygulama bölümleriyle birlikte çalışarak bir ASP.NET Core uygulamasının özelliklerini doldurur.</span><span class="sxs-lookup"><span data-stu-id="51b6d-110">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="51b6d-111">Uygulama bölümleri için ana kullanım örneği, bir derlemeyi, bir derlemeden ASP.NET Core özellikleri bulacak (ya da yüklemeden kaçınacak) şekilde yapılandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="51b6d-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="51b6d-112">Örneğin, birden çok uygulama arasında ortak işlevselliği paylaşmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51b6d-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="51b6d-113">Uygulama parçalarını kullanarak, birden çok uygulamayla denetleyiciler, görünümler, Razor Pages, Razor derleme kaynakları, etiket yardımcıları ve daha fazlasını içeren bir derlemeyi (DLL) paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51b6d-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="51b6d-114">Birden çok projedeki kodu çoğaltmak için bir derlemeyi paylaşma tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="51b6d-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="51b6d-115">ASP.NET Core uygulamalar <xref:System.Web.WebPages.ApplicationPart>özellikleri yükler.</span><span class="sxs-lookup"><span data-stu-id="51b6d-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="51b6d-116"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> sınıfı, bir derleme tarafından desteklenen bir uygulama bölümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="51b6d-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="51b6d-117">ASP.NET Core özellikleri yükle</span><span class="sxs-lookup"><span data-stu-id="51b6d-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="51b6d-118">ASP.NET Core özelliklerini (denetleyiciler, görünüm bileşenleri vb.) bulup yüklemek için `ApplicationPart` ve `AssemblyPart` sınıflarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="51b6d-118">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="51b6d-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>, kullanılabilir uygulama parçalarını ve özellik sağlayıcılarını izler.</span><span class="sxs-lookup"><span data-stu-id="51b6d-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="51b6d-120">`ApplicationPartManager` `Startup.ConfigureServices`yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="51b6d-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="51b6d-121">Aşağıdaki kod, `AssemblyPart`kullanarak `ApplicationPartManager` yapılandırmaya yönelik alternatif bir yaklaşım sağlar:</span><span class="sxs-lookup"><span data-stu-id="51b6d-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="51b6d-122">Önceki iki kod örneği bir derlemeden `SharedController` yükler.</span><span class="sxs-lookup"><span data-stu-id="51b6d-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="51b6d-123">`SharedController`, uygulamanın projesinde değil.</span><span class="sxs-lookup"><span data-stu-id="51b6d-123">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="51b6d-124">Bkz. [Webappparts çözüm](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) örneği indirmesi.</span><span class="sxs-lookup"><span data-stu-id="51b6d-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="51b6d-125">Görünümleri dahil et</span><span class="sxs-lookup"><span data-stu-id="51b6d-125">Include views</span></span>

<span data-ttu-id="51b6d-126">Derleme içindeki görünümleri dahil etmek için:</span><span class="sxs-lookup"><span data-stu-id="51b6d-126">To include views in the assembly:</span></span>

* <span data-ttu-id="51b6d-127">Aşağıdaki biçimlendirmeyi paylaşılan proje dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="51b6d-127">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="51b6d-128"><xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>ekleyin:</span><span class="sxs-lookup"><span data-stu-id="51b6d-128">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="51b6d-129">Kaynakları yüklemeyi engelle</span><span class="sxs-lookup"><span data-stu-id="51b6d-129">Prevent loading resources</span></span>

<span data-ttu-id="51b6d-130">Uygulama bölümleri, belirli bir derleme veya konumdaki kaynakları yüklemeyi *önlemek* için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51b6d-130">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="51b6d-131">Kullanılabilir kaynakları gizlemek veya mevcut hale getirmek için <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> koleksiyonun üyelerini ekleyin veya kaldırın.</span><span class="sxs-lookup"><span data-stu-id="51b6d-131">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="51b6d-132">`ApplicationParts` koleksiyonundaki girdilerin sırası önemli değildir.</span><span class="sxs-lookup"><span data-stu-id="51b6d-132">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="51b6d-133">Kapsayıcıdaki hizmetleri yapılandırmak için kullanmadan önce `ApplicationPartManager` yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="51b6d-133">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="51b6d-134">Örneğin, `AddControllersAsServices`çağırmadan önce `ApplicationPartManager` yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="51b6d-134">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="51b6d-135">Bir kaynağı kaldırmak için `ApplicationParts` koleksiyonundaki `Remove` çağırın.</span><span class="sxs-lookup"><span data-stu-id="51b6d-135">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="51b6d-136">Aşağıdaki kod uygulamadan `MyDependentLibrary` kaldırmak için <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> kullanır: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="51b6d-136">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="51b6d-137">`ApplicationPartManager` şunlar için parçalar içerir:</span><span class="sxs-lookup"><span data-stu-id="51b6d-137">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="51b6d-138">Uygulamanın derlemesi ve bağımlı derlemeleri.</span><span class="sxs-lookup"><span data-stu-id="51b6d-138">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="51b6d-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="51b6d-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="51b6d-140">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="51b6d-140">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="51b6d-141">Uygulama özelliği sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="51b6d-141">Application feature providers</span></span>

<span data-ttu-id="51b6d-142">Uygulama özelliği sağlayıcıları uygulama parçalarını inceler ve bu parçalar için özellikler sağlar.</span><span class="sxs-lookup"><span data-stu-id="51b6d-142">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="51b6d-143">Aşağıdaki ASP.NET Core özellikleri için yerleşik özellik sağlayıcıları vardır:</span><span class="sxs-lookup"><span data-stu-id="51b6d-143">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="51b6d-144">Denetleyiciler</span><span class="sxs-lookup"><span data-stu-id="51b6d-144">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="51b6d-145">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="51b6d-145">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="51b6d-146">Bileşenleri görüntüle</span><span class="sxs-lookup"><span data-stu-id="51b6d-146">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="51b6d-147">Özellik sağlayıcıları, `T` özelliğin türü olduğu <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>devralınır.</span><span class="sxs-lookup"><span data-stu-id="51b6d-147">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="51b6d-148">Özellik sağlayıcıları, daha önce listelenen özellik türlerinden herhangi biri için uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="51b6d-148">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="51b6d-149">`ApplicationPartManager.FeatureProviders` özellik sağlayıcılarının sırası çalışma zamanı davranışını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="51b6d-149">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="51b6d-150">Daha sonra eklenen sağlayıcılar, daha önce eklenen sağlayıcıların yaptığı eylemlere yanıt verebilir.</span><span class="sxs-lookup"><span data-stu-id="51b6d-150">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="51b6d-151">Genel denetleyici özelliği</span><span class="sxs-lookup"><span data-stu-id="51b6d-151">Generic controller feature</span></span>

<span data-ttu-id="51b6d-152">ASP.NET Core [genel denetleyicileri](/dotnet/csharp/programming-guide/generics/generic-classes)yoksayar.</span><span class="sxs-lookup"><span data-stu-id="51b6d-152">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="51b6d-153">Genel denetleyicinin bir tür parametresi vardır (örneğin, `MyController<T>`).</span><span class="sxs-lookup"><span data-stu-id="51b6d-153">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="51b6d-154">Aşağıdaki örnek, belirli bir tür listesi için genel denetleyici örnekleri ekler:</span><span class="sxs-lookup"><span data-stu-id="51b6d-154">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="51b6d-155">Türler `EntityTypes.Types`tanımlanmıştır:</span><span class="sxs-lookup"><span data-stu-id="51b6d-155">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="51b6d-156">Özellik sağlayıcısı `Startup`eklendi:</span><span class="sxs-lookup"><span data-stu-id="51b6d-156">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="51b6d-157">Yönlendirme için kullanılan genel denetleyici adları, *pencere öğesi*yerine *' 1 [pencere öğesi] biçiminde olan genericcontroller* .</span><span class="sxs-lookup"><span data-stu-id="51b6d-157">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="51b6d-158">Aşağıdaki öznitelik, denetleyici tarafından kullanılan genel türe karşılık gelen adı değiştirir:</span><span class="sxs-lookup"><span data-stu-id="51b6d-158">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="51b6d-159">`GenericController` Sınıfı:</span><span class="sxs-lookup"><span data-stu-id="51b6d-159">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="51b6d-160">Örneğin, `https://localhost:5001/Sprocket` bir URL 'SI istemek aşağıdaki Yanıt ile sonuçlanır:</span><span class="sxs-lookup"><span data-stu-id="51b6d-160">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="51b6d-161">Kullanılabilir özellikleri görüntüle</span><span class="sxs-lookup"><span data-stu-id="51b6d-161">Display available features</span></span>

<span data-ttu-id="51b6d-162">Bir uygulama için kullanılabilen özellikler, [bağımlılık ekleme](../../fundamentals/dependency-injection.md)yoluyla bir `ApplicationPartManager` isteyerek numaralandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="51b6d-162">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="51b6d-163">[Yükleme örneği](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) , uygulama özelliklerini göstermek için yukarıdaki kodu kullanır:</span><span class="sxs-lookup"><span data-stu-id="51b6d-163">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="51b6d-164">[Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="51b6d-164">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="51b6d-165">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="51b6d-165">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="51b6d-166">*Uygulama bölümü* , bir uygulamanın kaynakları üzerinde soyutlamadır.</span><span class="sxs-lookup"><span data-stu-id="51b6d-166">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="51b6d-167">Uygulama bölümleri ASP.NET Core denetleyicileri bulmasına, bileşenleri, etiket yardımcılarını, Razor Pages, Razor derleme kaynaklarını ve daha fazlasını bulmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="51b6d-167">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="51b6d-168">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) bir uygulama bölümüdür.</span><span class="sxs-lookup"><span data-stu-id="51b6d-168">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="51b6d-169">`AssemblyPart` bir derleme başvurusunu kapsüller ve türleri ve derleme başvurularını ortaya koyar.</span><span class="sxs-lookup"><span data-stu-id="51b6d-169">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="51b6d-170">*Özellik sağlayıcıları* , uygulama bölümleriyle birlikte çalışarak bir ASP.NET Core uygulamasının özelliklerini doldurur.</span><span class="sxs-lookup"><span data-stu-id="51b6d-170">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="51b6d-171">Uygulama bölümleri için ana kullanım örneği, bir derlemeyi, bir derlemeden ASP.NET Core özellikleri bulacak (ya da yüklemeden kaçınacak) şekilde yapılandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="51b6d-171">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="51b6d-172">Örneğin, birden çok uygulama arasında ortak işlevselliği paylaşmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51b6d-172">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="51b6d-173">Uygulama parçalarını kullanarak, birden çok uygulamayla denetleyiciler, görünümler, Razor Pages, Razor derleme kaynakları, etiket yardımcıları ve daha fazlasını içeren bir derlemeyi (DLL) paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51b6d-173">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="51b6d-174">Birden çok projedeki kodu çoğaltmak için bir derlemeyi paylaşma tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="51b6d-174">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="51b6d-175">ASP.NET Core uygulamalar <xref:System.Web.WebPages.ApplicationPart>özellikleri yükler.</span><span class="sxs-lookup"><span data-stu-id="51b6d-175">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="51b6d-176"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> sınıfı, bir derleme tarafından desteklenen bir uygulama bölümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="51b6d-176">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="51b6d-177">ASP.NET Core özellikleri yükle</span><span class="sxs-lookup"><span data-stu-id="51b6d-177">Load ASP.NET Core features</span></span>

<span data-ttu-id="51b6d-178">ASP.NET Core özelliklerini (denetleyiciler, görünüm bileşenleri vb.) bulup yüklemek için `ApplicationPart` ve `AssemblyPart` sınıflarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="51b6d-178">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="51b6d-179"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>, kullanılabilir uygulama parçalarını ve özellik sağlayıcılarını izler.</span><span class="sxs-lookup"><span data-stu-id="51b6d-179">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="51b6d-180">`ApplicationPartManager` `Startup.ConfigureServices`yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="51b6d-180">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="51b6d-181">Aşağıdaki kod, `AssemblyPart`kullanarak `ApplicationPartManager` yapılandırmaya yönelik alternatif bir yaklaşım sağlar:</span><span class="sxs-lookup"><span data-stu-id="51b6d-181">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="51b6d-182">Önceki iki kod örneği bir derlemeden `SharedController` yükler.</span><span class="sxs-lookup"><span data-stu-id="51b6d-182">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="51b6d-183">`SharedController`, uygulamanın projesinde değil.</span><span class="sxs-lookup"><span data-stu-id="51b6d-183">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="51b6d-184">Bkz. [Webappparts çözüm](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) örneği indirmesi.</span><span class="sxs-lookup"><span data-stu-id="51b6d-184">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="51b6d-185">Görünümleri dahil et</span><span class="sxs-lookup"><span data-stu-id="51b6d-185">Include views</span></span>

<span data-ttu-id="51b6d-186">Derleme içindeki görünümleri dahil etmek için:</span><span class="sxs-lookup"><span data-stu-id="51b6d-186">To include views in the assembly:</span></span>

* <span data-ttu-id="51b6d-187">Aşağıdaki biçimlendirmeyi paylaşılan proje dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="51b6d-187">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="51b6d-188"><xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>ekleyin:</span><span class="sxs-lookup"><span data-stu-id="51b6d-188">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="51b6d-189">Kaynakları yüklemeyi engelle</span><span class="sxs-lookup"><span data-stu-id="51b6d-189">Prevent loading resources</span></span>

<span data-ttu-id="51b6d-190">Uygulama bölümleri, belirli bir derleme veya konumdaki kaynakları yüklemeyi *önlemek* için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51b6d-190">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="51b6d-191">Kullanılabilir kaynakları gizlemek veya mevcut hale getirmek için <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> koleksiyonun üyelerini ekleyin veya kaldırın.</span><span class="sxs-lookup"><span data-stu-id="51b6d-191">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="51b6d-192">`ApplicationParts` koleksiyonundaki girdilerin sırası önemli değildir.</span><span class="sxs-lookup"><span data-stu-id="51b6d-192">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="51b6d-193">Kapsayıcıdaki hizmetleri yapılandırmak için kullanmadan önce `ApplicationPartManager` yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="51b6d-193">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="51b6d-194">Örneğin, `AddControllersAsServices`çağırmadan önce `ApplicationPartManager` yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="51b6d-194">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="51b6d-195">Bir kaynağı kaldırmak için `ApplicationParts` koleksiyonundaki `Remove` çağırın.</span><span class="sxs-lookup"><span data-stu-id="51b6d-195">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="51b6d-196">Aşağıdaki kod uygulamadan `MyDependentLibrary` kaldırmak için <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> kullanır: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="51b6d-196">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="51b6d-197">`ApplicationPartManager` şunlar için parçalar içerir:</span><span class="sxs-lookup"><span data-stu-id="51b6d-197">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="51b6d-198">Uygulamanın derlemesi ve bağımlı derlemeleri.</span><span class="sxs-lookup"><span data-stu-id="51b6d-198">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="51b6d-199">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="51b6d-199">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="51b6d-200">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="51b6d-200">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="51b6d-201">Uygulama özelliği sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="51b6d-201">Application feature providers</span></span>

<span data-ttu-id="51b6d-202">Uygulama özelliği sağlayıcıları uygulama parçalarını inceler ve bu parçalar için özellikler sağlar.</span><span class="sxs-lookup"><span data-stu-id="51b6d-202">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="51b6d-203">Aşağıdaki ASP.NET Core özellikleri için yerleşik özellik sağlayıcıları vardır:</span><span class="sxs-lookup"><span data-stu-id="51b6d-203">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="51b6d-204">Denetleyiciler</span><span class="sxs-lookup"><span data-stu-id="51b6d-204">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="51b6d-205">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="51b6d-205">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="51b6d-206">Bileşenleri görüntüle</span><span class="sxs-lookup"><span data-stu-id="51b6d-206">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="51b6d-207">Özellik sağlayıcıları, `T` özelliğin türü olduğu <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>devralınır.</span><span class="sxs-lookup"><span data-stu-id="51b6d-207">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="51b6d-208">Özellik sağlayıcıları, daha önce listelenen özellik türlerinden herhangi biri için uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="51b6d-208">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="51b6d-209">`ApplicationPartManager.FeatureProviders` özellik sağlayıcılarının sırası çalışma zamanı davranışını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="51b6d-209">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="51b6d-210">Daha sonra eklenen sağlayıcılar, daha önce eklenen sağlayıcıların yaptığı eylemlere yanıt verebilir.</span><span class="sxs-lookup"><span data-stu-id="51b6d-210">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="51b6d-211">Genel denetleyici özelliği</span><span class="sxs-lookup"><span data-stu-id="51b6d-211">Generic controller feature</span></span>

<span data-ttu-id="51b6d-212">ASP.NET Core [genel denetleyicileri](/dotnet/csharp/programming-guide/generics/generic-classes)yoksayar.</span><span class="sxs-lookup"><span data-stu-id="51b6d-212">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="51b6d-213">Genel denetleyicinin bir tür parametresi vardır (örneğin, `MyController<T>`).</span><span class="sxs-lookup"><span data-stu-id="51b6d-213">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="51b6d-214">Aşağıdaki örnek, belirli bir tür listesi için genel denetleyici örnekleri ekler:</span><span class="sxs-lookup"><span data-stu-id="51b6d-214">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="51b6d-215">Türler `EntityTypes.Types`tanımlanmıştır:</span><span class="sxs-lookup"><span data-stu-id="51b6d-215">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="51b6d-216">Özellik sağlayıcısı `Startup`eklendi:</span><span class="sxs-lookup"><span data-stu-id="51b6d-216">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="51b6d-217">Yönlendirme için kullanılan genel denetleyici adları, *pencere öğesi*yerine *' 1 [pencere öğesi] biçiminde olan genericcontroller* .</span><span class="sxs-lookup"><span data-stu-id="51b6d-217">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="51b6d-218">Aşağıdaki öznitelik, denetleyici tarafından kullanılan genel türe karşılık gelen adı değiştirir:</span><span class="sxs-lookup"><span data-stu-id="51b6d-218">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="51b6d-219">`GenericController` Sınıfı:</span><span class="sxs-lookup"><span data-stu-id="51b6d-219">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="51b6d-220">Örneğin, `https://localhost:5001/Sprocket` bir URL 'SI istemek aşağıdaki Yanıt ile sonuçlanır:</span><span class="sxs-lookup"><span data-stu-id="51b6d-220">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="51b6d-221">Kullanılabilir özellikleri görüntüle</span><span class="sxs-lookup"><span data-stu-id="51b6d-221">Display available features</span></span>

<span data-ttu-id="51b6d-222">Bir uygulama için kullanılabilen özellikler, [bağımlılık ekleme](../../fundamentals/dependency-injection.md)yoluyla bir `ApplicationPartManager` isteyerek numaralandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="51b6d-222">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="51b6d-223">[Yükleme örneği](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) , uygulama özelliklerini göstermek için yukarıdaki kodu kullanır:</span><span class="sxs-lookup"><span data-stu-id="51b6d-223">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

::: moniker-end
