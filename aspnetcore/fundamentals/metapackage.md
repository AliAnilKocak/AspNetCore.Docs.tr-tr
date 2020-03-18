---
title: ASP.NET Core 2,0 için Microsoft. AspNetCore. All metapackage
author: Rick-Anderson
description: Microsoft. AspNetCore. All metapackage, ASP.NET Core 2,1 ve üzeri için önerilmez.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
uid: fundamentals/metapackage
ms.openlocfilehash: cc00c075909da5c17a4aa2fd252c9e662e5a0fc9
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511073"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a><span data-ttu-id="9080f-103">ASP.NET Core 2,0 için Microsoft. AspNetCore. All metapackage</span><span class="sxs-lookup"><span data-stu-id="9080f-103">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9080f-104">`Microsoft.AspNetCore.All` metapackage, ASP.NET Core 3,0 ve sonraki sürümlere dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="9080f-104">The `Microsoft.AspNetCore.All` metapackage isn't included in ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="9080f-105">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/aspnet/Announcements/issues/314)bakın.</span><span class="sxs-lookup"><span data-stu-id="9080f-105">For more information, see [this GitHub issue](https://github.com/aspnet/Announcements/issues/314).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="9080f-106">ASP.NET Core 2,1 ' i hedefleyen uygulamaların ve daha sonra bu paket yerine [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) kullanılmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="9080f-106">We recommend applications targeting ASP.NET Core 2.1 and later use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) rather than this package.</span></span> <span data-ttu-id="9080f-107">Bkz. Bu makaledeki [Microsoft. AspNetCore. All Ile Microsoft. aspnetcore. app 'e geçme](#migrate) .</span><span class="sxs-lookup"><span data-stu-id="9080f-107">See [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](#migrate) in this article.</span></span>

<span data-ttu-id="9080f-108">Bu özellik, .NET Core 2. x 'i hedefleyen ASP.NET Core 2. x gerektirir.</span><span class="sxs-lookup"><span data-stu-id="9080f-108">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="9080f-109">[Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) , paylaşılan bir çerçeveye başvuran bir metapackage.</span><span class="sxs-lookup"><span data-stu-id="9080f-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) is a metapackage that refers to a shared framework.</span></span> <span data-ttu-id="9080f-110">*Paylaşılan çerçeve* , uygulamanın klasörlerinde olmayan derlemelerin ( *. dll* dosyaları) bir kümesidir.</span><span class="sxs-lookup"><span data-stu-id="9080f-110">A *shared framework* is a set of assemblies (*.dll* files) that are not in the app's folders.</span></span> <span data-ttu-id="9080f-111">Uygulamayı çalıştırmak için makinede paylaşılan çerçeve yüklü olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9080f-111">The shared framework must be installed on the machine to run the app.</span></span> <span data-ttu-id="9080f-112">Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="9080f-112">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="9080f-113">`Microsoft.AspNetCore.All` başvurduğu paylaşılan çerçeve şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="9080f-113">The shared framework that `Microsoft.AspNetCore.All` refers to includes:</span></span>

* <span data-ttu-id="9080f-114">ASP.NET Core ekibi tarafından desteklenen tüm paketler.</span><span class="sxs-lookup"><span data-stu-id="9080f-114">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="9080f-115">Entity Framework Core tarafından desteklenen tüm paketler.</span><span class="sxs-lookup"><span data-stu-id="9080f-115">All supported packages by the Entity Framework Core.</span></span>
* <span data-ttu-id="9080f-116">ASP.NET Core ve Entity Framework Core tarafından kullanılan dahili ve üçüncü taraf bağımlılıklar.</span><span class="sxs-lookup"><span data-stu-id="9080f-116">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span>

<span data-ttu-id="9080f-117">ASP.NET Core 2. x ve Entity Framework Core 2. x ' in tüm özellikleri `Microsoft.AspNetCore.All` paketine dahildir.</span><span class="sxs-lookup"><span data-stu-id="9080f-117">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="9080f-118">ASP.NET Core 2,0 ' i hedefleyen varsayılan proje şablonları bu paketi kullanır.</span><span class="sxs-lookup"><span data-stu-id="9080f-118">The default project templates targeting ASP.NET Core 2.0 use this package.</span></span>

<span data-ttu-id="9080f-119">`Microsoft.AspNetCore.All` metapackage sürüm numarası, en düşük ASP.NET Core sürümü ve Entity Framework Core sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9080f-119">The version number of the `Microsoft.AspNetCore.All` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="9080f-120">Aşağıdaki *. csproj* dosyası, ASP.NET Core için `Microsoft.AspNetCore.All` metapackage 'e başvurur:</span><span class="sxs-lookup"><span data-stu-id="9080f-120">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a><span data-ttu-id="9080f-121">Örtük sürüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="9080f-121">Implicit versioning</span></span>

<span data-ttu-id="9080f-122">ASP.NET Core 2,1 veya üzeri sürümlerde, bir sürüm olmadan `Microsoft.AspNetCore.All` paketi başvurusunu belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9080f-122">In ASP.NET Core 2.1 or later, you can specify the `Microsoft.AspNetCore.All` package reference without a version.</span></span> <span data-ttu-id="9080f-123">Sürüm belirtilmediğinde, SDK tarafından örtük bir sürüm belirtilir (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="9080f-123">When the version isn't specified, an implicit version is specified by the SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="9080f-124">SDK tarafından belirtilen örtük sürüme güvenmek ve paket başvurusunda sürüm numarasını açıkça ayarlamamanız önerilir.</span><span class="sxs-lookup"><span data-stu-id="9080f-124">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="9080f-125">Bu yaklaşım hakkında sorularınız varsa, [Microsoft. AspNetCore. app örtük sürümü Için tartışmada](https://github.com/dotnet/AspNetCore.Docs/issues/6430)bir GitHub yorumu bırakın.</span><span class="sxs-lookup"><span data-stu-id="9080f-125">If you have questions about this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="9080f-126">Örtük sürüm, taşınabilir uygulamalar için `major.minor.0` olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="9080f-126">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="9080f-127">Paylaşılan Framework toplaması-iletme mekanizması, uygulamayı yüklü paylaşılan Çerçeveler arasındaki en son uyumlu sürümde çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="9080f-127">The shared framework roll-forward mechanism runs the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="9080f-128">Geliştirme, test ve üretimde aynı sürümün kullanıldığını güvence altına almak için, paylaşılan Framework 'ün aynı sürümünün tüm ortamlarda yüklü olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="9080f-128">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="9080f-129">Kendi içinde bulunan uygulamalar için, örtük sürüm numarası, yüklü SDK 'da paketlenmiş paylaşılan Framework `major.minor.patch` ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="9080f-129">For self-contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="9080f-130">`Microsoft.AspNetCore.All` paketi başvurusunda bir sürüm numarası belirtilmesi, paylaşılan Çerçeve sürümünün seçili olduğunu garanti **etmez** .</span><span class="sxs-lookup"><span data-stu-id="9080f-130">Specifying a version number on the `Microsoft.AspNetCore.All` package reference does **not** guarantee that version of the shared framework is chosen.</span></span> <span data-ttu-id="9080f-131">Örneğin, "2.1.1" sürümünün belirtildiğini, ancak "2.1.3" nin yüklü olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="9080f-131">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="9080f-132">Bu durumda, uygulama "2.1.3" kullanacaktır.</span><span class="sxs-lookup"><span data-stu-id="9080f-132">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="9080f-133">Önerilmese de, iletmeyi (Patch ve/veya Minor) devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9080f-133">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="9080f-134">DotNet ana bilgisayar alma hakkında daha fazla bilgi ve davranışını yapılandırma hakkında daha fazla bilgi için bkz. [DotNet Host top Forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="9080f-134">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

<span data-ttu-id="9080f-135">Projenin SDK 'sının, `Microsoft.AspNetCore.All`örtük sürümünü kullanabilmesi için proje dosyasında `Microsoft.NET.Sdk.Web` olarak ayarlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9080f-135">The project's SDK must be set to `Microsoft.NET.Sdk.Web` in the project file to use the implicit version of `Microsoft.AspNetCore.All`.</span></span> <span data-ttu-id="9080f-136">`Microsoft.NET.Sdk` SDK belirtildiğinde (proje dosyasının en üstünde`<Project Sdk="Microsoft.NET.Sdk">`), aşağıdaki uyarı oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="9080f-136">When the `Microsoft.NET.Sdk` SDK is specified (`<Project Sdk="Microsoft.NET.Sdk">` at the top of the project file), the following warning is generated:</span></span>

<span data-ttu-id="9080f-137">*Uyarı NU1604: proje bağımlılığı Microsoft. AspNetCore. All, kapsamlı bir alt sınır içermez. Tutarlı geri yükleme sonuçlarının sağlanması için bağımlılık sürümüne bir alt sınır ekleyin.*</span><span class="sxs-lookup"><span data-stu-id="9080f-137">*Warning NU1604: Project dependency Microsoft.AspNetCore.All does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

<span data-ttu-id="9080f-138">Bu, .NET Core 2,1 SDK ile ilgili bilinen bir sorundur ve .NET Core 2,2 SDK 'sında düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="9080f-138">This is a known issue with the .NET Core 2.1 SDK and will be fixed in the .NET Core 2.2 SDK.</span></span>

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a><span data-ttu-id="9080f-139">Microsoft. AspNetCore. All 'dan Microsoft. AspNetCore. app 'e geçiş</span><span class="sxs-lookup"><span data-stu-id="9080f-139">Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App</span></span>

<span data-ttu-id="9080f-140">Aşağıdaki paketler `Microsoft.AspNetCore.All`, `Microsoft.AspNetCore.App` paketine dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="9080f-140">The following packages are included in `Microsoft.AspNetCore.All` but not the `Microsoft.AspNetCore.App` package.</span></span>

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

<span data-ttu-id="9080f-141">`Microsoft.AspNetCore.All` ' den `Microsoft.AspNetCore.App`' ye taşımak için, uygulamanız yukarıdaki paketlerdeki API 'Leri veya bu paketler tarafından getirilen paketleri kullanıyorsa, projenizdeki bu paketlere başvurular ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9080f-141">To move from `Microsoft.AspNetCore.All` to `Microsoft.AspNetCore.App`, if your app uses any APIs from the above packages, or packages brought in by those packages, add references to those packages in your project.</span></span>

<span data-ttu-id="9080f-142">Önceki paketlerin, aksi durumda `Microsoft.AspNetCore.App` bağımlılıkları olmayan tüm bağımlılıkları örtük olarak dahil edilmez.</span><span class="sxs-lookup"><span data-stu-id="9080f-142">Any dependencies of the preceding packages that otherwise aren't dependencies of `Microsoft.AspNetCore.App` are not included implicitly.</span></span> <span data-ttu-id="9080f-143">Örnek:</span><span class="sxs-lookup"><span data-stu-id="9080f-143">For example:</span></span>

* <span data-ttu-id="9080f-144">`Microsoft.Extensions.Caching.Redis` bağımlılığı olarak `StackExchange.Redis`</span><span class="sxs-lookup"><span data-stu-id="9080f-144">`StackExchange.Redis` as a dependency of `Microsoft.Extensions.Caching.Redis`</span></span>
* <span data-ttu-id="9080f-145">`Microsoft.AspNetCore.ApplicationInsights.HostingStartup` bağımlılığı olarak `Microsoft.ApplicationInsights`</span><span class="sxs-lookup"><span data-stu-id="9080f-145">`Microsoft.ApplicationInsights` as a dependency of `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span></span>

## <a name="update-aspnet-core-21"></a><span data-ttu-id="9080f-146">Güncelleştirme ASP.NET Core 2,1</span><span class="sxs-lookup"><span data-stu-id="9080f-146">Update ASP.NET Core 2.1</span></span>

<span data-ttu-id="9080f-147">2,1 ve üzeri için `Microsoft.AspNetCore.App` metapackage 'e geçiş yapmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="9080f-147">We recommend migrating to the `Microsoft.AspNetCore.App` metapackage for 2.1 and later.</span></span> <span data-ttu-id="9080f-148">`Microsoft.AspNetCore.All` metapackage kullanmaya devam etmek ve en son düzeltme eki sürümünün dağıtıldığından emin olmak için:</span><span class="sxs-lookup"><span data-stu-id="9080f-148">To keep using the `Microsoft.AspNetCore.All` metapackage and ensure the latest patch version is deployed:</span></span>

* <span data-ttu-id="9080f-149">Geliştirme makinelerinde ve yapı sunucularında: en son [.NET Core SDK](https://dotnet.microsoft.com/download)yükler.</span><span class="sxs-lookup"><span data-stu-id="9080f-149">On development machines and build servers: Install the latest [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="9080f-150">Dağıtım sunucularında: en son [.NET Core çalışma zamanını](https://dotnet.microsoft.com/download)yükler.</span><span class="sxs-lookup"><span data-stu-id="9080f-150">On deployment servers: Install the latest [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>
 <span data-ttu-id="9080f-151">Uygulamanız, uygulama yeniden başlatıldığında en son yüklenen sürüme ileri alınacaktır.</span><span class="sxs-lookup"><span data-stu-id="9080f-151">Your app will roll forward to the latest installed version on an application restart.</span></span>
