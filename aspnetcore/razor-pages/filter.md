---
title: ASP.NET Core Razor sayfaları için filtre yöntemleri
author: Rick-Anderson
description: ASP.NET Core Razor sayfaları için filtre yöntemleri oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/05/2018
uid: razor-pages/filter
ms.openlocfilehash: 2480e67d251de8f8aecb6c484999c90d0220dd19
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900893"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="d0f7a-103">ASP.NET Core Razor sayfaları için filtre yöntemleri</span><span class="sxs-lookup"><span data-stu-id="d0f7a-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="d0f7a-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d0f7a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d0f7a-105">Razor sayfası filtreler [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) ve [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) bir Razor sayfası işleyici çalıştırılmadan önce ve sonra kodu çalıştırmak Razor sayfaları olanak verir.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="d0f7a-106">Razor sayfa filtreleri benzer [ASP.NET Core MVC eylem filtrelerini](xref:mvc/controllers/filters#action-filters)dışında tekil işleyici yöntemleri için uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span> 

<span data-ttu-id="d0f7a-107">Razor sayfası filtreler:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-107">Razor Page filters:</span></span>

* <span data-ttu-id="d0f7a-108">Kodu bir işleyici yöntemi seçtikten sonra ancak model bağlama gerçekleşmeden önce çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="d0f7a-109">Model bağlama işlemi tamamlandıktan sonra işleyicisi yöntemi yürütülmeden önce kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="d0f7a-110">İşleyici yöntemi yürütüldükten sonra kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="d0f7a-111">Bir sayfadaki veya genel olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="d0f7a-112">Belirli bir sayfaya işleyici yöntemleri için uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-112">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="d0f7a-113">Kod sayfası Oluşturucusu veya ara yazılımlar kullanarak bir işleyici yöntemini yürütür, ancak Razor sayfası filtreler yalnızca erişimleri önce çalıştırılabilir [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="d0f7a-113">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="d0f7a-114">Filtreye sahip bir [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) erişim sağlayan bir parametre türetilmiş `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-114">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="d0f7a-115">Örneğin, [uygulayan bir filtre özniteliğini](#ifa) örnek yanıt oluşturucular veya ara yazılımlar ile yapılamaz bir şey için bir başlık ekler.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-115">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="d0f7a-116">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d0f7a-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d0f7a-117">Razor sayfa filtreleri, genel olarak veya sayfa düzeyinde uygulanabilir aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-117">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="d0f7a-118">Zaman uyumlu metotları:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-118">Synchronous methods:</span></span>

  * <span data-ttu-id="d0f7a-119">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : İşleyicisi yöntemi seçildi, ancak önce model bağlama gerçekleşir sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-119">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="d0f7a-120">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Model bağlama işlemi tamamlandıktan sonra işleyicisi yöntemi yürütülmeden önce çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-120">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="d0f7a-121">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : İşleyicisi yöntemi, önce eylem sonucu yürütüldükten sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-121">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="d0f7a-122">Zaman uyumsuz yöntemler:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-122">Asynchronous methods:</span></span>

  * <span data-ttu-id="d0f7a-123">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Zaman uyumsuz olarak işleyicisi yöntemi seçtikten sonra ancak model bağlama gerçekleşmeden önce çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-123">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="d0f7a-124">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Zaman uyumsuz model bağlama işlemi tamamlandıktan sonra işleyicisi yöntemi çağırılmadan önce çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-124">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="d0f7a-125">Uygulama **ya da** zaman uyumlu veya zaman uyumsuz sürümü filtre arabirimi, her ikisini birden değil.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-125">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="d0f7a-126">Framework ilk filtre zaman uyumsuz arabirimini uygulayan ve çağrı yaptığı bu durumda olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-126">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="d0f7a-127">Aksi durumda, zaman uyumlu arabirim yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-127">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="d0f7a-128">Her iki arabirimde uygulanırsa, yalnızca zaman uyumsuz yöntemler olan çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-128">If both interfaces are implemented, only the async methods are be called.</span></span> <span data-ttu-id="d0f7a-129">Geçersiz kılmalar sayfalarında aynı kuralın uygulanacağı, zaman uyumlu veya zaman uyumsuz sürümü geçersiz kılma, ikisini birden uygular.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-129">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="d0f7a-130">Genel olarak Filtreleri Uygula Razor sayfası</span><span class="sxs-lookup"><span data-stu-id="d0f7a-130">Implement Razor Page filters globally</span></span>

<span data-ttu-id="d0f7a-131">Aşağıdaki kod uygulayan `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-131">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="d0f7a-132">Önceki kodda, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-132">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="d0f7a-133">Aşağıdaki örnekte, uygulama için izleme bilgisi sağlamak üzere kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-133">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="d0f7a-134">Aşağıdaki kod etkinleştirir `SampleAsyncPageFilter` içinde `Startup` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-134">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="d0f7a-135">Aşağıdaki kod tam gösterir `Startup` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-135">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="d0f7a-136">Aşağıdaki kod çağrıları `AddFolderApplicationModelConvention` uygulanacak `SampleAsyncPageFilter` yalnızca sayfalarına */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-136">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="d0f7a-137">Aşağıdaki kod, zaman uyumlu uygulayan `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-137">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="d0f7a-138">Aşağıdaki kod etkinleştirir `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-138">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="d0f7a-139">Filtre yöntemi geçersiz kılarak Filtreleri Uygula Razor sayfası</span><span class="sxs-lookup"><span data-stu-id="d0f7a-139">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="d0f7a-140">Aşağıdaki kod, zaman uyumlu bir Razor sayfası filtrelerini geçersiz kılan:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-140">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

::: moniker-end

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="d0f7a-141">Bir filtre özniteliğini uygulayın</span><span class="sxs-lookup"><span data-stu-id="d0f7a-141">Implement a filter attribute</span></span>

<span data-ttu-id="d0f7a-142">Yerleşik öznitelik tabanlı filtre [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtre sınıflandırma.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-142">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="d0f7a-143">Aşağıdaki filtre yanıt olarak bir başlık ekler:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-143">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="d0f7a-144">Aşağıdaki kod geçerlidir `AddHeader` özniteliği:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-144">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="d0f7a-145">Bkz: [varsayılan sırası geçersiz kılma](xref:mvc/controllers/filters#overriding-the-default-order) sırası geçersiz kılma hakkında yönergeler için.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-145">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="d0f7a-146">Bkz: [iptal ve kestirmeler](xref:mvc/controllers/filters#cancellation-and-short-circuiting) filtre ardışık düzen bir filtre tarafından iki ilişkin yönergeler için.</span><span class="sxs-lookup"><span data-stu-id="d0f7a-146">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="d0f7a-147">Filtre özniteliği Yetkilendir</span><span class="sxs-lookup"><span data-stu-id="d0f7a-147">Authorize filter attribute</span></span>

<span data-ttu-id="d0f7a-148">[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) özniteliği uygulanabilir bir `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="d0f7a-148">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]
