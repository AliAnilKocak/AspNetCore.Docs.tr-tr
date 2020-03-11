---
title: DotNet ASPNET-CodeGenerator komutu
author: rick-anderson
description: DotNet ASPNET-CodeGenerator komutu yapı ASP.NET Core projeler.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 1043a578f66d5bb57f4a81e9fe21afa5e3c37cb8
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665188"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="68606-103">DotNet ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="68606-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="68606-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="68606-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="68606-105">`dotnet aspnet-codegenerator`-ASP.NET Core scafkatlama altyapısını çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="68606-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="68606-106">`dotnet aspnet-codegenerator` yalnızca komut satırından yapı iskelesi sağlamak için gereklidir, Visual Studio ile scafkatlamayı kullanmak gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="68606-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="68606-107">Bu makale [.NET Core 2,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) ve üzeri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="68606-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="68606-108">ASPNET-CodeGenerator yükleniyor</span><span class="sxs-lookup"><span data-stu-id="68606-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="68606-109">`dotnet-aspnet-codegenerator` yüklenmesi gereken [küresel bir araçtır](/dotnet/core/tools/global-tools) .</span><span class="sxs-lookup"><span data-stu-id="68606-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="68606-110">Aşağıdaki komut `dotnet-aspnet-codegenerator` aracının en son kararlı sürümünü yüklüyor:</span><span class="sxs-lookup"><span data-stu-id="68606-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="68606-111">Aşağıdaki komut, yüklü .NET Core SDK 'larında bulunan en son kararlı sürüme `dotnet-aspnet-codegenerator` güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="68606-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="68606-112">Özeti</span><span class="sxs-lookup"><span data-stu-id="68606-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="68606-113">Açıklama</span><span class="sxs-lookup"><span data-stu-id="68606-113">Description</span></span>

<span data-ttu-id="68606-114">`dotnet aspnet-codegenerator` Global komutu, ASP.NET Core kod Oluşturucu ve scafkatlama altyapısını çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="68606-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="68606-115">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="68606-115">Arguments</span></span>

`generator`

<span data-ttu-id="68606-116">Çalıştırılacak kod Oluşturucu.</span><span class="sxs-lookup"><span data-stu-id="68606-116">The code generator to run.</span></span> <span data-ttu-id="68606-117">Aşağıdaki oluşturucular kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="68606-117">The following generators are available:</span></span>

| <span data-ttu-id="68606-118">Oluşturucu</span><span class="sxs-lookup"><span data-stu-id="68606-118">Generator</span></span> | <span data-ttu-id="68606-119">Çalışma</span><span class="sxs-lookup"><span data-stu-id="68606-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="68606-120">Alan</span><span class="sxs-lookup"><span data-stu-id="68606-120">area</span></span>      | [<span data-ttu-id="68606-121">Bir alanı dolandırın</span><span class="sxs-lookup"><span data-stu-id="68606-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="68606-122">denetleyici</span><span class="sxs-lookup"><span data-stu-id="68606-122">controller</span></span>| [<span data-ttu-id="68606-123">Bir denetleyiciyi yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="68606-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="68606-124">identity</span><span class="sxs-lookup"><span data-stu-id="68606-124">identity</span></span>  | [<span data-ttu-id="68606-125">Yapı iskelesi kimliği</span><span class="sxs-lookup"><span data-stu-id="68606-125">Scaffolds Identity</span></span>](/aspnet/core/security/authentication/scaffold-identity) |
  <span data-ttu-id="68606-126">Razorpage</span><span class="sxs-lookup"><span data-stu-id="68606-126">razorpage</span></span> | [<span data-ttu-id="68606-127">Yapı iskelesi Razor Pages</span><span class="sxs-lookup"><span data-stu-id="68606-127">Scaffolds Razor Pages</span></span>](/aspnet/core/tutorials/razor-pages/model) |
  <span data-ttu-id="68606-128">görünüm</span><span class="sxs-lookup"><span data-stu-id="68606-128">view</span></span>      | [<span data-ttu-id="68606-129">Bir görünümü dolandırın</span><span class="sxs-lookup"><span data-stu-id="68606-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="68606-130">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="68606-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="68606-131">NuGet paket dizinini belirtir.</span><span class="sxs-lookup"><span data-stu-id="68606-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="68606-132">Yapı yapılandırmasını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="68606-132">Defines the build configuration.</span></span> <span data-ttu-id="68606-133">Varsayılan değer: `Debug`.</span><span class="sxs-lookup"><span data-stu-id="68606-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="68606-134">Kullanılacak hedef [çerçeve](/dotnet/standard/frameworks) .</span><span class="sxs-lookup"><span data-stu-id="68606-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="68606-135">Örneğin, `net46`.</span><span class="sxs-lookup"><span data-stu-id="68606-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="68606-136">Yapı temel yolu.</span><span class="sxs-lookup"><span data-stu-id="68606-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="68606-137">Komut için kısa bir yardım yazdırır.</span><span class="sxs-lookup"><span data-stu-id="68606-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="68606-138">Çalıştırmadan önce projeyi oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="68606-138">Doesn't build the project before running.</span></span> <span data-ttu-id="68606-139">Ayrıca `--no-restore` bayrağını örtülü olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="68606-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="68606-140">Çalıştırılacak proje dosyasının yolunu belirtir (klasör adı veya tam yol).</span><span class="sxs-lookup"><span data-stu-id="68606-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="68606-141">Belirtilmezse, varsayılan olarak geçerli dizini alır.</span><span class="sxs-lookup"><span data-stu-id="68606-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="68606-142">Oluşturucu seçenekleri</span><span class="sxs-lookup"><span data-stu-id="68606-142">Generator options</span></span>

<span data-ttu-id="68606-143">Aşağıdaki bölümler, desteklenen oluşturucular için kullanılabilen seçenekleri ayrıntılandırır:</span><span class="sxs-lookup"><span data-stu-id="68606-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="68606-144">Alan</span><span class="sxs-lookup"><span data-stu-id="68606-144">Area</span></span>
* <span data-ttu-id="68606-145">Denetleyici</span><span class="sxs-lookup"><span data-stu-id="68606-145">Controller</span></span>
* <span data-ttu-id="68606-146">Kimlik</span><span class="sxs-lookup"><span data-stu-id="68606-146">Identity</span></span>  
* <span data-ttu-id="68606-147">Razorpage</span><span class="sxs-lookup"><span data-stu-id="68606-147">Razorpage</span></span>
* <span data-ttu-id="68606-148">Görünüm</span><span class="sxs-lookup"><span data-stu-id="68606-148">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="68606-149">Alan seçenekleri</span><span class="sxs-lookup"><span data-stu-id="68606-149">Area options</span></span>

<span data-ttu-id="68606-150">Bu araç, denetleyiciler ve görünümler içeren ASP.NET Core Web projelerine yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="68606-150">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="68606-151">Razor Pages uygulamalarına yönelik değildir.</span><span class="sxs-lookup"><span data-stu-id="68606-151">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="68606-152">Kullanım: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="68606-152">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="68606-153">Yukarıdaki komut aşağıdaki klasörleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="68606-153">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="68606-154">*Alanlar*</span><span class="sxs-lookup"><span data-stu-id="68606-154">*Areas*</span></span>
  * <span data-ttu-id="68606-155">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="68606-155">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="68606-156">*Denetleyiciler*</span><span class="sxs-lookup"><span data-stu-id="68606-156">*Controllers*</span></span>
    * <span data-ttu-id="68606-157">*Veriler*</span><span class="sxs-lookup"><span data-stu-id="68606-157">*Data*</span></span>
    * <span data-ttu-id="68606-158">*Modelde*</span><span class="sxs-lookup"><span data-stu-id="68606-158">*Models*</span></span>
    * <span data-ttu-id="68606-159">*Görünümler*</span><span class="sxs-lookup"><span data-stu-id="68606-159">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="68606-160">Denetleyici Seçenekleri</span><span class="sxs-lookup"><span data-stu-id="68606-160">Controller options</span></span>

<span data-ttu-id="68606-161">Aşağıdaki tabloda `aspnet-codegenerator` `controller` ve `razorpage`seçenekleri listelenmektedir:</span><span class="sxs-lookup"><span data-stu-id="68606-161">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="68606-162">Aşağıdaki tabloda `aspnet-codegenerator controller`için benzersiz seçenekler listelenmektedir:</span><span class="sxs-lookup"><span data-stu-id="68606-162">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="68606-163">Seçenek</span><span class="sxs-lookup"><span data-stu-id="68606-163">Option</span></span>               | <span data-ttu-id="68606-164">Açıklama</span><span class="sxs-lookup"><span data-stu-id="68606-164">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="68606-165">--controllerName veya-Name</span><span class="sxs-lookup"><span data-stu-id="68606-165">--controllerName or -name</span></span> | <span data-ttu-id="68606-166">Denetleyicinin adı.</span><span class="sxs-lookup"><span data-stu-id="68606-166">Name of the controller.</span></span> |
| <span data-ttu-id="68606-167">--Kullanılan Asyncactions veya-async</span><span class="sxs-lookup"><span data-stu-id="68606-167">--useAsyncActions or -async</span></span> | <span data-ttu-id="68606-168">Zaman uyumsuz denetleyici eylemleri oluştur.</span><span class="sxs-lookup"><span data-stu-id="68606-168">Generate async controller actions.</span></span> |
| <span data-ttu-id="68606-169">--noViews veya-NV</span><span class="sxs-lookup"><span data-stu-id="68606-169">--noViews or -nv</span></span> | <span data-ttu-id="68606-170">**Hiçbir** görünüm oluşturun.</span><span class="sxs-lookup"><span data-stu-id="68606-170">Generate **no** views.</span></span> |
| <span data-ttu-id="68606-171">--restWithNoViews veya-API</span><span class="sxs-lookup"><span data-stu-id="68606-171">--restWithNoViews or -api</span></span>  | <span data-ttu-id="68606-172">REST stili API ile bir denetleyici oluşturun.</span><span class="sxs-lookup"><span data-stu-id="68606-172">Generate a Controller with REST style API.</span></span> <span data-ttu-id="68606-173">`noViews` varsayılır ve tüm görünümle ilgili seçenekler yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="68606-173">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="68606-174">--readWriteActions veya-Actions</span><span class="sxs-lookup"><span data-stu-id="68606-174">--readWriteActions or -actions</span></span> | <span data-ttu-id="68606-175">Model olmadan okuma/yazma eylemleri ile denetleyici oluşturun.</span><span class="sxs-lookup"><span data-stu-id="68606-175">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="68606-176">`aspnet-codegenerator controller` komutu hakkında yardım için `-h` anahtarını kullanın:</span><span class="sxs-lookup"><span data-stu-id="68606-176">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="68606-177">Bkz. `dotnet aspnet-codegenerator controller`bir örnek için [film modelini yapı](/aspnet/core/tutorials/razor-pages/model) .</span><span class="sxs-lookup"><span data-stu-id="68606-177">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="68606-178">Razorpage</span><span class="sxs-lookup"><span data-stu-id="68606-178">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="68606-179">Razor Pages yeni sayfanın adı ve kullanılacak şablon belirtilerek tek tek iskele alınabilir.</span><span class="sxs-lookup"><span data-stu-id="68606-179">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="68606-180">Desteklenen şablonlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="68606-180">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="68606-181">Örneğin, aşağıdaki komut *myedit. cshtml* ve *MyEdit.cshtml.cs*oluşturmak için düzenleme şablonunu kullanır:</span><span class="sxs-lookup"><span data-stu-id="68606-181">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="68606-182">Genellikle, şablon ve oluşturulan dosya adı belirtilmez ve aşağıdaki şablonlar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="68606-182">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="68606-183">Aşağıdaki tabloda `aspnet-codegenerator` `razorpage` ve `controller`seçenekleri listelenmektedir:</span><span class="sxs-lookup"><span data-stu-id="68606-183">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="68606-184">Aşağıdaki tabloda `aspnet-codegenerator razorpage`için benzersiz seçenekler listelenmektedir:</span><span class="sxs-lookup"><span data-stu-id="68606-184">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="68606-185">Seçenek</span><span class="sxs-lookup"><span data-stu-id="68606-185">Option</span></span>               | <span data-ttu-id="68606-186">Açıklama</span><span class="sxs-lookup"><span data-stu-id="68606-186">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="68606-187">--namespaceName veya-Namespace</span><span class="sxs-lookup"><span data-stu-id="68606-187">--namespaceName or -namespace</span></span> | <span data-ttu-id="68606-188">Oluşturulan PageModel için kullanılacak ad alanının adı</span><span class="sxs-lookup"><span data-stu-id="68606-188">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="68606-189">--partialView veya-Partial</span><span class="sxs-lookup"><span data-stu-id="68606-189">--partialView or -partial</span></span> | <span data-ttu-id="68606-190">Kısmi bir görünüm oluşturun.</span><span class="sxs-lookup"><span data-stu-id="68606-190">Generate a partial view.</span></span> <span data-ttu-id="68606-191">Bu belirtilirse, düzen seçenekleri-l ve-UDL yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="68606-191">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="68606-192">--noPageModel veya-NPM</span><span class="sxs-lookup"><span data-stu-id="68606-192">--noPageModel or -npm</span></span> | <span data-ttu-id="68606-193">Boş şablon için bir PageModel sınıfı oluşturmamı geç</span><span class="sxs-lookup"><span data-stu-id="68606-193">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="68606-194">`aspnet-codegenerator razorpage` komutu hakkında yardım için `-h` anahtarını kullanın:</span><span class="sxs-lookup"><span data-stu-id="68606-194">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="68606-195">Bkz. `dotnet aspnet-codegenerator razorpage`bir örnek için [film modelini yapı](/aspnet/core/tutorials/razor-pages/model) .</span><span class="sxs-lookup"><span data-stu-id="68606-195">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### <a name="identity"></a><span data-ttu-id="68606-196">Kimlik</span><span class="sxs-lookup"><span data-stu-id="68606-196">Identity</span></span>

<span data-ttu-id="68606-197">Bkz. [Yapı Iskelesi kimliği](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="68606-197">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
