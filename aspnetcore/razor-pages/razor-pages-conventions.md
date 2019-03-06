---
title: İçinde ASP.NET Core Razor sayfalar yol ve uygulama kuralları
author: guardrex
description: Nasıl yol ve uygulama modeli sağlayıcısı kuralları sayfası denetimi yönlendirme, bulma ve işleme yardımcı keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/27/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 5cfcae5cffd5d9484ca64c3885b838ae0a2b4a0d
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346521"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="4b003-103">İçinde ASP.NET Core Razor sayfalar yol ve uygulama kuralları</span><span class="sxs-lookup"><span data-stu-id="4b003-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="4b003-104">Tarafından [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4b003-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4b003-105">Sayfa kullanmayı öğrenin [rota ve uygulama sağlayıcısı kuralları modeli](xref:mvc/controllers/application-model#conventions) sayfasına yönlendirme, bulma ve Razor sayfaları uygulamalarda işleme denetlemek için.</span><span class="sxs-lookup"><span data-stu-id="4b003-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="4b003-106">Özel sayfa yolları her bir sayfayı yapılandırmanız gerektiğinde sahip sayfalar için yönlendirmeyi yapılandırma [AddPageRoute kuralı](#configure-a-page-route) bu konunun ilerleyen bölümlerinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="4b003-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="4b003-107">Bir sayfa yolu belirtin, yol kesimleri ekleyin veya bir rota için parametreleri eklemek için sayfanın kullanın `@page` yönergesi.</span><span class="sxs-lookup"><span data-stu-id="4b003-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="4b003-108">Daha fazla bilgi için [özel yollar](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="4b003-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="4b003-109">Yol segmentlerini veya parametre adları kullanılamaz, ayrılmış sözcükler vardır.</span><span class="sxs-lookup"><span data-stu-id="4b003-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="4b003-110">Daha fazla bilgi için [yönlendirme: Yönlendirme adları ayrılmış](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="4b003-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="4b003-111">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4b003-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="4b003-112">Senaryo</span><span class="sxs-lookup"><span data-stu-id="4b003-112">Scenario</span></span> | <span data-ttu-id="4b003-113">Örnek gösterir...</span><span class="sxs-lookup"><span data-stu-id="4b003-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="4b003-114">Model kuralları</span><span class="sxs-lookup"><span data-stu-id="4b003-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="4b003-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="4b003-115">Conventions.Add</span></span><ul><li><span data-ttu-id="4b003-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="4b003-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="4b003-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="4b003-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="4b003-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="4b003-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="4b003-119">Bir uygulamanın sayfaları için bir rota şablonu ve üst bilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4b003-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="4b003-120">Rota eylem kuralları sayfası</span><span class="sxs-lookup"><span data-stu-id="4b003-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="4b003-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="4b003-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="4b003-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="4b003-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="4b003-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="4b003-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="4b003-124">Bir rota şablonu, bir klasördeki sayfalara ve tek bir sayfaya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4b003-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="4b003-125">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="4b003-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="4b003-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="4b003-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="4b003-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="4b003-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="4b003-128">ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtresi Fabrika)</span><span class="sxs-lookup"><span data-stu-id="4b003-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="4b003-129">Bir klasördeki sayfalara üstbilgi ekleme, tek bir sayfaya bir üst bilgi ekleme ve yapılandırma bir [filtre Fabrika](xref:mvc/controllers/filters#ifilterfactory) uygulamanın sayfaları için bir başlık eklemek için.</span><span class="sxs-lookup"><span data-stu-id="4b003-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="4b003-130">Razor sayfaları kuralları eklenir ve kullanılarak yapılandırılan <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> genişletme yöntemi için <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> hizmet koleksiyonu üzerinde `Startup` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="4b003-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="4b003-131">Aşağıdaki kural örnekleri, bu konunun ilerleyen bölümlerinde açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="4b003-131">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention("/About", model => { ... });
                options.Conventions.AddPageRoute("/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention("/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a><span data-ttu-id="4b003-132">Rota sırası</span><span class="sxs-lookup"><span data-stu-id="4b003-132">Route order</span></span>

<span data-ttu-id="4b003-133">Rota belirtme bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (yol ile eşleşen) işlemek için.</span><span class="sxs-lookup"><span data-stu-id="4b003-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="4b003-134">Sipariş verme</span><span class="sxs-lookup"><span data-stu-id="4b003-134">Order</span></span>            | <span data-ttu-id="4b003-135">Davranış</span><span class="sxs-lookup"><span data-stu-id="4b003-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="4b003-136">-1</span><span class="sxs-lookup"><span data-stu-id="4b003-136">-1</span></span>               | <span data-ttu-id="4b003-137">Rotanın diğer yollar işlenmeden önce işlenir.</span><span class="sxs-lookup"><span data-stu-id="4b003-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="4b003-138">0</span><span class="sxs-lookup"><span data-stu-id="4b003-138">0</span></span>                | <span data-ttu-id="4b003-139">Sipariş belirtilmediyse (varsayılan değer).</span><span class="sxs-lookup"><span data-stu-id="4b003-139">Order isn't specified (default value).</span></span> <span data-ttu-id="4b003-140">Atama yok `Order` (`Order = null`) rota varsayılanları `Order` işleme için 0 (sıfır).</span><span class="sxs-lookup"><span data-stu-id="4b003-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="4b003-141">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="4b003-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="4b003-142">Rota işlem sırasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="4b003-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="4b003-143">Yönlendirme işlemi kurala göre belirlenir:</span><span class="sxs-lookup"><span data-stu-id="4b003-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="4b003-144">Yollar, sıralı olarak işlenir (-1, 0, 1, 2 &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="4b003-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="4b003-145">Yollar olduğunda aynı `Order`en belirli bir yol ilk less yazımına özgü yol tarafından izlenen eşleşir.</span><span class="sxs-lookup"><span data-stu-id="4b003-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="4b003-146">Zaman aynı yollar `Order` ve aynı parametre sayısıyla istek URL'si, yollar, eklemiş sırayla işlenir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span><span class="sxs-lookup"><span data-stu-id="4b003-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="4b003-147">Mümkünse, bağlı olarak belirlenen rota işleme sipariş kaçının.</span><span class="sxs-lookup"><span data-stu-id="4b003-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="4b003-148">Genellikle, yönlendirme ile URL ile eşleşen doğru yolu seçer.</span><span class="sxs-lookup"><span data-stu-id="4b003-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="4b003-149">Rota ayarlamanız gerekirse `Order` uygulamanın yönlendirme düzeni, büyük olasılıkla istemcilere kafa karıştırıcı ve kırılgan korumak için yönlendirmek için özellikler doğru ister.</span><span class="sxs-lookup"><span data-stu-id="4b003-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="4b003-150">Uygulamanın yönlendirme şeması basitleştirmek arama yapın.</span><span class="sxs-lookup"><span data-stu-id="4b003-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="4b003-151">Örnek uygulama, tek bir uygulama kullanarak çeşitli Yönlendirme senaryoları göstermek için sipariş işleme açık bir yol gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="4b003-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="4b003-152">Ancak, uygulama ayarı rotanın önlemek denemelidir `Order` üretim uygulamalarında.</span><span class="sxs-lookup"><span data-stu-id="4b003-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="4b003-153">Yönlendirme razor sayfaları ve MVC denetleyicisi yönlendirme paylaşım uygulaması.</span><span class="sxs-lookup"><span data-stu-id="4b003-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="4b003-154">Rota sırası MVC konularında bilgi şu adreste [denetleyici eylemlerine yönlendirme: Öznitelik rotaları sıralama](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="4b003-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="4b003-155">Model kuralları</span><span class="sxs-lookup"><span data-stu-id="4b003-155">Model conventions</span></span>

<span data-ttu-id="4b003-156">Bir temsilci eklemek <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> eklemek için [model kuralları](xref:mvc/controllers/application-model#conventions) Razor sayfaları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4b003-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="4b003-157">Tüm sayfalar için bir yol modeli Kuralı Ekle</span><span class="sxs-lookup"><span data-stu-id="4b003-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="4b003-158">Kullanım <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> oluşturmak ve eklemek için bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> koleksiyonuna <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sayfasına yönlendirme sırasında uygulanan örnek model oluşturma.</span><span class="sxs-lookup"><span data-stu-id="4b003-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="4b003-159">Örnek uygulamayı ekler bir `{globalTemplate?}` uygulamadaki tüm sayfalar için rota şablonu:</span><span class="sxs-lookup"><span data-stu-id="4b003-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="4b003-160"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> Özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> ayarlanır `1`.</span><span class="sxs-lookup"><span data-stu-id="4b003-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="4b003-161">Bu örnek uygulamada davranışı aşağıdaki yol sağlar:</span><span class="sxs-lookup"><span data-stu-id="4b003-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="4b003-162">Bir rota şablonu için `TheContactPage/{text?}` konusunda daha sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="4b003-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="4b003-163">İlgili kişi sayfası yol varsayılan sıralamasını sahip `null` (`Order = 0`), önce eşleşecek şekilde `{globalTemplate?}` rota şablonu.</span><span class="sxs-lookup"><span data-stu-id="4b003-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="4b003-164">Bir `{aboutTemplate?}` rota şablonu konusunda daha sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="4b003-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="4b003-165">`{aboutTemplate?}` Şablon verildiğinde bir `Order` , `2`.</span><span class="sxs-lookup"><span data-stu-id="4b003-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="4b003-166">Ne zaman hakkında sayfası istenen adresindeki `/About/RouteDataValue`, "RouteDataValue" içine yüklenir `RouteData.Values["globalTemplate"]` (`Order = 1`) ve `RouteData.Values["aboutTemplate"]` (`Order = 2`) ayarı nedeniyle `Order` özelliği.</span><span class="sxs-lookup"><span data-stu-id="4b003-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="4b003-167">Bir `{otherPagesTemplate?}` rota şablonu konusunda daha sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="4b003-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="4b003-168">`{otherPagesTemplate?}` Şablon verildiğinde bir `Order` , `2`.</span><span class="sxs-lookup"><span data-stu-id="4b003-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="4b003-169">Ne zaman tüm sayfa içinde *sayfaları/OtherPages* klasörü ile bir rota parametresini istenen (örneğin, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" içine yüklenir `RouteData.Values["globalTemplate"]` (`Order = 1`) ve `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) ayarı nedeniyle `Order` özelliği.</span><span class="sxs-lookup"><span data-stu-id="4b003-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="4b003-170">Mümkün olduğunda, ayarlamamanız `Order`, içindeki sonuçlar `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="4b003-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="4b003-171">Doğru yol seçmek için yönlendirme kullanır.</span><span class="sxs-lookup"><span data-stu-id="4b003-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="4b003-172">Razor sayfaları seçenekleri ekleme gibi <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, MVC hizmet koleksiyona eklendiğinde, eklenen `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4b003-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4b003-173">Bir örnek için bkz. [örnek uygulaması](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="4b003-173">For an example, see the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="4b003-174">Örnek kullanıcının hakkında sayfası, istek `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="4b003-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Hakkında sayfası GlobalRouteValue ile bir yol kesimi istenir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="4b003-177">Tüm sayfalar için bir uygulama modeli Kuralı Ekle</span><span class="sxs-lookup"><span data-stu-id="4b003-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="4b003-178">Kullanım <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> oluşturmak ve eklemek için bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> koleksiyonuna <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sayfasında uygulama modeli oluşturma sırasında uygulanan örnekleri.</span><span class="sxs-lookup"><span data-stu-id="4b003-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="4b003-179">Bu ve diğer kuralları daha sonra bu konudaki göstermek için örnek uygulamayı içeren bir `AddHeaderAttribute` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="4b003-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="4b003-180">Sınıf oluşturucu kabul eden bir `name` dize ve `values` dize dizisi.</span><span class="sxs-lookup"><span data-stu-id="4b003-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="4b003-181">Bu değerler kullanılır, `OnResultExecuting` yöntemi yanıt üst bilgisini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4b003-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="4b003-182">Tam sınıf gösterilen [sayfa modeli eylem kuralları](#page-model-action-conventions) konunun ilerleyen bölümlerinde.</span><span class="sxs-lookup"><span data-stu-id="4b003-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="4b003-183">Örnek uygulama kullandığı `AddHeaderAttribute` üst bilgi eklemek için sınıfı `GlobalHeader`, uygulamadaki tüm sayfalar için:</span><span class="sxs-lookup"><span data-stu-id="4b003-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="4b003-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4b003-184">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="4b003-185">Örnek kullanıcının hakkında sayfası, istek `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="4b003-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Yanıt Üstbilgileri hakkında sayfasının GlobalHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="4b003-187">Tüm sayfalar için bir işleyici modeli Kuralı Ekle</span><span class="sxs-lookup"><span data-stu-id="4b003-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="4b003-188">Kullanım <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> oluşturmak ve eklemek için bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> koleksiyonuna <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sırasında uygulanan örnek işleyici modeli oluşturma sayfası.</span><span class="sxs-lookup"><span data-stu-id="4b003-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="4b003-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4b003-189">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="4b003-190">Rota eylem kuralları sayfası</span><span class="sxs-lookup"><span data-stu-id="4b003-190">Page route action conventions</span></span>

<span data-ttu-id="4b003-191">Öğesinden türetilen varsayılan rota model sağlayıcısı <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> sayfa yolları yapılandırmak için genişletilebilirlik noktaları sağlamak için tasarlanmış kuralları çağırır.</span><span class="sxs-lookup"><span data-stu-id="4b003-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="4b003-192">Klasör yolu model kuralı</span><span class="sxs-lookup"><span data-stu-id="4b003-192">Folder route model convention</span></span>

<span data-ttu-id="4b003-193">Kullanım <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> oluşturmak ve eklemek için bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , çağıran bir eylem üzerinde <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> belirtilen klasör altındaki tüm sayfalar için.</span><span class="sxs-lookup"><span data-stu-id="4b003-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="4b003-194">Örnek uygulama kullandığı <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> eklemek için bir `{otherPagesTemplate?}` sayfaları için rota şablonu *OtherPages* klasörü:</span><span class="sxs-lookup"><span data-stu-id="4b003-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="4b003-195"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> Özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> ayarlanır `2`.</span><span class="sxs-lookup"><span data-stu-id="4b003-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="4b003-196">Bu şablonu sağlar `{globalTemplate?}` (önceki konuya ayarlamak `1`) için tek yol değeri sağlandığında konumu ilk rota veri değeri öncelik verilir.</span><span class="sxs-lookup"><span data-stu-id="4b003-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="4b003-197">Bir sayfa varsa *sayfaları/OtherPages* klasör yolu bir parametre istenen (örneğin, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" içine yüklenir `RouteData.Values["globalTemplate"]` (`Order = 1`) ve `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) ayarı nedeniyle `Order` özelliği.</span><span class="sxs-lookup"><span data-stu-id="4b003-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="4b003-198">Mümkün olduğunda, ayarlamamanız `Order`, içindeki sonuçlar `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="4b003-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="4b003-199">Doğru yol seçmek için yönlendirme kullanır.</span><span class="sxs-lookup"><span data-stu-id="4b003-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="4b003-200">Örnek kullanıcının Sayfa1 sayfanın istek `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="4b003-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages klasöründe Sayfa1 GlobalRouteValue ve OtherPagesRouteValue yol kesimini ile istenir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="4b003-203">Sayfa yolu model kuralı</span><span class="sxs-lookup"><span data-stu-id="4b003-203">Page route model convention</span></span>

<span data-ttu-id="4b003-204">Kullanım <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> oluşturmak ve eklemek için bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , çağıran bir eylem üzerinde <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> sayfanın belirtilen ada sahip.</span><span class="sxs-lookup"><span data-stu-id="4b003-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="4b003-205">Örnek uygulama kullandığı `AddPageRouteModelConvention` eklemek için bir `{aboutTemplate?}` hakkında sayfasına rota şablonu:</span><span class="sxs-lookup"><span data-stu-id="4b003-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="4b003-206"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> Özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> ayarlanır `2`.</span><span class="sxs-lookup"><span data-stu-id="4b003-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="4b003-207">Bu şablonu sağlar `{globalTemplate?}` (önceki konuya ayarlamak `1`) için tek yol değeri sağlandığında konumu ilk rota veri değeri öncelik verilir.</span><span class="sxs-lookup"><span data-stu-id="4b003-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="4b003-208">Bir rota parametresi değer ile hakkında sayfası istenirse, `/About/RouteDataValue`, "RouteDataValue" içine yüklenir `RouteData.Values["globalTemplate"]` (`Order = 1`) ve `RouteData.Values["aboutTemplate"]` (`Order = 2`) ayarı nedeniyle `Order` özelliği.</span><span class="sxs-lookup"><span data-stu-id="4b003-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="4b003-209">Mümkün olduğunda, ayarlamamanız `Order`, içindeki sonuçlar `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="4b003-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="4b003-210">Doğru yol seçmek için yönlendirme kullanır.</span><span class="sxs-lookup"><span data-stu-id="4b003-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="4b003-211">Örnek kullanıcının hakkında sayfası, istek `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="4b003-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![Sayfa hakkında GlobalRouteValue ve AboutRouteValue için yol kesimleri ile istenir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="4b003-214">Sayfa yollar özelleştirmek için bir parametre transformer kullanma</span><span class="sxs-lookup"><span data-stu-id="4b003-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="4b003-215">ASP.NET Core tarafından oluşturulan sayfa yollar, bir parametre transformer kullanma özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="4b003-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="4b003-216">Bir parametre transformer uygulayan `IOutboundParameterTransformer` ve parametre değerine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="4b003-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="4b003-217">Örneğin, bir özel `SlugifyParameterTransformer` parametre transformer değişiklikleri `SubscriptionManagement` yönlendirmek için değer `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="4b003-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="4b003-218">`PageRouteTransformerConvention` Sayfa rota modeli kuralı bir uygulamayı otomatik olarak oluşturulan sayfa yollar klasör ve dosya adı kesimlerini parametresi transformer uygular.</span><span class="sxs-lookup"><span data-stu-id="4b003-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="4b003-219">Örneğin, Razor sayfaları dosya */Pages/SubscriptionManagement/ViewAll.cshtml* gelen yeniden yolunu gerekir `/SubscriptionManagement/ViewAll` için `/subscription-management/view-all`.</span><span class="sxs-lookup"><span data-stu-id="4b003-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="4b003-220">`PageRouteTransformerConvention` Razor sayfaları klasör ve dosya adı gelirler otomatik olarak oluşturulan bir sayfa yolu parçalarını yalnızca dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="4b003-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="4b003-221">Yol kesimleri ile eklenen dönüştürmez `@page` yönergesi.</span><span class="sxs-lookup"><span data-stu-id="4b003-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="4b003-222">Kuralı tarafından eklenen rotaları da dönüştürmez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span><span class="sxs-lookup"><span data-stu-id="4b003-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="4b003-223">`PageRouteTransformerConvention` Bir seçenek olarak kayıtlı `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4b003-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add(
                    new PageRouteTransformerConvention(
                        new SlugifyParameterTransformer()));
            });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end

## <a name="configure-a-page-route"></a><span data-ttu-id="4b003-224">Bir sayfa yolunu Yapılandır</span><span class="sxs-lookup"><span data-stu-id="4b003-224">Configure a page route</span></span>

<span data-ttu-id="4b003-225">Kullanım <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> belirtilen sayfa yolunda bir sayfa için bir yol yapılandırmak için.</span><span class="sxs-lookup"><span data-stu-id="4b003-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="4b003-226">Oluşturulan Bağlantılar sayfasını, belirtilen rota kullanın.</span><span class="sxs-lookup"><span data-stu-id="4b003-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="4b003-227">`AddPageRoute` kullanan `AddPageRouteModelConvention` rota oluşturmak için.</span><span class="sxs-lookup"><span data-stu-id="4b003-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="4b003-228">Örnek uygulama için bir yol oluşturur `/TheContactPage` için *Contact.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4b003-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="4b003-229">İlgili kişi sayfası aynı zamanda adresinden ulaşılabilir `/Contact` aracılığıyla varsayılan yolu.</span><span class="sxs-lookup"><span data-stu-id="4b003-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="4b003-230">İlgili kişi sayfası örnek uygulamanın özel yol için bir isteğe bağlı sağlar `text` yol kesimi (`{text?}`).</span><span class="sxs-lookup"><span data-stu-id="4b003-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="4b003-231">Sayfa Ayrıca bu isteğe bağlı bir segmente içerir, `@page` ziyaretçi sayfasına erişen durumunda yönerge kendi `/Contact` rota:</span><span class="sxs-lookup"><span data-stu-id="4b003-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="4b003-232">URL için oluşturulan Not **kişi** işlenen sayfanın bağlantısını yansıtan güncelleştirilmiş yolu:</span><span class="sxs-lookup"><span data-stu-id="4b003-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Gezinti çubuğunda, uygulama kişi bağlantısı örneği](razor-pages-conventions/_static/contact-link.png)

![İşlenmiş HTML kişi bağlantıya inceleyerek gösterir href ayarlamak ' / TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="4b003-235">İlgili kişi sayfasını ya da kendi sıradan rota ziyaret edin `/Contact`, ya da özel rota `/TheContactPage`.</span><span class="sxs-lookup"><span data-stu-id="4b003-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="4b003-236">Ek bir sağlarsanız `text` yol kesimi sayfada gösterilir HTML ile kodlanmış segment sağlamanız:</span><span class="sxs-lookup"><span data-stu-id="4b003-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL'deki 'MetinDeğeri' bir isteğe bağlı 'text' yol kesimi sağlama edge tarayıcı örneği.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="4b003-239">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="4b003-239">Page model action conventions</span></span>

<span data-ttu-id="4b003-240">Uygulayan varsayılan sayfa modeli sağlayıcısı <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sayfa modelleri yapılandırmak için genişletilebilirlik noktaları sağlamak için tasarlanmış kuralları çağırır.</span><span class="sxs-lookup"><span data-stu-id="4b003-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="4b003-241">Bu kuralları oluştururken ve sayfa bulma ve işleme senaryoları değiştirme yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="4b003-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="4b003-242">Bu bölümdeki örnekler için örnek uygulamayı kullanan bir `AddHeaderAttribute` sınıfının bir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, bir yanıt üstbilgisi, geçerli:</span><span class="sxs-lookup"><span data-stu-id="4b003-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="4b003-243">Kuralları kullanarak örnek bir klasördeki tüm sayfalar için ve tek sayfalı bir öznitelik uygulamak nasıl gösterir.</span><span class="sxs-lookup"><span data-stu-id="4b003-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="4b003-244">**Klasör uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="4b003-244">**Folder app model convention**</span></span>

<span data-ttu-id="4b003-245">Kullanım <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> oluşturmak ve eklemek için bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , çağıran bir eylem üzerinde <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> belirtilen klasör altındaki tüm sayfalar için örnekleri.</span><span class="sxs-lookup"><span data-stu-id="4b003-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="4b003-246">Örnek, kullanımını gösterir. `AddFolderApplicationModelConvention` bir üst bilgisi ekleyerek `OtherPagesHeader`, içinde sayfalara *OtherPages* uygulama klasörü:</span><span class="sxs-lookup"><span data-stu-id="4b003-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="4b003-247">Örnek kullanıcının Sayfa1 sayfanın istek `localhost:5000/OtherPages/Page1` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="4b003-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Yanıt Üstbilgileri OtherPages/Sayfa1 sayfanın OtherPagesHeader eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="4b003-249">**Sayfa uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="4b003-249">**Page app model convention**</span></span>

<span data-ttu-id="4b003-250">Kullanım <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> oluşturmak ve eklemek için bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , çağıran bir eylem üzerinde <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> sayfanın belirtilen ada sahip.</span><span class="sxs-lookup"><span data-stu-id="4b003-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="4b003-251">Örnek, kullanımını gösterir. `AddPageApplicationModelConvention` bir üst bilgisi ekleyerek `AboutHeader`, hakkında sayfası:</span><span class="sxs-lookup"><span data-stu-id="4b003-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="4b003-252">Örnek kullanıcının hakkında sayfası, istek `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="4b003-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Yanıt Üstbilgileri hakkında sayfasının AboutHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="4b003-254">**Bir filtre yapılandırın**</span><span class="sxs-lookup"><span data-stu-id="4b003-254">**Configure a filter**</span></span>

<span data-ttu-id="4b003-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> Belirtilen filtre uygulamak için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="4b003-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="4b003-256">Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama, Sahne Arkası filtre döndüren bir Fabrika olarak uygulanan bir lambda ifadesinde bir filtre uygulamak gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="4b003-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="4b003-257">Sayfa uygulama modeli Page2 sayfasına yol kesimleri için göreli yolu denetlemek için kullanılan *OtherPages* klasör.</span><span class="sxs-lookup"><span data-stu-id="4b003-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="4b003-258">Koşul başarılı olursa, bir üst bilgi eklenir.</span><span class="sxs-lookup"><span data-stu-id="4b003-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="4b003-259">Aksi takdirde, `EmptyFilter` uygulanır.</span><span class="sxs-lookup"><span data-stu-id="4b003-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="4b003-260">`EmptyFilter` olan bir [eylem filtresi](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="4b003-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="4b003-261">Eylem filtreleri Razor sayfaları tarafından göz ardı edilir beri `EmptyFilter` no-yolu içermiyor beklendiği gibi ops `OtherPages/Page2`.</span><span class="sxs-lookup"><span data-stu-id="4b003-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` no-ops as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="4b003-262">Örnek kullanıcının Page2 sayfanın istek `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="4b003-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header Page2 için yanıta eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="4b003-264">**Bir filtre fabrikayı yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="4b003-264">**Configure a filter factory**</span></span>

<span data-ttu-id="4b003-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> Belirtilen Üreteç uygulamak için yapılandırır [filtreleri](xref:mvc/controllers/filters) tüm Razor sayfaları için.</span><span class="sxs-lookup"><span data-stu-id="4b003-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="4b003-266">Örnek uygulamasını kullanarak bir örnek sağlar. bir [filtre Fabrika](xref:mvc/controllers/filters#ifilterfactory) bir üst bilgisi ekleyerek `FilterFactoryHeader`, uygulamanın sayfaları için iki değerleriyle:</span><span class="sxs-lookup"><span data-stu-id="4b003-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="4b003-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="4b003-267">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="4b003-268">Örnek kullanıcının hakkında sayfası, istek `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="4b003-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![İki FilterFactoryHeader üst bilgileri eklendiğinden hakkında sayfasının yanıt üst bilgileri gösterin.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="4b003-270">MVC filtreleri ve sayfa filtresi (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="4b003-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="4b003-271">MVC [eylem filtrelerini](xref:mvc/controllers/filters#action-filters) Razor sayfaları işleyici yöntemleri kullandığından Razor sayfaları tarafından göz ardı edilir.</span><span class="sxs-lookup"><span data-stu-id="4b003-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="4b003-272">MVC filtre diğer türleri kullanabilmeniz için kullanılabilir: [Yetkilendirme](xref:mvc/controllers/filters#authorization-filters), [özel durum](xref:mvc/controllers/filters#exception-filters), [kaynak](xref:mvc/controllers/filters#resource-filters), ve [sonucu](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="4b003-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="4b003-273">Daha fazla bilgi için [filtreleri](xref:mvc/controllers/filters) konu.</span><span class="sxs-lookup"><span data-stu-id="4b003-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="4b003-274">Sayfa filtresi (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) Razor sayfaları için geçerli bir filtredir.</span><span class="sxs-lookup"><span data-stu-id="4b003-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="4b003-275">Daha fazla bilgi için [yöntemleri Razor sayfaları için filtre](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="4b003-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b003-276">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4b003-276">Additional resources</span></span>

* [<span data-ttu-id="4b003-277">Razor Pages yetkilendirme kuralları</span><span class="sxs-lookup"><span data-stu-id="4b003-277">Razor Pages authorization conventions</span></span>](xref:security/authorization/razor-pages-authorization)
