---
title: ASP.NET Core uygulama dağıtımı için Visual Studio yayımlama profilleri
author: rick-anderson
description: Visual Studio 'da yayımlama profilleri oluşturmayı ve bunları çeşitli hedeflere ASP.NET Core uygulama dağıtımlarını yönetmek için kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/12/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: a3d6cc450e42d7eb6b694cd4985828ce52fa7519
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333760"
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a><span data-ttu-id="b3c44-103">ASP.NET Core uygulama dağıtımı için Visual Studio yayımlama profilleri</span><span class="sxs-lookup"><span data-stu-id="b3c44-103">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>

<span data-ttu-id="b3c44-104">[Sayed Ibrampahashve](https://github.com/sayedihashimi) [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="b3c44-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b3c44-105">Bu belge, yayımlama profillerinin oluşturulması ve kullanılması için Visual Studio 2019 veya sonraki bir sürümü kullanılarak odaklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-105">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="b3c44-106">Visual Studio ile oluşturulan yayımlama profilleri MSBuild ve Visual Studio ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-106">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="b3c44-107">Azure 'da yayımlama yönergeleri için bkz. <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="b3c44-107">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="b3c44-108">@No__t-0 komutu, aşağıdaki kök düzeyi [\<proje > öğesini](/visualstudio/msbuild/project-element-msbuild)içeren bir proje dosyası üretir:</span><span class="sxs-lookup"><span data-stu-id="b3c44-108">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="b3c44-109">Önceki `<Project>` öğesinin `Sdk` özniteliği, *$ (msbuildsdkspath) \Microsoft.net.SDK.Web\Sdk\Sdk.props* ve $ (msbuildsdkspath) konumundan MSBuild [özelliklerini](/visualstudio/msbuild/msbuild-properties) ve [hedeflerini](/visualstudio/msbuild/msbuild-targets) içeri aktarır *\Microsoft.net.SDK.Web\Sdk\ SDK. targets*, sırasıyla.</span><span class="sxs-lookup"><span data-stu-id="b3c44-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="b3c44-110">@No__t-0 (Visual Studio 2019 Enterprise ile) için varsayılan konum *% ProgramFiles (x86)% \ Microsoft Visual Studio\2019\enterprise\msbuild\sdk* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="b3c44-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="b3c44-111">`Microsoft.NET.Sdk.Web` (Web SDK), `Microsoft.NET.Sdk` (.NET Core SDK) ve `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)) dahil diğer SDK 'lara bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-111">`Microsoft.NET.Sdk.Web` (Web SDK) depends on other SDKs, including `Microsoft.NET.Sdk` (.NET Core SDK) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="b3c44-112">Her bağımlı SDK ile ilişkili MSBuild özellikleri ve hedefleri içeri aktarılır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="b3c44-113">Yayımlama hedefleri, kullanılan Yayımla yöntemine göre uygun hedef kümesini içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="b3c44-114">MSBuild veya Visual Studio bir projeyi yüklediğinde, aşağıdaki üst düzey eylemler gerçekleşir:</span><span class="sxs-lookup"><span data-stu-id="b3c44-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="b3c44-115">Projeyi oluştur</span><span class="sxs-lookup"><span data-stu-id="b3c44-115">Build project</span></span>
* <span data-ttu-id="b3c44-116">Yayımlanacak işlem dosyaları</span><span class="sxs-lookup"><span data-stu-id="b3c44-116">Compute files to publish</span></span>
* <span data-ttu-id="b3c44-117">Dosyaları hedefe Yayımla</span><span class="sxs-lookup"><span data-stu-id="b3c44-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="b3c44-118">İşlem projesi öğeleri</span><span class="sxs-lookup"><span data-stu-id="b3c44-118">Compute project items</span></span>

<span data-ttu-id="b3c44-119">Proje yüklendiğinde, [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) (dosyalar) hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="b3c44-120">Öğe türü, dosyanın nasıl işlendiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="b3c44-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="b3c44-121">Varsayılan olarak, *. cs* dosyaları `Compile` öğe listesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="b3c44-122">@No__t-0 öğe listesindeki dosyalar derlenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="b3c44-123">@No__t-0 öğe listesi, derleme çıktılarına ek olarak yayımlanan dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="b3c44-124">Varsayılan olarak, `wwwroot\**`, `**\*.config` ve `**\*.json` desenleriyle eşleşen dosyalar `Content` öğe listesine dahildir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="b3c44-125">Örneğin, `wwwroot\**` [Glob deseninin](https://gruntjs.com/configuring-tasks#globbing-patterns) *Wwwroot* klasörü ve alt klasörlerindeki tüm dosyalar eşleşir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b3c44-126">Web SDK 'Sı [Razor SDK 'sını](xref:razor-pages/sdk)içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-126">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="b3c44-127">Sonuç olarak, `**\*.cshtml` ve `**\*.razor` desenleriyle eşleşen dosyalar da `Content` öğe listesine dahil edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="b3c44-128">Web SDK 'Sı [Razor SDK 'sını](xref:razor-pages/sdk)içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-128">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="b3c44-129">Sonuç olarak, `**\*.cshtml` düzeniyle eşleşen dosyalar `Content` öğe listesine de dahildir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="b3c44-130">Yayınlama listesine açıkça bir dosya eklemek için, dosyayı, [dosyaları dahil et](#include-files) bölümünde gösterildiği gibi doğrudan *. csproj* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b3c44-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="b3c44-131">Visual Studio 'da veya komut satırından yayımlarken **Yayımla** düğmesini seçerken:</span><span class="sxs-lookup"><span data-stu-id="b3c44-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="b3c44-132">Özellikler/öğeler hesaplanır (oluşturmak için gereken dosyalar).</span><span class="sxs-lookup"><span data-stu-id="b3c44-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="b3c44-133">**Yalnızca Visual Studio**: NuGet paketleri geri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="b3c44-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="b3c44-134">(Geri yüklemenin CLı üzerinde kullanıcı tarafından açık olması gerekir.)</span><span class="sxs-lookup"><span data-stu-id="b3c44-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="b3c44-135">Proje oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b3c44-135">The project builds.</span></span>
* <span data-ttu-id="b3c44-136">Yayımlama öğeleri hesaplanır (yayımlamak için gereken dosyalar).</span><span class="sxs-lookup"><span data-stu-id="b3c44-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="b3c44-137">Proje yayımlandı (hesaplanan dosyalar yayımlama hedefine kopyalanır).</span><span class="sxs-lookup"><span data-stu-id="b3c44-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="b3c44-138">Bir ASP.NET Core projesi Proje dosyasında `Microsoft.NET.Sdk.Web` ' a başvurduğunda, Web uygulaması dizininin köküne bir *app_offline. htm* dosyası yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="b3c44-139">Dosya olduğunda, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatır ve dağıtım sırasında *app_offline. htm* dosyasına hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="b3c44-140">Daha fazla bilgi için [ASP.NET Core modülü yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.</span><span class="sxs-lookup"><span data-stu-id="b3c44-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="b3c44-141">Temel komut satırı yayımlama</span><span class="sxs-lookup"><span data-stu-id="b3c44-141">Basic command-line publishing</span></span>

<span data-ttu-id="b3c44-142">Komut satırı yayımlama, .NET Core tarafından desteklenen tüm platformlarda çalışmaktadır ve Visual Studio 'Yu gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="b3c44-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="b3c44-143">Aşağıdaki örneklerde .NET Core CLI [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutu proje dizininden çalıştırılır ( *. csproj* dosyasını içerir).</span><span class="sxs-lookup"><span data-stu-id="b3c44-143">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="b3c44-144">Proje klasörü geçerli çalışma dizini değilse, proje dosyası yolunda açıkça geçiş yapın.</span><span class="sxs-lookup"><span data-stu-id="b3c44-144">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="b3c44-145">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b3c44-145">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="b3c44-146">Bir Web uygulaması oluşturmak ve yayımlamak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b3c44-146">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="b3c44-147">@No__t-0 komutu aşağıdaki çıkışın bir varyasyonunu üretir:</span><span class="sxs-lookup"><span data-stu-id="b3c44-147">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="b3c44-148">Varsayılan yayımlama klasörü biçimi *bin\Debug @ no__t-1 {Target Framework takma adı} \publish @ no__t-2*' dir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-148">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="b3c44-149">Örneğin, *Bin\debug\netcoreapp2,2\publish @ no__t-1*.</span><span class="sxs-lookup"><span data-stu-id="b3c44-149">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="b3c44-150">Aşağıdaki komut `Release` derlemesini ve yayımlama dizinini belirtir:</span><span class="sxs-lookup"><span data-stu-id="b3c44-150">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="b3c44-151">@No__t-0 komutu, `Publish` hedefini çağıran MSBuild 'i çağırır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-151">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="b3c44-152">@No__t-0 ' a geçirilen parametreler MSBuild 'e geçirilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-152">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="b3c44-153">@No__t-0 ve `-o` parametreleri sırasıyla MSBuild 'in `Configuration` ve `OutputPath` özelliklerine eşlenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-153">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="b3c44-154">MSBuild özellikleri aşağıdaki biçimlerden birini kullanarak geçirilebilir:</span><span class="sxs-lookup"><span data-stu-id="b3c44-154">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="b3c44-155">Örneğin, aşağıdaki komut bir ağ paylaşımında `Release` derlemesi yayımlar.</span><span class="sxs-lookup"><span data-stu-id="b3c44-155">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="b3c44-156">Ağ paylaşma, eğik çizgiler (/*saat*) ile belirtilir ve tüm .NET Core desteklenen platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-156">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="b3c44-157">Dağıtım için yayımlanan uygulamanın çalışmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="b3c44-157">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="b3c44-158">*Yayımla* klasöründeki dosyalar, uygulama çalışırken kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-158">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="b3c44-159">Kilitli dosyalar kopyalanamadığından dağıtım gerçekleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="b3c44-159">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="b3c44-160">Yayımlama profilleri</span><span class="sxs-lookup"><span data-stu-id="b3c44-160">Publish profiles</span></span>

<span data-ttu-id="b3c44-161">Bu bölüm, bir yayımlama profili oluşturmak için Visual Studio 2019 veya üstünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-161">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="b3c44-162">Profil oluşturulduktan sonra, Visual Studio 'dan veya komut satırından yayımlama kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-162">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="b3c44-163">Yayımlama profilleri Yayımlama sürecini basitleştirebilir ve herhangi bir sayıda profil bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-163">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="b3c44-164">Aşağıdaki yollardan birini seçerek Visual Studio 'da bir yayımlama profili oluşturun:</span><span class="sxs-lookup"><span data-stu-id="b3c44-164">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="b3c44-165">**Çözüm Gezgini** projeye sağ tıklayın ve **Yayımla**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="b3c44-165">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="b3c44-166">**Build** menüsünden **{Project Name} Yayımla** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="b3c44-166">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="b3c44-167">Uygulama özellikleri sayfasının **Yayımla** sekmesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-167">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="b3c44-168">Projenin bir yayımlama profili yoksa, **bir yayımlama hedefi seçin** sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-168">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="b3c44-169">Aşağıdaki yayımlama hedeflerinden birini seçmeniz istenir:</span><span class="sxs-lookup"><span data-stu-id="b3c44-169">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="b3c44-170">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b3c44-170">Azure App Service</span></span>
* <span data-ttu-id="b3c44-171">Linux üzerinde Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b3c44-171">Azure App Service on Linux</span></span>
* <span data-ttu-id="b3c44-172">Azure sanal makineleri</span><span class="sxs-lookup"><span data-stu-id="b3c44-172">Azure Virtual Machines</span></span>
* <span data-ttu-id="b3c44-173">Klasör</span><span class="sxs-lookup"><span data-stu-id="b3c44-173">Folder</span></span>
* <span data-ttu-id="b3c44-174">IIS, FTP, Web Dağıtımı (herhangi bir Web sunucusu için)</span><span class="sxs-lookup"><span data-stu-id="b3c44-174">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="b3c44-175">Profili içeri aktar</span><span class="sxs-lookup"><span data-stu-id="b3c44-175">Import Profile</span></span>

<span data-ttu-id="b3c44-176">En uygun yayımlama hedefini belirlemek için, [hangi yayımlama seçeneklerinin benim için](/visualstudio/ide/not-in-toc/web-publish-options)uygun olduğunu öğrenin.</span><span class="sxs-lookup"><span data-stu-id="b3c44-176">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="b3c44-177">Hedef Yayımla **klasörü** seçildiğinde, yayımlanmış varlıkları depolamak için bir klasör yolu belirtin.</span><span class="sxs-lookup"><span data-stu-id="b3c44-177">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="b3c44-178">Varsayılan klasör yolu *bin @ no__t-1 {Project CONFIGURATION} \\ {Target Framework takma adı} \publish @ no__t-3*şeklindedir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-178">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="b3c44-179">Örneğin, *Bin\release\netcoreapp2,2\publish @ no__t-1*.</span><span class="sxs-lookup"><span data-stu-id="b3c44-179">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="b3c44-180">Tamamlanacak **Profil oluştur** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="b3c44-180">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="b3c44-181">Bir yayımlama profili oluşturulduktan sonra, **Yayımla** sekmesinin içeriği değişir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-181">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="b3c44-182">Yeni oluşturulan profil bir açılan listede görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="b3c44-183">Aşağı açılan listenin altında **Yeni profil** oluştur ' u seçerek yeni bir profil oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b3c44-183">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="b3c44-184">Visual Studio 'nun yayımlama aracı, yayımlama profilini açıklayan bir *Özellikler/PublishProfiles/{PROFILE Name}. pubxml* MSBuild dosyası oluşturuyor.</span><span class="sxs-lookup"><span data-stu-id="b3c44-184">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="b3c44-185">*. Pubxml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="b3c44-185">The *.pubxml* file:</span></span>

* <span data-ttu-id="b3c44-186">Yayımlama yapılandırma ayarlarını içerir ve yayımlama işlemi tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-186">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="b3c44-187">Derleme ve yayımlama işlemini özelleştirmek için değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-187">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="b3c44-188">Azure hedefine yayımlarken, *. pubxml* dosyası Azure abonelik tanımlarınızı içerir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-188">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="b3c44-189">Bu hedef türünde, bu dosyayı kaynak denetimine eklemek önerilmez.</span><span class="sxs-lookup"><span data-stu-id="b3c44-189">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="b3c44-190">Azure olmayan bir hedefe yayımlarken, *. pubxml* dosyasını denetlemek güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-190">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="b3c44-191">Gizli bilgiler (yayımlama parolası gibi) Kullanıcı/makine düzeyinde şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-191">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="b3c44-192">*Özellikler/PublishProfiles/{PROFILE Name}. pubxml. User* dosyasında depolanır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-192">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="b3c44-193">Bu dosya hassas bilgileri depolayabildiğinden, kaynak denetimine denetlenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-193">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="b3c44-194">ASP.NET Core Web uygulaması yayımlama hakkında genel bakış için, bkz. <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="b3c44-194">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="b3c44-195">ASP.NET Core Web uygulaması yayımlamak için gereken MSBuild görevleri ve hedefleri, [ASPNET/WebSDK deposunda](https://github.com/aspnet/websdk)açık kaynaktır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-195">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="b3c44-196">Aşağıdaki komutlar Folder, MSDeploy ve [kudu](https://github.com/projectkudu/kudu/wiki) yayımlama profillerini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-196">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="b3c44-197">MSDeploy platformlar arası destek olmadığından, aşağıdaki MSDeploy seçenekleri yalnızca Windows 'da desteklenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-197">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="b3c44-198">**Klasör (platformlar arası):**</span><span class="sxs-lookup"><span data-stu-id="b3c44-198">**Folder (works cross-platform):**</span></span>

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="b3c44-199">**MSDeploy**</span><span class="sxs-lookup"><span data-stu-id="b3c44-199">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="b3c44-200">**MSDeploy paketi:**</span><span class="sxs-lookup"><span data-stu-id="b3c44-200">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="b3c44-201">Yukarıdaki örneklerde:</span><span class="sxs-lookup"><span data-stu-id="b3c44-201">In the preceding examples:</span></span>

* <span data-ttu-id="b3c44-202">`dotnet publish` ve `dotnet build`, Azure 'da herhangi bir platformda yayımlamak üzere kudu API 'Lerini destekler.</span><span class="sxs-lookup"><span data-stu-id="b3c44-202">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="b3c44-203">Visual Studio yayımlama, kudu API 'Lerini destekler, ancak Azure 'da platformlar arası yayımlama için WebSDK tarafından desteklenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-203">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="b3c44-204">@No__t-0 ' i `dotnet publish` komutuna geçirmeyin.</span><span class="sxs-lookup"><span data-stu-id="b3c44-204">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="b3c44-205">Daha fazla bilgi için bkz. [Microsoft. net. SDK. Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="b3c44-205">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="b3c44-206">Projenin *Properties/PublishProfiles* klasörüne aşağıdaki içeriğe sahip bir yayımlama profili ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b3c44-206">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a><span data-ttu-id="b3c44-207">Klasör Yayımla örneği</span><span class="sxs-lookup"><span data-stu-id="b3c44-207">Folder publish example</span></span>

<span data-ttu-id="b3c44-208">*Folderprofile*adlı bir profille yayımlarken, aşağıdaki komutlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="b3c44-208">When publishing with a profile named *FolderProfile*, use either of the following commands:</span></span>

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="b3c44-209">.NET Core CLI [DotNet derleme](/dotnet/core/tools/dotnet-build) komutu, derleme ve yayımlama işlemini çalıştırmak için `msbuild` ' i çağırır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-209">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="b3c44-210">@No__t-0 ve `msbuild` komutları, bir klasör profilinde geçirilerek eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-210">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="b3c44-211">@No__t-0 doğrudan Windows üzerinde çağrılırken, MSBuild 'in .NET Framework sürümü kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-211">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="b3c44-212">Klasör olmayan bir profilde `dotnet build` çağrılıyor:</span><span class="sxs-lookup"><span data-stu-id="b3c44-212">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="b3c44-213">MSDeploy kullanan `msbuild` ' ı çağırır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-213">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="b3c44-214">Hataya neden olur (Windows üzerinde çalışırken bile).</span><span class="sxs-lookup"><span data-stu-id="b3c44-214">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="b3c44-215">Klasör olmayan bir profille yayımlamak için, doğrudan `msbuild` ' ı çağırın.</span><span class="sxs-lookup"><span data-stu-id="b3c44-215">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="b3c44-216">Aşağıdaki klasör yayımlama profili, Visual Studio ile oluşturulmuştur ve bir ağ paylaşımında yayımlar:</span><span class="sxs-lookup"><span data-stu-id="b3c44-216">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="b3c44-217">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="b3c44-217">In the preceding example:</span></span>

* <span data-ttu-id="b3c44-218">@No__t-0 özelliği yalnızca bir XML şeması gereksinimini karşılamak için vardır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-218">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="b3c44-219">@No__t-0 özelliğinin, proje kökünde *App_Data* klasörü olsa bile, yayımlama işlemi üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="b3c44-219">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="b3c44-220">*App_Data* klasörü, ASP.NET 4. x projelerinde olduğu gibi özel bir işleme almaz.</span><span class="sxs-lookup"><span data-stu-id="b3c44-220">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* <span data-ttu-id="b3c44-221">@No__t-0 özelliği `Release` olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-221">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="b3c44-222">Visual Studio 'dan yayımlarken, `<LastUsedBuildConfiguration>` değeri, yayımlama işlemi başlatıldığında değeri kullanılarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-222">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="b3c44-223">`<LastUsedBuildConfiguration>` özeldir ve içeri aktarılan MSBuild dosyasında geçersiz kılınmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-223">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="b3c44-224">Ancak, bu özellik aşağıdaki yaklaşımlardan birini kullanarak komut satırından geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-224">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="b3c44-225">.NET Core CLI kullanarak:</span><span class="sxs-lookup"><span data-stu-id="b3c44-225">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="b3c44-226">MSBuild 'i kullanma:</span><span class="sxs-lookup"><span data-stu-id="b3c44-226">Using MSBuild:</span></span>

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="b3c44-227">Daha fazla bilgi için bkz. [MSBuild: yapılandırma özelliğini ayarlama](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="b3c44-227">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="b3c44-228">Komut satırından bir MSDeploy uç noktasına yayımlama</span><span class="sxs-lookup"><span data-stu-id="b3c44-228">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="b3c44-229">Aşağıdaki örnek, *AzureWebApp*adlı Visual Studio tarafından oluşturulan bir ASP.NET Core Web uygulamasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-229">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="b3c44-230">Visual Studio ile bir Azure Apps yayımlama profili eklenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-230">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="b3c44-231">Profil oluşturma hakkında daha fazla bilgi için, [Yayımlama profilleri](#publish-profiles) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b3c44-231">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="b3c44-232">Uygulamayı bir yayımlama profili kullanarak dağıtmak için, bir Visual Studio **Geliştirici Komut İstemi**`msbuild` komutunu yürütün.</span><span class="sxs-lookup"><span data-stu-id="b3c44-232">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="b3c44-233">Komut istemi, Windows görev çubuğundaki **Başlat** menüsünün *Visual Studio* klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="b3c44-233">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="b3c44-234">Daha kolay erişim için, Visual Studio 'daki **Araçlar** menüsüne komut istemi ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b3c44-234">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="b3c44-235">Daha fazla bilgi için bkz. [Visual Studio için geliştirici komut istemi](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="b3c44-235">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="b3c44-236">MSBuild aşağıdaki komut sözdizimini kullanır:</span><span class="sxs-lookup"><span data-stu-id="b3c44-236">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="b3c44-237">{PATH} @no__t-uygulamanın proje dosyasının 0 yolu.</span><span class="sxs-lookup"><span data-stu-id="b3c44-237">{PATH} &ndash; Path to the app's project file.</span></span>
* <span data-ttu-id="b3c44-238">{PROFILE} @no__t-yayımlama profilinin 0 adı.</span><span class="sxs-lookup"><span data-stu-id="b3c44-238">{PROFILE} &ndash; Name of the publish profile.</span></span>
* <span data-ttu-id="b3c44-239">{USERNAME} &ndash; MSDeploy Kullanıcı adı.</span><span class="sxs-lookup"><span data-stu-id="b3c44-239">{USERNAME} &ndash; MSDeploy username.</span></span> <span data-ttu-id="b3c44-240">{USERNAME}, yayımlama profilinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-240">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="b3c44-241">{PASSWORD} &ndash; MSDeploy parolası.</span><span class="sxs-lookup"><span data-stu-id="b3c44-241">{PASSWORD} &ndash; MSDeploy password.</span></span> <span data-ttu-id="b3c44-242">{PROFILE} öğesinden {PASSWORD} öğesini edinin *. PublishSettings* dosyası.</span><span class="sxs-lookup"><span data-stu-id="b3c44-242">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="b3c44-243">' Nı indirin *. PublishSettings* dosyası şunlardan biri:</span><span class="sxs-lookup"><span data-stu-id="b3c44-243">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="b3c44-244">**Çözüm Gezgini**: **Görünüm** > **bulut Gezgini**' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="b3c44-244">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="b3c44-245">Azure aboneliğinize bağlanın.</span><span class="sxs-lookup"><span data-stu-id="b3c44-245">Connect with your Azure subscription.</span></span> <span data-ttu-id="b3c44-246">**Uygulama hizmetleri**'ni açın.</span><span class="sxs-lookup"><span data-stu-id="b3c44-246">Open **App Services**.</span></span> <span data-ttu-id="b3c44-247">Uygulamaya sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b3c44-247">Right-click the app.</span></span> <span data-ttu-id="b3c44-248">**Yayımlama profilini indir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="b3c44-248">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="b3c44-249">Azure portal: Web uygulamasının **genel bakış** panelinde **Yayımlama profilini al** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="b3c44-249">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="b3c44-250">Aşağıdaki örnek, *AzureWebApp-Web dağıtımı*adlı bir yayımlama profili kullanır:</span><span class="sxs-lookup"><span data-stu-id="b3c44-250">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="b3c44-251">Bir yayımlama profili, Windows komut kabuğu 'ndan .NET Core CLI [DotNet MSBuild](/dotnet/core/tools/dotnet-msbuild) komutuyla da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="b3c44-251">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="b3c44-252">@No__t-0 komutu platformlar arası bir komuttur ve macOS ve Linux üzerinde ASP.NET Core uygulamalar derleyebilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-252">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="b3c44-253">Ancak, macOS ve Linux 'ta MSBuild, bir uygulamayı Azure 'a veya diğer MSDeploy uç noktalarına dağıtmıyor.</span><span class="sxs-lookup"><span data-stu-id="b3c44-253">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="b3c44-254">Ortamı ayarlama</span><span class="sxs-lookup"><span data-stu-id="b3c44-254">Set the environment</span></span>

<span data-ttu-id="b3c44-255">Uygulamanın [ortamını](xref:fundamentals/environments)ayarlamak için Publish profile ( *. pubxml*) veya proje dosyasında `<EnvironmentName>` özelliğini dahil edin:</span><span class="sxs-lookup"><span data-stu-id="b3c44-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="b3c44-256">*Web. config* dönüştürmelerine ihtiyaç duyuyorsanız (örneğin, yapılandırma, profil veya ortama göre ortam değişkenlerini ayarlamak), bkz. <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="b3c44-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="b3c44-257">Dosyaları Dışla</span><span class="sxs-lookup"><span data-stu-id="b3c44-257">Exclude files</span></span>

<span data-ttu-id="b3c44-258">ASP.NET Core Web Apps yayımlandığında, aşağıdaki varlıklar dahil edilmiştir:</span><span class="sxs-lookup"><span data-stu-id="b3c44-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="b3c44-259">Yapı yapıtları</span><span class="sxs-lookup"><span data-stu-id="b3c44-259">Build artifacts</span></span>
* <span data-ttu-id="b3c44-260">Aşağıdaki glob desenleriyle eşleşen klasörler ve dosyalar:</span><span class="sxs-lookup"><span data-stu-id="b3c44-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="b3c44-261">`**\*.config` (örneğin, *Web. config*)</span><span class="sxs-lookup"><span data-stu-id="b3c44-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="b3c44-262">`**\*.json` (örneğin, *appSettings. JSON*)</span><span class="sxs-lookup"><span data-stu-id="b3c44-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="b3c44-263">MSBuild, [Glob desenlerini](https://gruntjs.com/configuring-tasks#globbing-patterns)destekler.</span><span class="sxs-lookup"><span data-stu-id="b3c44-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="b3c44-264">Örneğin, aşağıdaki `<Content>` öğesi, metin ( *. txt*) dosyalarının *wwwroot\content* klasörüne ve alt klasörlerine kopyalanmasını önler:</span><span class="sxs-lookup"><span data-stu-id="b3c44-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="b3c44-265">Önceki biçimlendirme bir yayımlama profiline veya *. csproj* dosyasına eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="b3c44-266">*. Csproj* dosyasına eklendiğinde, kural projedeki tüm yayımlama profillerine eklenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="b3c44-267">Aşağıdaki `<MsDeploySkipRules>` öğesi, tüm dosyaları *wwwroot\content* klasöründen dışlar:</span><span class="sxs-lookup"><span data-stu-id="b3c44-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="b3c44-268">`<MsDeploySkipRules>`, dağıtım sitesinden *atlama* hedeflerini silmez.</span><span class="sxs-lookup"><span data-stu-id="b3c44-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="b3c44-269">`<Content>` hedefli dosya ve klasörler dağıtım sitesinden silinir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="b3c44-270">Örneğin, dağıtılan bir Web uygulamasının aşağıdaki dosyalar olduğunu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="b3c44-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="b3c44-271">*Görünümler/Home/about1. cshtml*</span><span class="sxs-lookup"><span data-stu-id="b3c44-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="b3c44-272">*Görünümler/Home/About2. cshtml*</span><span class="sxs-lookup"><span data-stu-id="b3c44-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="b3c44-273">*Görünümler/Home/About3. cshtml*</span><span class="sxs-lookup"><span data-stu-id="b3c44-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="b3c44-274">Aşağıdaki `<MsDeploySkipRules>` öğeleri eklendiyse, bu dosyalar dağıtım sitesinde silinmez.</span><span class="sxs-lookup"><span data-stu-id="b3c44-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="b3c44-275">Önceki `<MsDeploySkipRules>` öğeleri *Atlanan* dosyaların dağıtılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="b3c44-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="b3c44-276">Dağıtıldıktan sonra bu dosyaları silmez.</span><span class="sxs-lookup"><span data-stu-id="b3c44-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="b3c44-277">Aşağıdaki `<Content>` öğesi, dağıtım sitesindeki hedeflenen dosyaları siler:</span><span class="sxs-lookup"><span data-stu-id="b3c44-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="b3c44-278">Önceki `<Content>` öğesiyle komut satırı dağıtımını kullanmak aşağıdaki çıkışın bir varyasyonunu verir:</span><span class="sxs-lookup"><span data-stu-id="b3c44-278">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="b3c44-279">İçerme dosyaları</span><span class="sxs-lookup"><span data-stu-id="b3c44-279">Include files</span></span>

<span data-ttu-id="b3c44-280">Aşağıdaki bölümlerde, yayımlama zamanında dosya ekleme için farklı yaklaşımlar ana hatlarıyla verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-280">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="b3c44-281">[Genel dosya ekleme](#general-file-inclusion) bölümü, Web SDK 'sında bir Yayımla hedefi dosyası tarafından belirtilen `DotNetPublishFiles` öğesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-281">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the Web SDK.</span></span> <span data-ttu-id="b3c44-282">[Seçmeli dosya ekleme](#selective-file-inclusion) bölümü, .NET Core SDK bir Yayımla hedefi dosyası tarafından belirtilen `ResolvedFileToPublish` öğesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-282">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the .NET Core SDK.</span></span> <span data-ttu-id="b3c44-283">Web SDK .NET Core SDK bağlı olduğundan, her iki öğe bir ASP.NET Core projesinde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-283">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="b3c44-284">Genel dosya ekleme</span><span class="sxs-lookup"><span data-stu-id="b3c44-284">General file inclusion</span></span>

<span data-ttu-id="b3c44-285">Aşağıdaki örnek `<ItemGroup>` öğesi, proje dizininin dışında bulunan bir klasörü yayımlanmış sitenin bir klasörüne kopyalamayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-285">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="b3c44-286">Aşağıdaki biçimlendirmenin `<ItemGroup>` ' a eklenen dosyalar varsayılan olarak dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-286">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="b3c44-287">Yukarıdaki biçimlendirme:</span><span class="sxs-lookup"><span data-stu-id="b3c44-287">The preceding markup:</span></span>

* <span data-ttu-id="b3c44-288">*. Csproj* dosyasına veya yayımlama profiline eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-288">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="b3c44-289">*. Csproj* dosyasına eklenirse, bu, projedeki her bir yayımlama profiline eklenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-289">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="b3c44-290">@No__t-1 özniteliğinin glob düzeniyle eşleşen dosyaları depolamak için `_CustomFiles` öğesi bildirir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-290">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="b3c44-291">Düzende başvurulan *görüntüler* klasörü, proje dizininin dışında bulunur.</span><span class="sxs-lookup"><span data-stu-id="b3c44-291">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="b3c44-292">@No__t-1 adlı [ayrılmış bir özellik](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), proje dosyasının mutlak yoluna çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-292">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="b3c44-293">@No__t-0 öğesi için dosyaların bir listesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="b3c44-293">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="b3c44-294">Varsayılan olarak, öğenin `<DestinationRelativePath>` öğesi boştur.</span><span class="sxs-lookup"><span data-stu-id="b3c44-294">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="b3c44-295">Varsayılan değer, biçimlendirmede geçersiz kılınır ve `%(RecursiveDir)` gibi [iyi bilinen öğe meta verilerini](/visualstudio/msbuild/msbuild-well-known-item-metadata) kullanır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-295">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="b3c44-296">İç metin, yayımlanan sitenin *Wwwroot/görüntüler* klasörünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="b3c44-296">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="b3c44-297">Seçmeli dosya ekleme</span><span class="sxs-lookup"><span data-stu-id="b3c44-297">Selective file inclusion</span></span>

<span data-ttu-id="b3c44-298">Aşağıdaki örnekte vurgulanan biçimlendirme şunları göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="b3c44-298">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="b3c44-299">Projenin dışında bulunan bir dosyayı yayınlanan sitenin *Wwwroot* klasörüne kopyalama.</span><span class="sxs-lookup"><span data-stu-id="b3c44-299">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="b3c44-300">*ReadMe2.MD* dosyasının adı korunur.</span><span class="sxs-lookup"><span data-stu-id="b3c44-300">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="b3c44-301">*Wwwroot\content* klasörü dışlanıyor.</span><span class="sxs-lookup"><span data-stu-id="b3c44-301">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="b3c44-302">*Views\home\about2,cshtml*hariç tutulanıyor.</span><span class="sxs-lookup"><span data-stu-id="b3c44-302">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="b3c44-303">Yukarıdaki örnekte, varsayılan davranışı `Include` özniteliğinde sunulan dosyaları her zaman yayımlanan siteye kopyalamak olan `ResolvedFileToPublish` öğesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b3c44-303">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="b3c44-304">@No__t-1 veya `PreserveNewest` iç metniyle `<CopyToPublishDirectory>` alt öğesi ekleyerek varsayılan davranışı geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="b3c44-304">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="b3c44-305">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b3c44-305">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="b3c44-306">Daha fazla dağıtım örneği için bkz. [Web SDK deposu Benioku dosyası](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="b3c44-306">For more deployment samples, see the [Web SDK repository Readme](https://github.com/aspnet/websdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="b3c44-307">Yayımlamadan önce veya sonra bir hedef Çalıştır</span><span class="sxs-lookup"><span data-stu-id="b3c44-307">Run a target before or after publishing</span></span>

<span data-ttu-id="b3c44-308">Yerleşik `BeforePublish` ve `AfterPublish` hedefleri, yayımlama hedefinden önce veya sonra bir hedef yürütür.</span><span class="sxs-lookup"><span data-stu-id="b3c44-308">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="b3c44-309">Aşağıdaki öğeleri yayımlama profiline, yayımlamadan önce ve sonra da konsol iletilerini günlüğe kaydetmek için ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b3c44-309">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="b3c44-310">Güvenilmeyen bir sertifikayı kullanarak bir sunucuya yayımlama</span><span class="sxs-lookup"><span data-stu-id="b3c44-310">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="b3c44-311">Yayımlama profiline bir `True` değeri olan `<AllowUntrustedCertificate>` özelliğini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b3c44-311">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="b3c44-312">Kudu hizmeti</span><span class="sxs-lookup"><span data-stu-id="b3c44-312">The Kudu service</span></span>

<span data-ttu-id="b3c44-313">Azure App Service Web uygulaması dağıtımında dosyaları görüntülemek için [kudu hizmetini](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)kullanın.</span><span class="sxs-lookup"><span data-stu-id="b3c44-313">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="b3c44-314">@No__t-0 belirtecini Web uygulaması adına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b3c44-314">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="b3c44-315">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b3c44-315">For example:</span></span>

| <span data-ttu-id="b3c44-316">URL</span><span class="sxs-lookup"><span data-stu-id="b3c44-316">URL</span></span>                                    | <span data-ttu-id="b3c44-317">Sonuç</span><span class="sxs-lookup"><span data-stu-id="b3c44-317">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="b3c44-318">Web uygulaması</span><span class="sxs-lookup"><span data-stu-id="b3c44-318">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="b3c44-319">Kudu hizmeti</span><span class="sxs-lookup"><span data-stu-id="b3c44-319">Kudu service</span></span> |

<span data-ttu-id="b3c44-320">Dosyaları görüntülemek, düzenlemek, silmek veya eklemek için [hata ayıklama konsolu](https://github.com/projectkudu/kudu/wiki/Kudu-console) menü öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="b3c44-320">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3c44-321">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b3c44-321">Additional resources</span></span>

* <span data-ttu-id="b3c44-322">[Web dağıtımı](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy), IIS sunucularına Web uygulamaları ve Web siteleri dağıtımını basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="b3c44-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="b3c44-323">[Web SDK GitHub deposu](https://github.com/aspnet/websdk/issues): dağıtım için dosya sorunları ve istek özellikleri.</span><span class="sxs-lookup"><span data-stu-id="b3c44-323">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="b3c44-324">Visual Studio 'dan bir Azure VM 'de ASP.NET Web uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="b3c44-324">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
