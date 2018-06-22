---
title: ASP.NET Core Razor sayfalarında için filtre yöntemleri
author: Rick-Anderson
description: ASP.NET Core Razor sayfalar için filtre yöntemleri oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/05/2018
uid: razor-pages/filter
ms.openlocfilehash: ff2f4b4c2556e31d0261bc2c2ff47a4a6c7a1335
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36291599"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="83136-103">ASP.NET Core Razor sayfalarında için filtre yöntemleri</span><span class="sxs-lookup"><span data-stu-id="83136-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="83136-104">tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="83136-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="83136-105">Razor sayfa filtreleri [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) ve [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) Razor kodu önce ve Razor sayfasını işleyici çalıştırıldıktan sonra çalıştırmak sayfaları olanak verir.</span><span class="sxs-lookup"><span data-stu-id="83136-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="83136-106">Razor sayfa filtreleri benzer [ASP.NET Core MVC eylem filtrelerini](xref:mvc/controllers/filters#action-filters), tek tek sayfa işleyici yöntemlerine uygulanamaz dışında.</span><span class="sxs-lookup"><span data-stu-id="83136-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span> 

<span data-ttu-id="83136-107">Razor sayfa filtreleri:</span><span class="sxs-lookup"><span data-stu-id="83136-107">Razor Page filters:</span></span>

* <span data-ttu-id="83136-108">Kod bir işleyici yöntemi seçtikten sonra ancak model bağlama gerçekleşmeden önce çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="83136-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="83136-109">Model bağlama tamamlandıktan sonra işleyici yöntemi yürütülmeden önce kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="83136-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="83136-110">İşleyici yöntemi yürütüldükten sonra kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="83136-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="83136-111">Bir sayfa veya genel olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="83136-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="83136-112">Belirli bir sayfaya işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="83136-112">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="83136-113">Kod sayfası Oluşturucusu veya ara yazılımı kullanarak bir işleyici yöntemi çalışır, ancak yalnızca Razor sayfa filtreleri erişiminiz önce çalıştırılabilir [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="83136-113">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="83136-114">Filtreleri bulunan bir [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) erişim sağlayan parametre türetilmiş `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="83136-114">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="83136-115">Örneğin, [bir filtre özniteliğini uygulamak](#ifa) örnek yanıt oluşturucular veya ara yazılımı ile yapılamaz bir şey için bir başlık ekler.</span><span class="sxs-lookup"><span data-stu-id="83136-115">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="83136-116">[Görüntülemek veya karşıdan örnek kod](https://github.com/aspnet/Docs/tree/live/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="83136-116">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

<span data-ttu-id="83136-117">Razor sayfa filtreleri genel olarak veya sayfa düzeyinde uygulanabilir aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="83136-117">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="83136-118">Zaman uyumlu yöntemleri:</span><span class="sxs-lookup"><span data-stu-id="83136-118">Synchronous methods:</span></span>

    * <span data-ttu-id="83136-119">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : bir işleyici yöntemi seçildiyse, ancak önce model bağlama oluşur sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="83136-119">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
    * <span data-ttu-id="83136-120">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : model bağlama tamamlandıktan sonra işleyici yöntemi yürütülmeden önce çağrılır.</span><span class="sxs-lookup"><span data-stu-id="83136-120">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
    * <span data-ttu-id="83136-121">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : işleyici yöntemi, önce eylem sonucu yürütüldükten sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="83136-121">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="83136-122">Zaman uyumsuz yöntemleri:</span><span class="sxs-lookup"><span data-stu-id="83136-122">Asynchronous methods:</span></span>

    * <span data-ttu-id="83136-123">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : zaman uyumsuz olarak sonra adlı işleyici yöntemi seçilmedi, ancak önce model bağlama oluşur.</span><span class="sxs-lookup"><span data-stu-id="83136-123">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
    * <span data-ttu-id="83136-124">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : model bağlama tamamlandıktan sonra işleyici yöntemi çağrılmadan önce zaman uyumsuz olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="83136-124">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="83136-125">Uygulama **ya da** zaman uyumlu veya zaman uyumsuz bir filtre arabiriminin sürümünü, her ikisine değil.</span><span class="sxs-lookup"><span data-stu-id="83136-125">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="83136-126">Framework ilk filtreyi zaman uyumsuz arabirimini uygulayan ve Öyleyse, çağıran bakar.</span><span class="sxs-lookup"><span data-stu-id="83136-126">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="83136-127">Aksi durumda, zaman uyumlu arabiriminin yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="83136-127">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="83136-128">Her iki arabirimde uygulanırsa, yalnızca zaman uyumsuz yöntemleri olan çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="83136-128">If both interfaces are implemented, only the async methods are be called.</span></span> <span data-ttu-id="83136-129">Geçersiz kılmaları sayfalarında aynı kuralın uygulanacağı, zaman uyumlu veya zaman uyumsuz sürümü geçersiz kılar, ikisini uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="83136-129">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="83136-130">Razor sayfa filtreleri genel uygulama</span><span class="sxs-lookup"><span data-stu-id="83136-130">Implement Razor Page filters globally</span></span>

<span data-ttu-id="83136-131">Aşağıdaki kod uygulayan `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="83136-131">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="83136-132">Önceki kod [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="83136-132">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="83136-133">Aşağıdaki örnekte, uygulama için izleme bilgilerini sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="83136-133">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="83136-134">Aşağıdaki kod etkinleştirir `SampleAsyncPageFilter` içinde `Startup` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="83136-134">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="83136-135">Aşağıdaki kod tam gösterir `Startup` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="83136-135">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="83136-136">Aşağıdaki kod çağrıları `AddFolderApplicationModelConvention` uygulamak için `SampleAsyncPageFilter` yalnızca sayfalarına */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="83136-136">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="83136-137">Aşağıdaki kod zaman uyumlu uygular `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="83136-137">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="83136-138">Aşağıdaki kod etkinleştirir `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="83136-138">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

::: moniker range=">= aspnetcore-2.1"
## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="83136-139">Filtre yöntemi geçersiz kılarak uygulama Razor sayfa filtreleri</span><span class="sxs-lookup"><span data-stu-id="83136-139">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="83136-140">Aşağıdaki kod, zaman uyumlu Razor sayfa filtrelerini geçersiz kılan:</span><span class="sxs-lookup"><span data-stu-id="83136-140">The following code overrides the synchronous Razor Page filters:</span></span>

<span data-ttu-id="83136-141">[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]</span><span class="sxs-lookup"><span data-stu-id="83136-141">[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]</span></span>

::: moniker-end

<a name="ifa"></a>
## <a name="implement-a-filter-attribute"></a><span data-ttu-id="83136-142">Bir filtre özniteliğini uygulayın</span><span class="sxs-lookup"><span data-stu-id="83136-142">Implement a filter attribute</span></span>

<span data-ttu-id="83136-143">Yerleşik öznitelik tabanlı filtre [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtre sınıflandırma.</span><span class="sxs-lookup"><span data-stu-id="83136-143">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="83136-144">Aşağıdaki filtre üstbilgi yanıta ekler:</span><span class="sxs-lookup"><span data-stu-id="83136-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="83136-145">Aşağıdaki kod geçerlidir `AddHeader` özniteliği:</span><span class="sxs-lookup"><span data-stu-id="83136-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="83136-146">Bkz: [varsayılan sırası geçersiz kılma](xref:mvc/controllers/filters#overriding-the-default-order) sırası geçersiz kılma hakkında yönergeler için.</span><span class="sxs-lookup"><span data-stu-id="83136-146">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="83136-147">Bkz: [iptali ve kestirmeler](xref:mvc/controllers/filters#cancellation-and-short-circuiting) yönergeler bir filtre filtre ardışık düzen kısa devre oluşturur.</span><span class="sxs-lookup"><span data-stu-id="83136-147">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>
## <a name="authorize-filter-attribute"></a><span data-ttu-id="83136-148">Filtre özniteliği yetkilendirmek</span><span class="sxs-lookup"><span data-stu-id="83136-148">Authorize filter attribute</span></span>

<span data-ttu-id="83136-149">[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) özniteliği uygulanabilir bir `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="83136-149">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]
