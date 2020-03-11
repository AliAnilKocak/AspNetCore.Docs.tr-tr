---
title: ASP.NET Core Razor SDK'sı
author: Rick-Anderson
description: Nasıl ASP.NET Core Razor sayfalar kodlama sayfa odaklı senaryolar daha kolay ve MVC kullanmaktan daha üretken hale getirdiğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/23/2019
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 872d90662494735dc0e4caa01c46fcdcc2606bc6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660071"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="46acc-103">ASP.NET Core Razor SDK'sı</span><span class="sxs-lookup"><span data-stu-id="46acc-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="46acc-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="46acc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="46acc-105">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="46acc-105">Overview</span></span>

<span data-ttu-id="46acc-106">[!INCLUDE[](~/includes/2.1-SDK.md)], `Microsoft.NET.Sdk.Razor` MSBuild SDK 'sını (Razor SDK) içerir.</span><span class="sxs-lookup"><span data-stu-id="46acc-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="46acc-107">Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="46acc-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="46acc-108">ASP.NET Core MVC tabanlı veya [Blazor](xref:blazor/index) projeleri için [Razor](xref:mvc/views/razor) dosyaları içeren projeleri derlemek, paketlemek ve yayımlamak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="46acc-108">Is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based or [Blazor](xref:blazor/index) projects.</span></span>
* <span data-ttu-id="46acc-109">, Razor ( *. cshtml* veya *. Razor*) dosyalarının derlemesini özelleştirmeye izin veren bir dizi önceden tanımlanmış hedef, özellik ve öğe içerir.</span><span class="sxs-lookup"><span data-stu-id="46acc-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (*.cshtml* or *.razor*) files.</span></span>

<span data-ttu-id="46acc-110">Razor SDK, `**\*.cshtml` ve `**\*.razor` glob desenlerine ayarlı `Include` öznitelikleri olan `Content` öğeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="46acc-110">The Razor SDK includes `Content` items with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="46acc-111">Eşleşen dosyalar yayımlandı.</span><span class="sxs-lookup"><span data-stu-id="46acc-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="46acc-112">ASP.NET Core MVC tabanlı projeler için [Razor](xref:mvc/views/razor) dosyaları içeren projeleri oluşturma, paketleme ve yayımlama ile ilgili deneyimi standartlaştırır.</span><span class="sxs-lookup"><span data-stu-id="46acc-112">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="46acc-113">Bir dizi önceden tanımlanmış hedefleri, özellikler ve Razor dosyaları derleme özelleştirme izin veren öğeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="46acc-113">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

<span data-ttu-id="46acc-114">Razor SDK, `**\*.cshtml` glob düzenine ayarlanmış bir `Include` özniteliğine sahip `Content` öğesi içerir.</span><span class="sxs-lookup"><span data-stu-id="46acc-114">The Razor SDK includes a `Content` item with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="46acc-115">Eşleşen dosyalar yayımlandı.</span><span class="sxs-lookup"><span data-stu-id="46acc-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="46acc-116">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="46acc-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="46acc-117">Razor SDK 'sını kullanma</span><span class="sxs-lookup"><span data-stu-id="46acc-117">Use the Razor SDK</span></span>

<span data-ttu-id="46acc-118">Çoğu web uygulaması açıkça Razor SDK'ya başvurmak için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="46acc-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="46acc-119">Razor görünümlerini veya Razor Pages içeren sınıf kitaplıkları oluşturmak için Razor SDK 'yı kullanmak için Razor sınıf kitaplığı (RCL) proje şablonuyla başlamasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="46acc-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages, we recommend starting with the Razor class library (RCL) project template.</span></span> <span data-ttu-id="46acc-120">Blazor ( *. Razor*) dosyalarını derlemek için kullanılan bir RCL, [Microsoft. Aspnetcore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) paketine en az bir başvuru gerektirir.</span><span class="sxs-lookup"><span data-stu-id="46acc-120">An RCL that's used to build Blazor (*.razor*) files minimally requires a reference to the [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) package.</span></span> <span data-ttu-id="46acc-121">Razor görünümlerini veya sayfalarını ( *. cshtml* dosyaları) derlemek için kullanılan bir rcl, `netcoreapp3.0` veya üzeri hedefleme gerektirir ve proje dosyasındaki [Microsoft. Aspnetcore. App metapackage](xref:fundamentals/metapackage-app) `FrameworkReference`.</span><span class="sxs-lookup"><span data-stu-id="46acc-121">An RCL that's used to build Razor views or pages (*.cshtml* files) minimally requires targeting `netcoreapp3.0` or later and has a `FrameworkReference` to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in its project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="46acc-122">Razor görünümleri ya da Razor sayfaları içeren sınıf kitaplıkları oluşturmak için Razor SDK'sını kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="46acc-122">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="46acc-123">`Microsoft.NET.Sdk`yerine `Microsoft.NET.Sdk.Razor` kullanın:</span><span class="sxs-lookup"><span data-stu-id="46acc-123">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="46acc-124">Genellikle, Razor Pages ve Razor görünümlerini derlemek ve derlemek için gereken ek bağımlılıklar almak için `Microsoft.AspNetCore.Mvc` bir paket başvurusu gerekir.</span><span class="sxs-lookup"><span data-stu-id="46acc-124">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="46acc-125">En azından, paket başvuruları projenize eklemeniz gerekir:</span><span class="sxs-lookup"><span data-stu-id="46acc-125">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  <span data-ttu-id="46acc-126">`Microsoft.AspNetCore.Razor.Design` paketi, proje için Razor derleme görevlerini ve hedeflerini sağlar.</span><span class="sxs-lookup"><span data-stu-id="46acc-126">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="46acc-127">Önceki paketler `Microsoft.AspNetCore.Mvc`eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="46acc-127">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="46acc-128">Aşağıdaki biçimlendirmede Razor dosyaları için bir ASP.NET Core Razor sayfalar uygulama oluşturmak için Razor SDK'sını kullanan bir proje dosyasını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="46acc-128">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="46acc-129">`Microsoft.AspNetCore.Razor.Design` ve `Microsoft.AspNetCore.Mvc.Razor.Extensions` paketleri [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)içinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="46acc-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="46acc-130">Ancak, sürüm-daha az `Microsoft.AspNetCore.App` paket başvurusu, en son `Microsoft.AspNetCore.Razor.Design`sürümünü içermeyen uygulamaya bir metapackage sağlar.</span><span class="sxs-lookup"><span data-stu-id="46acc-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="46acc-131">, Razor için en son derleme zamanı düzeltmelerinin dahil olması için projeler `Microsoft.AspNetCore.Razor.Design` tutarlı bir sürüme (veya `Microsoft.AspNetCore.Mvc`) başvurmalıdır.</span><span class="sxs-lookup"><span data-stu-id="46acc-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="46acc-132">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/aspnet/Razor/issues/2553)bakın.</span><span class="sxs-lookup"><span data-stu-id="46acc-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="46acc-133">Özellikler</span><span class="sxs-lookup"><span data-stu-id="46acc-133">Properties</span></span>

<span data-ttu-id="46acc-134">Aşağıdaki özellikler proje derlemesi bir parçası olarak Razor'ın SDK davranışı denetler:</span><span class="sxs-lookup"><span data-stu-id="46acc-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="46acc-135">`true``RazorCompileOnBuild` &ndash;, proje oluşturmanın bir parçası olarak Razor derlemesini derler ve yayar.</span><span class="sxs-lookup"><span data-stu-id="46acc-135">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="46acc-136">`true` değerini varsayılan olarak alır.</span><span class="sxs-lookup"><span data-stu-id="46acc-136">Defaults to `true`.</span></span>
* <span data-ttu-id="46acc-137">`true``RazorCompileOnPublish` &ndash;, projenin yayımlaması kapsamında Razor derlemesini derler ve yayar.</span><span class="sxs-lookup"><span data-stu-id="46acc-137">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="46acc-138">`true` değerini varsayılan olarak alır.</span><span class="sxs-lookup"><span data-stu-id="46acc-138">Defaults to `true`.</span></span>

<span data-ttu-id="46acc-139">Özellikler ve öğeler aşağıdaki tabloda, giriş ve çıkış Razor SDK'sına yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="46acc-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="46acc-140">ASP.NET Core 3,0 ' den başlayarak, MVC görünümleri veya Razor Pages proje dosyasındaki `RazorCompileOnBuild` veya `RazorCompileOnPublish` MSBuild özellikleri devre dışı bırakılmışsa varsayılan olarak sunulmuyor.</span><span class="sxs-lookup"><span data-stu-id="46acc-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages aren't served by default if the `RazorCompileOnBuild` or `RazorCompileOnPublish` MSBuild properties in the project file are disabled.</span></span> <span data-ttu-id="46acc-141">Uygulama, *. cshtml* dosyalarını işlemek üzere çalışma zamanı derlemesini kullanıyorsa, uygulamalar [Microsoft. Aspnetcore. Mvc. Razor. runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) paketine açık bir başvuru eklememelidir.</span><span class="sxs-lookup"><span data-stu-id="46acc-141">Applications must add an explicit reference to the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) package if the app relies on runtime compilation to process *.cshtml* files.</span></span>

::: moniker-end

| <span data-ttu-id="46acc-142">Öğeler</span><span class="sxs-lookup"><span data-stu-id="46acc-142">Items</span></span> | <span data-ttu-id="46acc-143">Açıklama</span><span class="sxs-lookup"><span data-stu-id="46acc-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="46acc-144">Kod oluşturmaya giriş olan öğe öğeleri ( *. cshtml* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="46acc-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="46acc-145">Razor bileşen kodu oluşturmaya giriş olan öğe öğeleri ( *. Razor* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="46acc-145">Item elements (*.razor* files) that are inputs to Razor component code generation.</span></span> |
| `RazorCompile` | <span data-ttu-id="46acc-146">Razor derleme hedeflerine giriş olan öğe öğeleri ( *. cs* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="46acc-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="46acc-147">Razor derlemesine derlenecek ek dosyaları belirtmek için bu `ItemGroup` kullanın.</span><span class="sxs-lookup"><span data-stu-id="46acc-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="46acc-148">Kod için kullanılan öğeler için Razor derleme öznitelikleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="46acc-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="46acc-149">Örnek:</span><span class="sxs-lookup"><span data-stu-id="46acc-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="46acc-150">Oluşturulan Razor derlemesine katıştırılmış kaynakları olarak eklenen öğeler.</span><span class="sxs-lookup"><span data-stu-id="46acc-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="46acc-151">Özellik</span><span class="sxs-lookup"><span data-stu-id="46acc-151">Property</span></span> | <span data-ttu-id="46acc-152">Açıklama</span><span class="sxs-lookup"><span data-stu-id="46acc-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="46acc-153">Razor tarafından üretilen derleme dosya adı (uzantısı olmadan).</span><span class="sxs-lookup"><span data-stu-id="46acc-153">File name (without extension) of the assembly produced by Razor.</span></span> |
| `RazorOutputPath` | <span data-ttu-id="46acc-154">Razor çıkış dizini.</span><span class="sxs-lookup"><span data-stu-id="46acc-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="46acc-155">Razor derlemesi oluşturmak için kullanılan araç kümesini belirlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="46acc-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="46acc-156">Geçerli değerler `Implicit`, `RazorSDK`ve `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="46acc-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="46acc-157">Enabledefaultcontentıtems</span><span class="sxs-lookup"><span data-stu-id="46acc-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="46acc-158">`true` varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="46acc-158">Default is `true`.</span></span> <span data-ttu-id="46acc-159">`true`, *Web. config*, *. JSON*ve *. cshtml* dosyalarını projeye içerik olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="46acc-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="46acc-160">`Microsoft.NET.Sdk.Web`aracılığıyla başvuruluyorsa, *Wwwroot* ve yapılandırma dosyaları altındaki dosyalar da dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="46acc-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="46acc-161">`true`, `RazorGenerate` öğelerinde `Content` öğelerden *. cshtml* dosyalarını ekler.</span><span class="sxs-lookup"><span data-stu-id="46acc-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="46acc-162">`true`, `RazorAssemblyAttribute` tarafından belirtilen öznitelikleri içeren bir *. cs* dosyası oluşturur ve derleme çıkışında dosyayı içerir.</span><span class="sxs-lookup"><span data-stu-id="46acc-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="46acc-163">`true`, `RazorAssemblyAttribute`derleme özniteliklerinin varsayılan bir kümesini ekler.</span><span class="sxs-lookup"><span data-stu-id="46acc-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="46acc-164">`true`, `RazorGenerate` öğeleri ( *. cshtml*) dosyalarını Yayımla dizinine kopyalar.</span><span class="sxs-lookup"><span data-stu-id="46acc-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="46acc-165">Genellikle, derleme zamanı veya yayımlama zamanı derleme katıldıkları, Razor dosyaları bir yayımlanan uygulama için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="46acc-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="46acc-166">`false` değerini varsayılan olarak alır.</span><span class="sxs-lookup"><span data-stu-id="46acc-166">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="46acc-167">`true`, başvuru derleme öğelerini yayımlama dizinine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="46acc-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="46acc-168">Genellikle, Razor derleme, derleme zamanı veya yayımlama zamanı oluşursa başvuru bütünleştirilmiş kodları yayınlanmış bir uygulama için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="46acc-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="46acc-169">Yayımlanmış uygulamanız çalışma zamanı derlemesi gerektiriyorsa `true` olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="46acc-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="46acc-170">Örneğin, uygulama çalışma zamanında *. cshtml* dosyalarını değiştirirse veya gömülü görünümleri kullanıyorsa değeri `true` olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="46acc-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="46acc-171">`false` değerini varsayılan olarak alır.</span><span class="sxs-lookup"><span data-stu-id="46acc-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="46acc-172">`true`, tüm Razor içerik öğeleri ( *. cshtml* dosyaları) oluşturulan NuGet paketine eklenmek üzere işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="46acc-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="46acc-173">`false` değerini varsayılan olarak alır.</span><span class="sxs-lookup"><span data-stu-id="46acc-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="46acc-174">`true`, oluşturulan Razor derlemesine gömülü dosyalar olarak RazorGenerate ( *. cshtml*) öğelerini ekler.</span><span class="sxs-lookup"><span data-stu-id="46acc-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="46acc-175">`false` değerini varsayılan olarak alır.</span><span class="sxs-lookup"><span data-stu-id="46acc-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="46acc-176">`true`, kod oluşturma işinin yükünü boşaltmak için kalıcı bir yapı sunucusu işlemi kullanır.</span><span class="sxs-lookup"><span data-stu-id="46acc-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="46acc-177">Varsayılan olarak `UseSharedCompilation`değerini alır.</span><span class="sxs-lookup"><span data-stu-id="46acc-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="46acc-178">`true`, SDK, uygulama bölümü keşfi gerçekleştirmek için çalışma zamanında MVC tarafından kullanılan ek öznitelikler üretir.</span><span class="sxs-lookup"><span data-stu-id="46acc-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="46acc-179">Web veya Razor SDK 'Yı hedefleyen projelerde `Content` öğesi grubundan çıkarılacak öğe öğeleri için glob bir model</span><span class="sxs-lookup"><span data-stu-id="46acc-179">A globbing pattern for item elements that are to be excluded from the `Content` item group in projects targeting the Web or Razor SDK</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="46acc-180">`true`, *. config* ve *. JSON* dosyaları yapı çıkış dizinine kopyalanmaz.</span><span class="sxs-lookup"><span data-stu-id="46acc-180">When `true`, *.config* and *.json* files do not get copied to the build output directory.</span></span> |
| `AddRazorSupportForMvc` | <span data-ttu-id="46acc-181">`true`,, MVC görünümlerini veya Razor Pages içeren uygulamalar oluştururken gerekli olan MVC yapılandırmasına yönelik destek eklemek için Razor SDK 'sını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="46acc-181">When `true`, configures the Razor SDK to add support for the MVC configuration that is required when building applications containing MVC views or Razor Pages.</span></span> <span data-ttu-id="46acc-182">Bu özellik, Web SDK 'sını hedefleyen .NET Core 3,0 veya üzeri projeler için örtük olarak ayarlanmıştır</span><span class="sxs-lookup"><span data-stu-id="46acc-182">This property is implicitly set for .NET Core 3.0 or later projects targeting the Web SDK</span></span> |
| `RazorLangVersion` | <span data-ttu-id="46acc-183">Hedeflenecek Razor dilinin sürümü.</span><span class="sxs-lookup"><span data-stu-id="46acc-183">The version of the Razor Language to target.</span></span> |

<span data-ttu-id="46acc-184">Özellikler hakkında daha fazla bilgi için bkz. [MSBuild özellikleri](/visualstudio/msbuild/msbuild-properties).</span><span class="sxs-lookup"><span data-stu-id="46acc-184">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="46acc-185">Hedefler</span><span class="sxs-lookup"><span data-stu-id="46acc-185">Targets</span></span>

<span data-ttu-id="46acc-186">Razor SDK'sı iki birincil hedefleri tanımlar:</span><span class="sxs-lookup"><span data-stu-id="46acc-186">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="46acc-187">`RazorGenerate` &ndash; kodu, `RazorGenerate` öğesi öğelerinden *. cs* dosyaları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="46acc-187">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="46acc-188">Bu hedeften önce veya sonra çalışabilecek ek hedefleri belirtmek için `RazorGenerateDependsOn` özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="46acc-188">Use the `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="46acc-189">`RazorCompile` &ndash; oluşturulan *. cs* dosyalarını Razor derlemesinde derler.</span><span class="sxs-lookup"><span data-stu-id="46acc-189">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="46acc-190">Bu hedeften önce veya sonra çalışabilecek ek hedefleri belirtmek için `RazorCompileDependsOn` kullanın.</span><span class="sxs-lookup"><span data-stu-id="46acc-190">Use the `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="46acc-191">`RazorComponentGenerate` &ndash; kod `RazorComponent` öğesi öğeleri için *. cs* dosyaları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="46acc-191">`RazorComponentGenerate` &ndash; Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="46acc-192">Bu hedeften önce veya sonra çalışabilecek ek hedefleri belirtmek için `RazorComponentGenerateDependsOn` özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="46acc-192">Use the `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="46acc-193">Razor görünüm çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="46acc-193">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="46acc-194">Varsayılan olarak, Razor SDK'sı, çalışma zamanı derleme gerçekleştirmek için gereken başvuru derlemelerini yayımlama değil.</span><span class="sxs-lookup"><span data-stu-id="46acc-194">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="46acc-195">Bu, uygulama modeli çalışma zamanı derlemesini kullandığında derleme hatalarıyla sonuçlanır&mdash;Örneğin, uygulama yayımlandıktan sonra uygulamanın katıştırılmış görünümleri veya değişiklik görünümlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="46acc-195">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="46acc-196">Başvuru derlemelerini yayımlamaya devam etmek için `CopyRefAssembliesToPublishDirectory` `true` olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="46acc-196">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="46acc-197">Bir Web uygulaması için uygulamanızın `Microsoft.NET.Sdk.Web` SDK 'Yı hedeflediğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="46acc-197">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="46acc-198">Razor dili sürümü</span><span class="sxs-lookup"><span data-stu-id="46acc-198">Razor language version</span></span>

<span data-ttu-id="46acc-199">`Microsoft.NET.Sdk.Web` SDK 'Sı hedeflenirken, Razor dili sürümü uygulamanın hedef Framework sürümünden algılanır.</span><span class="sxs-lookup"><span data-stu-id="46acc-199">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the app's target framework version.</span></span> <span data-ttu-id="46acc-200">`Microsoft.NET.Sdk.Razor` SDK 'Yı hedefleyen projeler veya uygulamanın çıkarılan değerden farklı bir Razor dili sürümü gerektirmesi durumunda, uygulamanın proje dosyasındaki `<RazorLangVersion>` özelliği ayarlanarak bir sürüm yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="46acc-200">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="46acc-201">Razor 'nin dil sürümü, için oluşturulduğu çalışma zamanının sürümü ile sıkı bir şekilde tümleşiktir.</span><span class="sxs-lookup"><span data-stu-id="46acc-201">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="46acc-202">Çalışma zamanı için tasarlanmamış bir dil sürümünü hedeflemek desteklenmez ve muhtemelen derleme hataları üretir.</span><span class="sxs-lookup"><span data-stu-id="46acc-202">Targeting a language version that isn't designed for the runtime is unsupported and likely produces build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="46acc-203">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="46acc-203">Additional resources</span></span>

* [<span data-ttu-id="46acc-204">.NET Core için csproj biçimine eklemeler</span><span class="sxs-lookup"><span data-stu-id="46acc-204">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="46acc-205">Ortak MSBuild proje öğeleri</span><span class="sxs-lookup"><span data-stu-id="46acc-205">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
