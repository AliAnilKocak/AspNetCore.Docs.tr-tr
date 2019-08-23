---
title: ASP.NET Core Razor SDK'sı
author: Rick-Anderson
description: Nasıl ASP.NET Core Razor sayfalar kodlama sayfa odaklı senaryolar daha kolay ve MVC kullanmaktan daha üretken hale getirdiğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/18/2019
uid: razor-pages/sdk
ms.openlocfilehash: 1dc001c7c5fe320629835e06fe6db7fadabff94d
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69985393"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="4aacd-103">ASP.NET Core Razor SDK'sı</span><span class="sxs-lookup"><span data-stu-id="4aacd-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="4aacd-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4aacd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="4aacd-105">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="4aacd-105">Overview</span></span>

<span data-ttu-id="4aacd-106">[!INCLUDE[](~/includes/2.1-SDK.md)] İçerir `Microsoft.NET.Sdk.Razor` MSBuild SDK'sı (Razor SDK).</span><span class="sxs-lookup"><span data-stu-id="4aacd-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="4aacd-107">Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="4aacd-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"
* <span data-ttu-id="4aacd-108">Oluşturma, paketleme ve içeren proje yayımlama deneyimi standartlaştırır [Razor](xref:mvc/views/razor) dosyaları ASP.NET Core MVC tabanlı projeler için.</span><span class="sxs-lookup"><span data-stu-id="4aacd-108">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="4aacd-109">Bir dizi önceden tanımlanmış hedefleri, özellikler ve Razor dosyaları derleme özelleştirme izin veren öğeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <span data-ttu-id="4aacd-110">ASP.NET Core MVC tabanlı projeler veya [Blazor](xref:blazor/index) projeleri için [Razor](xref:mvc/views/razor) dosyaları içeren projeleri derlemek, paketlemek ve yayımlamak için gereklidir</span><span class="sxs-lookup"><span data-stu-id="4aacd-110">is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects, or [Blazor](xref:blazor/index) projects</span></span>
* <span data-ttu-id="4aacd-111">, Razor (. cshtml veya. Razor) dosyalarının derlemesini özelleştirmeye izin veren bir dizi önceden tanımlanmış hedef, özellik ve öğe içerir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-111">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (.cshtml or .razor) files.</span></span>
::: moniker-end


::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="4aacd-112">Razor SDK, `<Content>` `**\*.cshtml` glob düzenine ayarlanmış bir `Include` özniteliği olan bir öğesi içerir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-112">The Razor SDK includes a `<Content>` element with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="4aacd-113">Eşleşen dosyalar yayımlandı.</span><span class="sxs-lookup"><span data-stu-id="4aacd-113">Matching files are published.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4aacd-114">Razor SDK, `<Content>` `**\*.cshtml` ve `Include` Glob`**\*.razor` desenlerine ayarlanmış öznitelikleri olan öğeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-114">The Razor SDK includes `<Content>` elements with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="4aacd-115">Eşleşen dosyalar yayımlandı.</span><span class="sxs-lookup"><span data-stu-id="4aacd-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="4aacd-116">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="4aacd-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="4aacd-117">Razor SDK 'sını kullanma</span><span class="sxs-lookup"><span data-stu-id="4aacd-117">Use the Razor SDK</span></span>

<span data-ttu-id="4aacd-118">Çoğu web uygulaması açıkça Razor SDK'ya başvurmak için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"
<span data-ttu-id="4aacd-119">Razor görünümleri ya da Razor sayfaları içeren sınıf kitaplıkları oluşturmak için Razor SDK'sını kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="4aacd-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="4aacd-120">Kullanım `Microsoft.NET.Sdk.Razor` yerine `Microsoft.NET.Sdk`:</span><span class="sxs-lookup"><span data-stu-id="4aacd-120">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="4aacd-121">Genellikle, bir paket başvurusu `Microsoft.AspNetCore.Mvc` oluşturmak ve Razor sayfaları ve Razor görünümleri derlemek için gereken ek bağımlılıklar almak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-121">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="4aacd-122">En azından, paket başvuruları projenize eklemeniz gerekir:</span><span class="sxs-lookup"><span data-stu-id="4aacd-122">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  <span data-ttu-id="4aacd-123">`Microsoft.AspNetCore.Razor.Design` Paket proje için Razor derleme görevleri ve hedefleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="4aacd-123">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="4aacd-124">Yukarıdaki paketleri dahil `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="4aacd-124">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="4aacd-125">Aşağıdaki biçimlendirmede Razor dosyaları için bir ASP.NET Core Razor sayfalar uygulama oluşturmak için Razor SDK'sını kullanan bir proje dosyasını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="4aacd-125">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]
  
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="4aacd-126">Razor görünümlerini içeren sınıf kitaplıkları oluşturmak için Razor SDK 'sını veya Razor Pages Razor sınıf kitaplığı proje şablonuyla başlamasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="4aacd-126">To use the Razor SDK to build class libraries containing Razor views or Razor Pages we recommend starting with the Razor Class Library project template.</span></span> <span data-ttu-id="4aacd-127">Blazor (. Razor) dosyalarını derlemek için kullanılan bir Razor sınıfı kitaplığı, `Microsoft.AspNetCore.Components` en azından pakete bir başvuru gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-127">A Razor class library that is used to build Blazor (.razor) files will at minimum require a reference to the `Microsoft.AspNetCore.Components` package.</span></span> <span data-ttu-id="4aacd-128">Razor görünümlerini veya sayfalarını (. cshtml dosyaları) derlemek için kullanılan bir Razor sınıfı kitaplığı, hedefleme `netcoreapp3.0` veya daha yeni ve `FrameworkReference` öğesine `Microsoft.AspNetCore.App`sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-128">A Razor class library that is used to build Razor views or pages (.cshtml files), will require targeting `netcoreapp3.0` or newer and have a `FrameworkReference` to `Microsoft.AspNetCore.App`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="4aacd-129">`Microsoft.AspNetCore.Razor.Design` Ve `Microsoft.AspNetCore.Mvc.Razor.Extensions` paketleri dahil edilecek [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="4aacd-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="4aacd-130">Ancak, daha az sürüm `Microsoft.AspNetCore.App` paket başvuru sağlayan bir metapackage en son sürümünü içermeyen App `Microsoft.AspNetCore.Razor.Design`.</span><span class="sxs-lookup"><span data-stu-id="4aacd-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="4aacd-131">Projeleri sürümünün tutarlı olmasını başvurmalıdır `Microsoft.AspNetCore.Razor.Design` (veya `Microsoft.AspNetCore.Mvc`) böylece Razor yönelik en son derleme zamanı düzeltmeleri dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="4aacd-132">Daha fazla bilgi için [bu GitHub sorunu](https://github.com/aspnet/Razor/issues/2553).</span><span class="sxs-lookup"><span data-stu-id="4aacd-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="4aacd-133">Özellikler</span><span class="sxs-lookup"><span data-stu-id="4aacd-133">Properties</span></span>

<span data-ttu-id="4aacd-134">Aşağıdaki özellikler proje derlemesi bir parçası olarak Razor'ın SDK davranışı denetler:</span><span class="sxs-lookup"><span data-stu-id="4aacd-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="4aacd-135">`RazorCompileOnBuild` &ndash; Zaman `true`, derler ve Razor derleme proje oluşturma işleminin parçası olarak yayar.</span><span class="sxs-lookup"><span data-stu-id="4aacd-135">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="4aacd-136">Varsayılan olarak `true`.</span><span class="sxs-lookup"><span data-stu-id="4aacd-136">Defaults to `true`.</span></span>
* <span data-ttu-id="4aacd-137">`RazorCompileOnPublish` &ndash; Zaman `true`, derler ve Razor derleme proje yayımlama işleminin parçası olarak yayar.</span><span class="sxs-lookup"><span data-stu-id="4aacd-137">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="4aacd-138">Varsayılan olarak `true`.</span><span class="sxs-lookup"><span data-stu-id="4aacd-138">Defaults to `true`.</span></span>

<span data-ttu-id="4aacd-139">Özellikler ve öğeler aşağıdaki tabloda, giriş ve çıkış Razor SDK'sına yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4aacd-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"
> [!WARNING]
<span data-ttu-id="4aacd-140">ASP.NET Core 3,0 ' den başlayarak, MVC görünümleri veya Razor Pages, `RazorCompileOnBuild` veya `RazorCompileOnPublish` devre dışıysa varsayılan olarak sunulmayacak.</span><span class="sxs-lookup"><span data-stu-id="4aacd-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages will not be served by default if `RazorCompileOnBuild` or `RazorCompileOnPublish` is disabled.</span></span> <span data-ttu-id="4aacd-141">Uygulamalar, cshtml dosyalarını işlemek üzere çalışma `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` zamanı derlemesini kullanıyorsa, çalışma zamanı derlemesi için destek eklemek üzere pakete açık bir başvuru eklememelidir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-141">Applications must add an explicit reference to `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` package to add support for runtime compilation if they rely on runtime compilation to process cshtml files.</span></span>
::: moniker-end


| <span data-ttu-id="4aacd-142">Öğeler</span><span class="sxs-lookup"><span data-stu-id="4aacd-142">Items</span></span> | <span data-ttu-id="4aacd-143">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4aacd-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="4aacd-144">Kod oluşturmaya giriş olan öğe öğeleri ( *. cshtml* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="4aacd-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="4aacd-145">Bileşen kodu oluşturmaya giriş olan öğe öğeleri ( *. Razor* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="4aacd-145">Item elements (*.razor* files) that are inputs to Component code generation.</span></span>
| `RazorCompile` | <span data-ttu-id="4aacd-146">Razor derleme hedeflerine giriş olan öğe öğeleri ( *. cs* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="4aacd-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="4aacd-147">Razor derlemesinde `ItemGroup` derlenecek ek dosyaları belirtmek için bunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="4aacd-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="4aacd-148">Kod için kullanılan öğeler için Razor derleme öznitelikleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4aacd-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="4aacd-149">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4aacd-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="4aacd-150">Oluşturulan Razor derlemesine katıştırılmış kaynakları olarak eklenen öğeler.</span><span class="sxs-lookup"><span data-stu-id="4aacd-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="4aacd-151">Özellik</span><span class="sxs-lookup"><span data-stu-id="4aacd-151">Property</span></span> | <span data-ttu-id="4aacd-152">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4aacd-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="4aacd-153">Razor tarafından üretilen derleme dosya adı (uzantısı olmadan).</span><span class="sxs-lookup"><span data-stu-id="4aacd-153">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="4aacd-154">Razor çıkış dizini.</span><span class="sxs-lookup"><span data-stu-id="4aacd-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="4aacd-155">Razor derlemesi oluşturmak için kullanılan araç kümesini belirlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4aacd-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="4aacd-156">Geçerli değerler `Implicit`, `RazorSDK`, ve `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="4aacd-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="4aacd-157">Enabledefaultcontentıtems</span><span class="sxs-lookup"><span data-stu-id="4aacd-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="4aacd-158">Varsayılan değer `true`.</span><span class="sxs-lookup"><span data-stu-id="4aacd-158">Default is `true`.</span></span> <span data-ttu-id="4aacd-159">Ne `true`zaman, *Web. config*, *. JSON*ve *. cshtml* dosyalarını projeye içerik olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="4aacd-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="4aacd-160">Aracılığıyla başvurulduğunda `Microsoft.NET.Sdk.Web`, altında dosyaları *wwwroot* ve yapılandırma dosyaları da dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="4aacd-161">Zaman `true`, içeren *.cshtml* dosyalarını `Content` öğeler `RazorGenerate` öğeleri.</span><span class="sxs-lookup"><span data-stu-id="4aacd-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="4aacd-162">Zaman `true`, oluşturur bir *.cs* tarafından belirtilen öznitelikler içeren dosya `RazorAssemblyAttribute` ve derleme çıkış dosyası içerir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="4aacd-163">Zaman `true`, derleme öznitelikleri için varsayılan ayarı ekler `RazorAssemblyAttribute`.</span><span class="sxs-lookup"><span data-stu-id="4aacd-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="4aacd-164">Zaman `true`, kopya `RazorGenerate` öğeleri ( *.cshtml*) dosyalarını yayımlama dizinine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="4aacd-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="4aacd-165">Genellikle, derleme zamanı veya yayımlama zamanı derleme katıldıkları, Razor dosyaları bir yayımlanan uygulama için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="4aacd-166">Varsayılan olarak `false`.</span><span class="sxs-lookup"><span data-stu-id="4aacd-166">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="4aacd-167">Zaman `true`, başvuru bütünleştirilmiş kod öğeleri Yayımla dizinine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="4aacd-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="4aacd-168">Genellikle, Razor derleme, derleme zamanı veya yayımlama zamanı oluşursa başvuru bütünleştirilmiş kodları yayınlanmış bir uygulama için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="4aacd-169">Kümesine `true` yayımlanmış uygulamanızın çalışma zamanı derlemesi gerekiyorsa.</span><span class="sxs-lookup"><span data-stu-id="4aacd-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="4aacd-170">Örneğin, değer kümesine `true` uygulama değiştirirse *.cshtml* çalışma zamanında dosya veya katıştırılmış görünümlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="4aacd-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="4aacd-171">Varsayılan olarak `false`.</span><span class="sxs-lookup"><span data-stu-id="4aacd-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="4aacd-172">Zaman `true`, tüm Razor içerik öğeleri ( *.cshtml* dosyaları) üretilen NuGet paketini eklenmek üzere işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="4aacd-173">Varsayılan olarak `false`.</span><span class="sxs-lookup"><span data-stu-id="4aacd-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="4aacd-174">Zaman `true`, RazorGenerate ekler ( *.cshtml*) öğeleri olarak oluşturulmuş Razor derlemesine katıştırılmış dosyaları.</span><span class="sxs-lookup"><span data-stu-id="4aacd-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="4aacd-175">Varsayılan olarak `false`.</span><span class="sxs-lookup"><span data-stu-id="4aacd-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="4aacd-176">Zaman `true`, kod oluşturma iş yüklerini boşaltmak üzere bir sürekli derleme sunucu işlemi kullanır.</span><span class="sxs-lookup"><span data-stu-id="4aacd-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="4aacd-177">Varsayılan olarak, değeri olarak `UseSharedCompilation`.</span><span class="sxs-lookup"><span data-stu-id="4aacd-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="4aacd-178">Ne `true`zaman, SDK, uygulama bölümü keşfi gerçekleştirmek için çalışma zamanında MVC tarafından kullanılan ek öznitelikler üretir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |

<span data-ttu-id="4aacd-179">Özellikler hakkında daha fazla bilgi için bkz. [MSBuild özellikleri](/visualstudio/msbuild/msbuild-properties).</span><span class="sxs-lookup"><span data-stu-id="4aacd-179">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="4aacd-180">Hedefler</span><span class="sxs-lookup"><span data-stu-id="4aacd-180">Targets</span></span>

<span data-ttu-id="4aacd-181">Razor SDK'sı iki birincil hedefleri tanımlar:</span><span class="sxs-lookup"><span data-stu-id="4aacd-181">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="4aacd-182">`RazorGenerate` &ndash; Kod oluşturur *.cs* dosyalarını `RazorGenerate` öğesi öğeleri.</span><span class="sxs-lookup"><span data-stu-id="4aacd-182">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="4aacd-183">Kullanım `RazorGenerateDependsOn` özelliği ek hedefler belirtmek için önce veya sonra bu hedef çalıştırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4aacd-183">Use `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="4aacd-184">`RazorCompile` &ndash; Oluşturulan derler *.cs* için bir Razor derleme dosyaları.</span><span class="sxs-lookup"><span data-stu-id="4aacd-184">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="4aacd-185">Kullanım `RazorCompileDependsOn` önce veya sonra bu hedef çalıştırabilirsiniz ek hedefleri belirtmek için.</span><span class="sxs-lookup"><span data-stu-id="4aacd-185">Use `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="4aacd-186">`RazorComponentGenerate`Kod, öğe öğeleri için `RazorComponent` *. cs* dosyaları oluşturur. &ndash;</span><span class="sxs-lookup"><span data-stu-id="4aacd-186">`RazorComponentGenerate` &ndash; Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="4aacd-187">Kullanım `RazorComponentGenerateDependsOn` özelliği ek hedefler belirtmek için önce veya sonra bu hedef çalıştırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4aacd-187">Use `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="4aacd-188">Razor görünüm çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="4aacd-188">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="4aacd-189">Varsayılan olarak, Razor SDK'sı, çalışma zamanı derleme gerçekleştirmek için gereken başvuru derlemelerini yayımlama değil.</span><span class="sxs-lookup"><span data-stu-id="4aacd-189">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="4aacd-190">Çalışma zamanı derleme sırasında uygulama modeli kullanır, bu derleme hataları sonuçları&mdash;Örneğin, uygulama yayımlandıktan sonra uygulamayı katıştırılmış görünümleri ya da değişiklikleri görünümler kullanır.</span><span class="sxs-lookup"><span data-stu-id="4aacd-190">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="4aacd-191">Ayarlama `CopyRefAssembliesToPublishDirectory` için `true` başvuru bütünleştirilmiş kodları yayımlamaya devam etmek için.</span><span class="sxs-lookup"><span data-stu-id="4aacd-191">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="4aacd-192">Bir web uygulaması için uygulamanızın hedeflediği olun `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="4aacd-192">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="4aacd-193">Razor dili sürümü</span><span class="sxs-lookup"><span data-stu-id="4aacd-193">Razor language version</span></span>

<span data-ttu-id="4aacd-194">`Microsoft.NET.Sdk.Web` SDK 'yı hedeflerken, Razor dili sürümü uygulamanın hedef Framework sürümünden algılanır.</span><span class="sxs-lookup"><span data-stu-id="4aacd-194">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the the app's target framework version.</span></span> <span data-ttu-id="4aacd-195">`Microsoft.NET.Sdk.Razor` SDK 'yı hedefleyen projeler veya uygulamanın çıkarılan değerden farklı bir Razor dili sürümü gerektirmesi durumunda, uygulamanın proje dosyasındaki `<RazorLangVersion>` özelliği ayarlanarak bir sürüm yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="4aacd-195">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="4aacd-196">Razor 'nin dil sürümü, için oluşturulduğu çalışma zamanının sürümü ile sıkı bir şekilde tümleşiktir.</span><span class="sxs-lookup"><span data-stu-id="4aacd-196">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="4aacd-197">Çalışma zamanı için tasarlanmamış bir dil sürümünü hedeflemek desteklenmez ve muhtemelen derleme hataları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4aacd-197">Targeting a language version that is not designed for the runtime is unsupported, and will likely produce build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4aacd-198">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4aacd-198">Additional resources</span></span>

* [<span data-ttu-id="4aacd-199">.NET Core için csproj biçimine eklemeler</span><span class="sxs-lookup"><span data-stu-id="4aacd-199">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="4aacd-200">Ortak MSBuild proje öğeleri</span><span class="sxs-lookup"><span data-stu-id="4aacd-200">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
