---
title: "Razor görünüm derleme ve ön derlemesi"
author: rick-anderson
description: "MVC Razor görünüm derleme ve ASP.NET Core uygulamalarında ön derlemesi etkinleştirme açıklayan bir başvuru belgesi."
keywords: "ASP.NET Core, Razor görünüm derleme, Razor pre-derleme, Razor ön derlemesi"
ms.author: riande
manager: wpickett
ms.date: 12/13/2017
ms.topic: article
ms.assetid: ab4705b7-1638-1638-bc97-ea7f292fe92a
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-compilation
ms.openlocfilehash: 6839892c104673af0fd0fd074d368f3f42259d76
ms.sourcegitcommit: 198fb0488e961048bfa376cf58cb853ef1d1cb91
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/14/2017
---
# <a name="razor-view-compilation-and-precompilation-in-aspnet-core"></a><span data-ttu-id="3bcc1-104">Razor görünüm derleme ve ASP.NET Core içinde ön derlemesi</span><span class="sxs-lookup"><span data-stu-id="3bcc1-104">Razor view compilation and precompilation in ASP.NET Core</span></span>

<span data-ttu-id="3bcc1-105">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3bcc1-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3bcc1-106">Görünüm çağrıldığında razor görünümleri çalışma zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="3bcc1-106">Razor views are compiled at runtime when the view is invoked.</span></span> <span data-ttu-id="3bcc1-107">ASP.NET Core 1.1.0 ve daha yüksek isteğe bağlı olarak Razor görünümleri derlemek ve uygulamayla dağıtmadan&mdash;ön derlemesi da bilinen bir işlem.</span><span class="sxs-lookup"><span data-stu-id="3bcc1-107">ASP.NET Core 1.1.0 and higher can optionally compile Razor views and deploy them with the app&mdash;a process known as precompilation.</span></span> <span data-ttu-id="3bcc1-108">ASP.NET Core 2.x proje şablonları ön derlemesi varsayılan olarak etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="3bcc1-108">The ASP.NET Core 2.x project templates enable precompilation by default.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3bcc1-109">Razor görünüm ön derlemesi şu anda kullanılamıyor gerçekleştirirken bir [müstakil dağıtım (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="3bcc1-109">Razor view precompilation is currently unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span> <span data-ttu-id="3bcc1-110">2.1 bıraktığında özellik SCDs için kullanılabilir olacak.</span><span class="sxs-lookup"><span data-stu-id="3bcc1-110">The feature will be available for SCDs when 2.1 releases.</span></span> <span data-ttu-id="3bcc1-111">Daha fazla bilgi için bkz: [görünüm derleme başarısız cross-Windows Linux için derlerken](https://github.com/aspnet/MvcPrecompilation/issues/102).</span><span class="sxs-lookup"><span data-stu-id="3bcc1-111">For more information, see [View compilation fails when cross-compiling for Linux on Windows](https://github.com/aspnet/MvcPrecompilation/issues/102).</span></span>

<span data-ttu-id="3bcc1-112">Ön derleme dikkate alınacak noktalar:</span><span class="sxs-lookup"><span data-stu-id="3bcc1-112">Precompilation considerations:</span></span>

* <span data-ttu-id="3bcc1-113">Görünümleri önceden derleme, bir küçük yayımlanan paket ve daha hızlı başlangıç saati ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="3bcc1-113">Precompiling views results in a smaller published bundle and faster startup time.</span></span>
* <span data-ttu-id="3bcc1-114">Görünümleri ön derleme yap sonra Razor dosyaları düzenleyemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="3bcc1-114">You can't edit Razor files after you precompile views.</span></span> <span data-ttu-id="3bcc1-115">Düzenlenen görünümleri yayımlanan pakete mevcut olmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="3bcc1-115">The edited views won't be present in the published bundle.</span></span> 

<span data-ttu-id="3bcc1-116">Önceden derlenmiş görünümleri dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="3bcc1-116">To deploy precompiled views:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="3bcc1-117">ASP.NET 2.x çekirdek</span><span class="sxs-lookup"><span data-stu-id="3bcc1-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="3bcc1-118">Projeniz .NET Framework hedefliyorsa, bir paket başvuru eklemek [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/):</span><span class="sxs-lookup"><span data-stu-id="3bcc1-118">If your project targets .NET Framework, include a package reference to [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/):</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.ViewCompilation" Version="2.0.0" PrivateAssets="All" />
```

<span data-ttu-id="3bcc1-119">Projeniz .NET Core hedefliyorsa, hiçbir değişiklik gereklidir.</span><span class="sxs-lookup"><span data-stu-id="3bcc1-119">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="3bcc1-120">ASP.NET Core 2.x proje şablonları örtük olarak ayarlamak `MvcRazorCompileOnPublish` için `true` varsayılan olarak, yani bu düğüm güvenle kaldırılabilir gelen *.csproj* dosya.</span><span class="sxs-lookup"><span data-stu-id="3bcc1-120">The ASP.NET Core 2.x project templates implicitly set `MvcRazorCompileOnPublish` to `true` by default, which means this node can be safely removed from the *.csproj* file.</span></span> <span data-ttu-id="3bcc1-121">Açık olmasını isterseniz, ayarı hiçbir zarar yoktur `MvcRazorCompileOnPublish` özelliğine `true`.</span><span class="sxs-lookup"><span data-stu-id="3bcc1-121">If you prefer to be explicit, there's no harm in setting the `MvcRazorCompileOnPublish` property to `true`.</span></span> <span data-ttu-id="3bcc1-122">Aşağıdaki *.csproj* örnek bu ayar vurgular:</span><span class="sxs-lookup"><span data-stu-id="3bcc1-122">The following *.csproj* sample highlights this setting:</span></span>

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="3bcc1-123">ASP.NET 1.x çekirdek</span><span class="sxs-lookup"><span data-stu-id="3bcc1-123">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="3bcc1-124">Ayarlama `MvcRazorCompileOnPublish` için `true`ve bir paket başvuru eklemek `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`.</span><span class="sxs-lookup"><span data-stu-id="3bcc1-124">Set `MvcRazorCompileOnPublish` to `true`, and include a package reference to `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`.</span></span> <span data-ttu-id="3bcc1-125">Aşağıdaki *.csproj* örnek bu ayarları vurgular:</span><span class="sxs-lookup"><span data-stu-id="3bcc1-125">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish.csproj?highlight=5,12)]

---

<span data-ttu-id="3bcc1-126">Uygulama için hazırlama bir [framework bağımlı dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd) proje kökündeki aşağıdaki gibi bir komutu çalıştırarak:</span><span class="sxs-lookup"><span data-stu-id="3bcc1-126">Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) by executing a command such as the following at the project root:</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="3bcc1-127">A *< project_name >. PrecompiledViews.dll* derlenmiş Razor görünümleri içeren dosyası ön derlemesi başarılı olduğunda oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3bcc1-127">A *<project_name>.PrecompiledViews.dll* file, containing the compiled Razor views, is produced when precompilation succeeds.</span></span> <span data-ttu-id="3bcc1-128">Örneğin, aşağıdaki ekran görüntüsünde içeriğini gösterir *Index.cshtml* içine *WebApplication1.PrecompiledViews.dll*:</span><span class="sxs-lookup"><span data-stu-id="3bcc1-128">For example, the screenshot below depicts the contents of *Index.cshtml* inside of *WebApplication1.PrecompiledViews.dll*:</span></span>

![DLL içindeki Razor görünümleri](view-compilation/_static/razor-views-in-dll.png)
