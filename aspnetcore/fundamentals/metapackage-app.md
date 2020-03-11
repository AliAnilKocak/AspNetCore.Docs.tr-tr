---
title: ASP.NET Core için Microsoft. AspNetCore. app metapackage
author: Rick-Anderson
description: Microsoft. AspNetCore. app Shared Framework
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: 3ce74bc7329a88ffc6f77baf6b8a311c02951318
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663144"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="51024-103">ASP.NET Core için Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="51024-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="51024-104">ASP.NET Core paylaşılan çerçeve (`Microsoft.AspNetCore.App`) Microsoft tarafından geliştirilen ve desteklenen derlemeler içerir.</span><span class="sxs-lookup"><span data-stu-id="51024-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="51024-105">`Microsoft.AspNetCore.App`, [.NET Core 3,0 veya sonraki BIR SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) yüklendiğinde yüklenir.</span><span class="sxs-lookup"><span data-stu-id="51024-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="51024-106">*Paylaşılan çerçeve* , makinede yüklü olan derlemeler ( *. dll* dosyaları) kümesidir ve bir çalışma zamanı bileşeni ve hedefleme paketi içerir.</span><span class="sxs-lookup"><span data-stu-id="51024-106">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="51024-107">Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="51024-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="51024-108">`Microsoft.NET.Sdk.Web` SDK 'Yı hedefleyen projeler `Microsoft.AspNetCore.App` çerçevesine örtülü olarak başvurur.</span><span class="sxs-lookup"><span data-stu-id="51024-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="51024-109">Bu projeler için ek başvuru gerekli değildir:</span><span class="sxs-lookup"><span data-stu-id="51024-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="51024-110">ASP.NET Core paylaşılan çerçeve:</span><span class="sxs-lookup"><span data-stu-id="51024-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="51024-111">Üçüncü taraf bağımlılıklarını içermez.</span><span class="sxs-lookup"><span data-stu-id="51024-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="51024-112">ASP.NET Core ekibi tarafından desteklenen tüm paketleri içerir.</span><span class="sxs-lookup"><span data-stu-id="51024-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="51024-113">Bu özellik, .NET Core 2. x 'i hedefleyen ASP.NET Core 2. x gerektirir.</span><span class="sxs-lookup"><span data-stu-id="51024-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="51024-114">ASP.NET Core için [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) :</span><span class="sxs-lookup"><span data-stu-id="51024-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="51024-115">[JSON.net](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion. LINQ](https://www.nuget.org/packages/Remotion.Linq/)ve [x zaman uyumsuz](https://www.nuget.org/packages/System.Interactive.Async/)dışında üçüncü taraf bağımlılıklarını içermez.</span><span class="sxs-lookup"><span data-stu-id="51024-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="51024-116">Bu üçüncü taraf bağımlılıklar, ana çerçeveler özelliklerinin çalışmasını sağlamak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="51024-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="51024-117">ASP.NET Core ekibine, üçüncü taraf bağımlılıklar (daha önce bahsedilen) dışında, desteklenen tüm paketleri içerir.</span><span class="sxs-lookup"><span data-stu-id="51024-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="51024-118">Entity Framework Core ekibine, üçüncü taraf bağımlılıklar (daha önce bahsedilen) dışında, desteklenen tüm paketleri içerir.</span><span class="sxs-lookup"><span data-stu-id="51024-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="51024-119">ASP.NET Core 2. x ve Entity Framework Core 2. x ' in tüm özellikleri `Microsoft.AspNetCore.App` paketine dahildir.</span><span class="sxs-lookup"><span data-stu-id="51024-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="51024-120">ASP.NET Core 2. x ' i hedefleyen varsayılan proje şablonları bu paketi kullanın.</span><span class="sxs-lookup"><span data-stu-id="51024-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="51024-121">ASP.NET Core 2. x ve Entity Framework Core 2. x ' i hedefleyen uygulamaların `Microsoft.AspNetCore.App` paketini kullanması önerilir.</span><span class="sxs-lookup"><span data-stu-id="51024-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="51024-122">`Microsoft.AspNetCore.App` metapackage sürüm numarası, en düşük ASP.NET Core sürümü ve Entity Framework Core sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="51024-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="51024-123">`Microsoft.AspNetCore.App` metapackage 'in kullanılması, uygulamanızı koruyan sürüm kısıtlamalarını sağlar:</span><span class="sxs-lookup"><span data-stu-id="51024-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="51024-124">`Microsoft.AspNetCore.App`bir pakette geçişli (doğrudan) bağımlılığı olan bir paket varsa ve bu sürüm numaraları farklıysa, NuGet bir hata oluşturur.</span><span class="sxs-lookup"><span data-stu-id="51024-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="51024-125">Uygulamanıza eklenen diğer paketler, `Microsoft.AspNetCore.App`yer alan paketlerin sürümünü değiştiremez.</span><span class="sxs-lookup"><span data-stu-id="51024-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="51024-126">Sürüm tutarlılığı, güvenilir bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="51024-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="51024-127">`Microsoft.AspNetCore.App`, ilişkili bitlerin test edilmemiş sürüm birleşimlerinin aynı uygulamada birlikte kullanılmaları önleyecek şekilde tasarlandı.</span><span class="sxs-lookup"><span data-stu-id="51024-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="51024-128">`Microsoft.AspNetCore.App` metapackage kullanan uygulamalar, ASP.NET Core paylaşılan çerçeveden otomatik olarak faydalanır.</span><span class="sxs-lookup"><span data-stu-id="51024-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="51024-129">`Microsoft.AspNetCore.App` metapackage 'yi kullandığınızda, başvurulan ASP.NET Core NuGet paketlerindeki **hiçbir** varlık uygulama ile dağıtılır&mdash;ASP.NET Core paylaşılan Framework bu varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="51024-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="51024-130">Paylaşılan çerçevede bulunan varlıklar, uygulama başlatma süresini artırmak için önceden derlenmiş.</span><span class="sxs-lookup"><span data-stu-id="51024-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="51024-131">Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="51024-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="51024-132">Aşağıdaki proje dosyası, ASP.NET Core için `Microsoft.AspNetCore.App` metapackage öğesine başvurur ve tipik bir ASP.NET Core 2,2 şablonunu temsil eder:</span><span class="sxs-lookup"><span data-stu-id="51024-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="51024-133">Yukarıdaki biçimlendirme tipik bir ASP.NET Core 2. x şablonunu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="51024-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="51024-134">`Microsoft.AspNetCore.App` paketi başvurusu için bir sürüm numarası belirtmez.</span><span class="sxs-lookup"><span data-stu-id="51024-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="51024-135">Sürüm belirtilmediğinde, SDK tarafından [örtük](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) bir sürüm belirtilir, diğer bir deyişle `Microsoft.NET.Sdk.Web`.</span><span class="sxs-lookup"><span data-stu-id="51024-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="51024-136">SDK tarafından belirtilen örtük sürüme güvenmek ve paket başvurusunda sürüm numarasını açıkça ayarlamamanız önerilir.</span><span class="sxs-lookup"><span data-stu-id="51024-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="51024-137">Bu yaklaşım hakkında sorularınız varsa, [Microsoft. AspNetCore. app örtük sürümü Için tartışmada](https://github.com/dotnet/AspNetCore.Docs/issues/6430)bir GitHub yorumu bırakın.</span><span class="sxs-lookup"><span data-stu-id="51024-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="51024-138">Örtük sürüm, taşınabilir uygulamalar için `major.minor.0` olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="51024-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="51024-139">Paylaşılan Framework toplaması-iletme mekanizması, uygulamayı yüklü paylaşılan Çerçeveler arasındaki en son uyumlu sürümde çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="51024-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="51024-140">Geliştirme, test ve üretimde aynı sürümün kullanıldığını güvence altına almak için, paylaşılan Framework 'ün aynı sürümünün tüm ortamlarda yüklü olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="51024-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="51024-141">Kendi içindeki uygulamalar için, örtük sürüm numarası yüklü SDK 'da paketlenmiş paylaşılan Framework `major.minor.patch` ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="51024-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="51024-142">`Microsoft.AspNetCore.App` başvurusunda bir sürüm numarası belirtilmesi, paylaşılan Çerçeve sürümünün seçilmeyeceği garantisi **vermez** .</span><span class="sxs-lookup"><span data-stu-id="51024-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="51024-143">Örneğin, "2.2.1" sürümünün belirtildiğini, ancak "2.2.3" nin yüklü olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="51024-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="51024-144">Bu durumda, uygulama "2.2.3" kullanacaktır.</span><span class="sxs-lookup"><span data-stu-id="51024-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="51024-145">Önerilmese de, iletmeyi (Patch ve/veya Minor) devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51024-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="51024-146">DotNet ana bilgisayar alma hakkında daha fazla bilgi ve davranışını yapılandırma hakkında daha fazla bilgi için bkz. [DotNet Host top Forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="51024-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="51024-147">örtük sürüm `Microsoft.AspNetCore.App`kullanmak için `<Project Sdk` `Microsoft.NET.Sdk.Web` olarak ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="51024-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="51024-148">`<Project Sdk="Microsoft.NET.Sdk">` (sondaki `.Web`olmadan) kullanıldığında:</span><span class="sxs-lookup"><span data-stu-id="51024-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="51024-149">Aşağıdaki uyarı oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="51024-149">The following warning is generated:</span></span>

  <span data-ttu-id="51024-150">*Uyarı NU1604: proje bağımlılığı Microsoft. AspNetCore. uygulama, kapsamlı bir alt sınır içermez. Tutarlı geri yükleme sonuçlarının sağlanması için bağımlılık sürümüne bir alt sınır ekleyin.*</span><span class="sxs-lookup"><span data-stu-id="51024-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="51024-151">Bu, .NET Core 2,1 SDK ile ilgili bilinen bir sorundur.</span><span class="sxs-lookup"><span data-stu-id="51024-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="51024-152">Güncelleştirme ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51024-152">Update ASP.NET Core</span></span>

<span data-ttu-id="51024-153">`Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) , NuGet 'den güncelleştirilmiş geleneksel bir paket değildir.</span><span class="sxs-lookup"><span data-stu-id="51024-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="51024-154">`Microsoft.NETCore.App`benzer şekilde, `Microsoft.AspNetCore.App`, NuGet dışında işlenen özel sürüm oluşturma semantiğinin bulunduğu paylaşılan bir çalışma zamanını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="51024-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="51024-155">Daha fazla bilgi için bkz. [paketler, Metapackages ve çerçeveler](/dotnet/core/packages).</span><span class="sxs-lookup"><span data-stu-id="51024-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="51024-156">ASP.NET Core güncelleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="51024-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="51024-157">Geliştirme makinelerinde ve yapı sunucularında: [.NET Core SDK](https://www.microsoft.com/net/download)indirin ve yükleyin.</span><span class="sxs-lookup"><span data-stu-id="51024-157">On development machines and build servers: Download and install the [.NET Core SDK](https://www.microsoft.com/net/download).</span></span>
* <span data-ttu-id="51024-158">Dağıtım sunucularında: [.NET Core çalışma zamanını](https://www.microsoft.com/net/download)indirin ve yükleyin.</span><span class="sxs-lookup"><span data-stu-id="51024-158">On deployment servers: Download and install the [.NET Core runtime](https://www.microsoft.com/net/download).</span></span>

 <span data-ttu-id="51024-159">Uygulamalar, uygulama yeniden başlatıldığında en son yüklenen sürüme ileri alınacaktır.</span><span class="sxs-lookup"><span data-stu-id="51024-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="51024-160">Proje dosyasındaki `Microsoft.AspNetCore.App` sürüm numarasını güncelleştirmek gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="51024-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="51024-161">Daha fazla bilgi için bkz. [çerçeveye bağımlı uygulamalar ileri alma](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span><span class="sxs-lookup"><span data-stu-id="51024-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="51024-162">Uygulamanız `Microsoft.AspNetCore.All`önceden kullandıysanız [Microsoft. AspNetCore. All 'Dan Microsoft. aspnetcore. App ' e geçiş](xref:fundamentals/metapackage#migrate)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="51024-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
