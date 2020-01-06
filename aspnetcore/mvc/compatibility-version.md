---
title: ASP.NET Core MVC için uyumluluk sürümü
author: rick-anderson
description: ASP.NET Core başlangıç sınıfının Hizmetleri ve uygulamanın istek ardışık düzenini nasıl yapılandırdığını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
uid: mvc/compatibility-version
ms.openlocfilehash: b29e2ee49aaf0f557f1acd0cf03e9e82d5ea0105
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357737"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="d9570-103">ASP.NET Core MVC için uyumluluk sürümü</span><span class="sxs-lookup"><span data-stu-id="d9570-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="d9570-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d9570-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d9570-105"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> yöntemi, ASP.NET Core 3,0 uygulamaları için bir op değildir.</span><span class="sxs-lookup"><span data-stu-id="d9570-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method is a no-op for ASP.NET Core 3.0 apps.</span></span> <span data-ttu-id="d9570-106">Diğer bir deyişle, `SetCompatibilityVersion` herhangi bir <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> değeri ile çağırmak uygulama üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="d9570-106">That is, calling `SetCompatibilityVersion` with any value of <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> has no impact on the application.</span></span>

* <span data-ttu-id="d9570-107">ASP.NET Core sonraki alt sürümü yeni bir `CompatibilityVersion` değeri verebilir.</span><span class="sxs-lookup"><span data-stu-id="d9570-107">The next minor version of ASP.NET Core may provide a new `CompatibilityVersion` value.</span></span>
* <span data-ttu-id="d9570-108">`Version_2_2` ile `Version_2_0` `CompatibilityVersion` değerler `[Obsolete(...)]`olarak işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="d9570-108">`CompatibilityVersion` values `Version_2_0` through `Version_2_2` are marked `[Obsolete(...)]`.</span></span>
* <span data-ttu-id="d9570-109">Bkz. [Antiforgery, CORS, Diagnostics, MVC ve yönlendirme IÇINDEKI API değişikliklerini bozma](https://github.com/aspnet/Announcements/issues/387).</span><span class="sxs-lookup"><span data-stu-id="d9570-109">See [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387).</span></span> <span data-ttu-id="d9570-110">Bu liste, uyumluluk anahtarlarına yönelik son değişiklikleri içerir.</span><span class="sxs-lookup"><span data-stu-id="d9570-110">This list includes breaking changes for compatibility switches.</span></span>

<span data-ttu-id="d9570-111">`SetCompatibilityVersion` ASP.NET Core 2. x uygulamalarıyla nasıl çalıştığını görmek için, [Bu makalenin ASP.NET Core 2,2 sürümünü](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2)seçin.</span><span class="sxs-lookup"><span data-stu-id="d9570-111">To see how `SetCompatibilityVersion` works with ASP.NET Core 2.x apps, select the [ASP.NET Core 2.2 version of this article](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d9570-112"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> yöntemi, bir ASP.NET Core 2. x uygulamasının, ASP.NET Core MVC 2,1 veya 2,2 ' de ortaya çıkan olası davranış değişikliklerinin kabul etmesine veya devre dışı olmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d9570-112">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an ASP.NET Core 2.x app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or 2.2.</span></span> <span data-ttu-id="d9570-113">Bu olası davranışı ortadan kaldırma, MVC alt sisteminin davranışını ve **kodunuzun** çalışma zamanı tarafından nasıl çağrıldığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="d9570-113">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="d9570-114">' De, en son davranışı ve ASP.NET Core uzun vadeli davranışını alırsınız.</span><span class="sxs-lookup"><span data-stu-id="d9570-114">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="d9570-115">Aşağıdaki kod uyumluluk modunu ASP.NET Core 2,2 olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="d9570-115">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="d9570-116">Uygulamanızı en son sürümü (`CompatibilityVersion.Latest`) kullanarak test etmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="d9570-116">We recommend you test your app using the latest version (`CompatibilityVersion.Latest`).</span></span> <span data-ttu-id="d9570-117">Çoğu uygulamanın en son sürümü kullanarak önemli davranış değişikliklerinin olmadığı tahmin ederiz.</span><span class="sxs-lookup"><span data-stu-id="d9570-117">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="d9570-118">`SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` çağıran uygulamalar, ASP.NET Core 2.1/2.2 MVC sürümlerinde tanıtılan büyük olasılıkla davranış değişikliklerinden korunur.</span><span class="sxs-lookup"><span data-stu-id="d9570-118">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1/2.2 MVC versions.</span></span> <span data-ttu-id="d9570-119">Bu koruma:</span><span class="sxs-lookup"><span data-stu-id="d9570-119">This protection:</span></span>

* <span data-ttu-id="d9570-120">Tüm 2,1 ve sonraki değişikliklere uygulanmaz, MVC alt sisteminde çalışma zamanı davranış değişikliklerinin ASP.NET Core büyük olasılıkla bozmasına yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="d9570-120">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="d9570-121">ASP.NET Core 3,0 ' ye genişlemez.</span><span class="sxs-lookup"><span data-stu-id="d9570-121">Does not extend to ASP.NET Core 3.0.</span></span>

<span data-ttu-id="d9570-122">`SetCompatibilityVersion` **çağırmayan** ASP.NET Core 2,1 ve 2,2 uygulamaları için varsayılan uyumluluk, 2,0 uyumluluğuna sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d9570-122">The default compatibility for ASP.NET Core 2.1 and 2.2 apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="d9570-123">Diğer bir deyişle, `SetCompatibilityVersion` çağrılmayan `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`çağırma ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="d9570-123">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="d9570-124">Aşağıdaki kod, uyumluluk modunu aşağıdaki davranışlar dışında 2,2 ASP.NET Core olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="d9570-124">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="d9570-125">Doğru uyumluluk anahtarlarını kullanarak, bölme davranışı ile ilgili olan uygulamalar için:</span><span class="sxs-lookup"><span data-stu-id="d9570-125">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="d9570-126">En son sürümü kullanmanıza ve belirli bir bölme davranışı değişikliğini geri yüklemenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="d9570-126">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="d9570-127">, Uygulamanızı en son değişikliklerle çalışacak şekilde güncelleştirmeniz için size zaman kazandırır.</span><span class="sxs-lookup"><span data-stu-id="d9570-127">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="d9570-128"><xref:Microsoft.AspNetCore.Mvc.MvcOptions> belgelerinin ne değiştiği ve değişikliklerin çoğu kullanıcı için nasıl geliştirdiğinin iyi bir açıklaması vardır.</span><span class="sxs-lookup"><span data-stu-id="d9570-128">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="d9570-129">ASP.NET Core 3,0 ile, uyumluluk anahtarları tarafından desteklenen eski davranışlar kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="d9570-129">With ASP.NET Core 3.0, old behaviors supported by compatibility switches have been removed.</span></span> <span data-ttu-id="d9570-130">Bunlar, neredeyse tüm kullanıcılar faydalanmasını olumlu değişiklikler olduğunu hissettik.</span><span class="sxs-lookup"><span data-stu-id="d9570-130">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="d9570-131">Bu değişiklikleri 2,1 ve 2,2 ' de sunarak, çoğu uygulama yararlı olabilir, diğerleri ise güncelleştirilmeye zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="d9570-131">By introducing these changes in 2.1 and 2.2, most apps can benefit, while others have time to update.</span></span>
::: moniker-end
