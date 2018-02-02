---
title: "Denetleyici eylemleri için yönlendirme"
author: rick-anderson
description: 
manager: wpickett
ms.author: riande
ms.date: 03/14/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: mvc/controllers/routing
ms.openlocfilehash: d87cb50871b956c51045558d2e4f076de4211f81
ms.sourcegitcommit: f2a11a89037471a77ad68a67533754b7bb8303e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 02/01/2018
---
# <a name="routing-to-controller-actions"></a><span data-ttu-id="1bb6b-102">Denetleyici eylemleri için yönlendirme</span><span class="sxs-lookup"><span data-stu-id="1bb6b-102">Routing to Controller Actions</span></span>

<span data-ttu-id="1bb6b-103">Tarafından [Ryan Nowak](https://github.com/rynowak) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1bb6b-103">By [Ryan Nowak](https://github.com/rynowak) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1bb6b-104">ASP.NET Core MVC kullanan yönlendirme [ara yazılım](xref:fundamentals/middleware/index) gelen istekleri URL'lerini eşleşen ve eylemlere eşlemek için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-104">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="1bb6b-105">Yollar başlatma kodunu veya öznitelikleri tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-105">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="1bb6b-106">Yollar URL yollarını eylemler için nasıl eşleştirilmesi gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-106">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="1bb6b-107">Yollar yanıtları gönderilen (Bağlantılar) URL'lerini oluşturmak için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-107">Routes are also used to generate URLs (for links) sent out in responses.</span></span> 

<span data-ttu-id="1bb6b-108">Öznitelik yönlendirilebilir veya Eylemler işleme, genel yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-108">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="1bb6b-109">Bir rota denetleyici veya eylem getirir, yönlendirilmiş özniteliği.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-109">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="1bb6b-110">Bkz: [yönlendirme karma](#routing-mixed-ref-label) daha fazla bilgi için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-110">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="1bb6b-111">Bu belge, MVC ve Yönlendirme ve nasıl tipik MVC uygulamaları oluşturma arasındaki etkileşimler anlatılmıştır yönlendirme özelliklerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-111">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="1bb6b-112">Bkz: [yönlendirme](xref:fundamentals/routing) Gelişmiş yönlendirme ile ilgili ayrıntılar için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-112">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="1bb6b-113">Ara yazılım yönlendirme ayarlama</span><span class="sxs-lookup"><span data-stu-id="1bb6b-113">Setting up Routing Middleware</span></span>

<span data-ttu-id="1bb6b-114">İçinde *yapılandırma* yöntemi için benzer bir kod da karşılaşabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-114">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="1bb6b-115">Çağrısı içinde `UseMvc`, `MapRoute` olarak adlandırılan tek bir yol oluşturmak için kullanılan `default` rota.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-115">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="1bb6b-116">Çoğu MVC uygulamaları için benzer bir şablonla bir yol kullanacak `default` rota.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-116">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="1bb6b-117">Rota şablonu `"{controller=Home}/{action=Index}/{id?}"` gibi bir URL yolu eşleştirebilirsiniz `/Products/Details/5` ve rota değerlerini ayıklar `{ controller = Products, action = Details, id = 5 }` yolu tokenizing tarafından.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-117">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="1bb6b-118">MVC bulmaya çalışır adlı bir denetleyicisi `ProductsController` eylemi çalıştırıp `Details`:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-118">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="1bb6b-119">Bu örnekte, değeri, model bağlama kullanırsınız Not `id = 5` ayarlamak için `id` parametresi `5` bu eylemi çağrılırken.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-119">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="1bb6b-120">Bkz: [Model bağlama](../models/model-binding.md) daha fazla ayrıntı için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-120">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="1bb6b-121">Kullanarak `default` yol:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-121">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="1bb6b-122">Rota şablonu:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-122">The route template:</span></span>

* <span data-ttu-id="1bb6b-123">`{controller=Home}`tanımlar `Home` varsayılan olarak`controller`</span><span class="sxs-lookup"><span data-stu-id="1bb6b-123">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="1bb6b-124">`{action=Index}`tanımlar `Index` varsayılan olarak`action`</span><span class="sxs-lookup"><span data-stu-id="1bb6b-124">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="1bb6b-125">`{id?}`tanımlar `id` isteğe bağlı olarak</span><span class="sxs-lookup"><span data-stu-id="1bb6b-125">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="1bb6b-126">Varsayılan ve isteğe bağlı rota parametreler, eşleşen bir URL yolu bulunması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-126">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="1bb6b-127">Bkz: [rota şablon başvurusu](../../fundamentals/routing.md#route-template-reference) rota şablon söz dizimi ayrıntılı bir açıklaması.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-127">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="1bb6b-128">`"{controller=Home}/{action=Index}/{id?}"`URL yolunu eşleştirebilirsiniz `/` ve rota değerleri üretecektir `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-128">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="1bb6b-129">Değerleri `controller` ve `action` olun varsayılan değerini kullanmak `id` URL yolu ilgili segment yok olduğundan bir değeri oluşturmuyor.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-129">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="1bb6b-130">MVC kullandığınız bu rota değerlerini seçmek için `HomeController` ve `Index` eylem:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-130">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="1bb6b-131">Bu denetleyici tanımı ve rota şablonu kullanarak `HomeController.Index` eylem yürütülebilir herhangi aşağıdaki URL yolu için:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-131">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="1bb6b-132">Kolaylık metodunun `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-132">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="1bb6b-133">Değiştirmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-133">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="1bb6b-134">`UseMvc`ve `UseMvcWithDefaultRoute` bir örnek ekler `RouterMiddleware` ara yazılım ardışık düzenini için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-134">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="1bb6b-135">MVC doğrudan ara yazılımı ile etkileşim değil ve yönlendirme isteklerini işlemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-135">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="1bb6b-136">MVC örneği ile yolların bağlı `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-136">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="1bb6b-137">Kod içinde `UseMvc` aşağıdakine benzer:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-137">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="1bb6b-138">`UseMvc`tüm yollar doğrudan tanımlamıyor rota koleksiyonu için bir yer tutucu ekler `attribute` rota.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-138">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="1bb6b-139">Aşırı `UseMvc(Action<IRouteBuilder>)` kendi yollar eklemenize olanak sağlayan ve ayrıca özniteliği yönlendirmeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-139">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="1bb6b-140">`UseMvc`ve tüm alt çeşitleri ekler özniteliği yol için bir yer tutucu - özniteliği yönlendirme her zaman nasıl yapılandırdığınıza bakmaksızın kullanılabilir `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-140">`UseMvc` and all of its variations adds a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="1bb6b-141">`UseMvcWithDefaultRoute`Varsayılan rota tanımlar ve öznitelik yönlendirmeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-141">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="1bb6b-142">[Özniteliği yönlendirme](#attribute-routing-ref-label) bölüm özniteliği yönlendirme hakkında daha fazla ayrıntı içerir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-142">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="1bb6b-143">Geleneksel yönlendirme</span><span class="sxs-lookup"><span data-stu-id="1bb6b-143">Conventional routing</span></span>

<span data-ttu-id="1bb6b-144">`default` Yol:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-144">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="1bb6b-145">örneği bir *geleneksel yönlendirme*.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-145">is an example of a *conventional routing*.</span></span> <span data-ttu-id="1bb6b-146">Bu stili diyoruz *geleneksel yönlendirme* , çünkü bir *kuralı* URL yolları için:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-146">We call this style *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="1bb6b-147">İlk yol kesimi Denetleyici adı eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="1bb6b-147">the first path segment maps to the controller name</span></span>

* <span data-ttu-id="1bb6b-148">Eylem adı ikinci eşlenir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-148">the second maps to the action name.</span></span>

* <span data-ttu-id="1bb6b-149">Üçüncü segment isteğe için kullanılan `id` modeli varlığa eşlemek için kullanılır</span><span class="sxs-lookup"><span data-stu-id="1bb6b-149">the third segment is used for an optional `id` used to map to a model entity</span></span>

<span data-ttu-id="1bb6b-150">Bu kullanarak `default` yol, URL yolunu `/Products/List` eşlendiği `ProductsController.List` eylemi ve `/Blog/Article/17` eşlendiği `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-150">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="1bb6b-151">Bu eşleme denetleyici ve eylem adları temelinde **yalnızca** ve ad alanları, kaynak dosya konumları veya yöntem parametreleri göre değil.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-151">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="1bb6b-152">Geleneksel olan varsayılan yol yönlendirme kullanmak için tanımladığınız her eylem için yeni bir URL düzendeki gündeme gerek kalmadan uygulama hızlı bir şekilde oluşturmanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-152">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="1bb6b-153">CRUD stili eylemleri içeren bir uygulama için tutarlılık için URL'leri denetleyicilerinizi arasında sahip olmak, kodunuzu basitleştirerek UI daha öngörülebilir hale yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-153">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="1bb6b-154">`id` İsteğe bağlı olarak eylemlerinizi URL'SİNİN bir parçası sağlanan kimliği olmadan yürütebilir anlamı rota şablonu tarafından tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-154">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="1bb6b-155">Genellikle ne olursa olacağını `id` URL'den atlanmış onu ayarlanacak emin olan `0` model bağlama tarafından ve hiçbir varlık veritabanı eşleşen bulunabilir sonuç olarak `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-155">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="1bb6b-156">Öznitelik yönlendirme bazı eylemler için ve başkaları için gerekli kimlik yapmak için hassas bir denetim verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-156">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="1bb6b-157">İsteğe bağlı parametreler belgelere içereceği kurala göre ister `id` olduğunda bunlar büyük bir olasılıkla doğru kullanımı görünür.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-157">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="1bb6b-158">Birden çok yol</span><span class="sxs-lookup"><span data-stu-id="1bb6b-158">Multiple routes</span></span>

<span data-ttu-id="1bb6b-159">İçinde birden çok yol ekleyebilirsiniz `UseMvc` daha fazla aramaları ekleyerek `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-159">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="1bb6b-160">Bunun yapılması, birden çok kuralları tanımlamak ya da belirli bir eylemi gibi ayrılmış geleneksel yollar eklemeniz sağlar:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-160">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="1bb6b-161">`blog` Burada yol bir *ayrılmış geleneksel rota*, onu geleneksel yönlendirme sistem kullanır ancak belirli bir eylemi ayrılmış anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-161">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="1bb6b-162">Bu yana `controller` ve `action` rota şablonu parametreleri olarak görünmez, yalnızca varsayılan değerleri olabilir ve bu nedenle bu rota için eylem her zaman eşler `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-162">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="1bb6b-163">Rota koleksiyonu yollar sıralanır ve eklenen sırada işlenir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-163">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="1bb6b-164">Bu örnekte, bunu `blog` rota çalıştı önce `default` rota.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-164">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="1bb6b-165">*Geleneksel yollar ayrılmış* catch tüm rota parametrelerinin gibi sık kullandığınız `{*article}` URL yolunu geri kalan bölümü yakalama için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-165">*Dedicated conventional routes* often use catch-all route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="1bb6b-166">Bu bir rota 'çok doyumsuz' yapabilirsiniz diğer yollar eşleştirilmesini hedeflenen URL'leri eşleşen anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-166">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="1bb6b-167">'Doyumsuz' yollar bunu çözmek için daha sonra rota tablosunda yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-167">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="1bb6b-168">Geri dönüş</span><span class="sxs-lookup"><span data-stu-id="1bb6b-168">Fallback</span></span>

<span data-ttu-id="1bb6b-169">İstek işleme bir parçası olarak, MVC, rota değerleri, uygulamanızda bir denetleyici ve eylem bulmak için kullanılabilir doğrular.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-169">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="1bb6b-170">Rota değerleri bir eylem eşleşmiyorsa sonra rota bir eşleşme olarak değil ve sonraki yol denenir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-170">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="1bb6b-171">Bu adlı *geri dönüş*, ve geleneksel yollar çakıştığı durumlarda basitleştirmek hedeflenen.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-171">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="1bb6b-172">Belirsizliği Eylemler</span><span class="sxs-lookup"><span data-stu-id="1bb6b-172">Disambiguating actions</span></span>

<span data-ttu-id="1bb6b-173">İki eylem Yönlendirme aracılığıyla eşleştiğinde, MVC 'en iyi' adayı seçin veya başka bir özel durum için ayırt etmek gerekir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-173">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="1bb6b-174">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-174">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="1bb6b-175">Bu denetleyici URL yolunu eşleşir iki eylemleri tanımlar `/Products/Edit/17` ve rota verilerini `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-175">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="1bb6b-176">MVC denetleyicileri için genel bir desen budur nerede `Edit(int)` bir ürünü düzenlemek için bir form gösterir ve `Edit(int, Product)` gönderilen formu işler.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-176">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="1bb6b-177">Bu olası MVC seçmek yapmanız `Edit(int, Product)` bir HTTP istek olduğunda `POST` ve `Edit(int)` HTTP fiili olduğunda başka bir şey.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-177">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="1bb6b-178">`HttpPostAttribute` ( `[HttpPost]` ) Uygulamasıdır `IActionConstraint` , yalnızca izni verdiği HTTP fiili olduğunda, seçili eylem `POST`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-178">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="1bb6b-179">Varlığını bir `IActionConstraint` yapar `Edit(int, Product)` 'daha iyi' eşleşen daha `Edit(int)`, bu nedenle `Edit(int, Product)` ilk olarak denenir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-179">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="1bb6b-180">Yalnızca özel yazma gerekir `IActionConstraint` özel senaryoları, ancak uygulamalarında gibi özniteliklere rolünü anlamak önemlidir `HttpPostAttribute` -benzer öznitelikleri için diğer HTTP fiilleri tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-180">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="1bb6b-181">Geleneksel yönlendirme parçası olduğunuzda aynı eylem adı kullanmak eylemler için yaygındır bir `show form -> submit form` iş akışı.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-181">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="1bb6b-182">Bu desen kolaylık gözden geçirdikten sonra daha belirgin hale gelecek [anlama IActionConstraint](#understanding-iactionconstraint) bölümü.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-182">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="1bb6b-183">Birden çok yol eşleşen ve MVC 'en iyi' yolu bulunamıyor, throw bir `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-183">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="1bb6b-184">Yol adları</span><span class="sxs-lookup"><span data-stu-id="1bb6b-184">Route names</span></span>

<span data-ttu-id="1bb6b-185">Dizeleri `"blog"` ve `"default"` aşağıdaki örneklerde rota adları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-185">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>


```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="1bb6b-186">Rota adları, rota mantıksal bir ad verin adlandırılmış rota URL üretmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-186">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="1bb6b-187">Yolların sıralama URL nesil karmaşık hale getirebilecek olduğunda bu URL oluşturma büyük ölçüde basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-187">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="1bb6b-188">Rota adları benzersiz uygulama kapsamında olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-188">Route names must be unique application-wide.</span></span>

<span data-ttu-id="1bb6b-189">Rota adları eşleşen veya istekleri işleme URL üzerinde bir etkisi yok; Bunlar yalnızca URL oluşturma için kullanılırlar.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-189">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="1bb6b-190">[Yönlendirme](xref:fundamentals/routing) MVC özgü Yardımcıları URL oluşturma dahil olmak üzere URL oluşturma hakkında ayrıntılı bilgi vardır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-190">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="1bb6b-191">Öznitelik yönlendirme</span><span class="sxs-lookup"><span data-stu-id="1bb6b-191">Attribute routing</span></span>

<span data-ttu-id="1bb6b-192">Öznitelik yönlendirme eylemleri doğrudan rota şablonlarının eşlemek için öznitelikler kümesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-192">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="1bb6b-193">Aşağıdaki örnekte, `app.UseMvc();` kullanılan `Configure` yöntemi ve hiçbir rota geçirilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-193">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="1bb6b-194">`HomeController` URL'leri hangi varsayılan yol için benzer bir dizi eşleşir `{controller=Home}/{action=Index}/{id?}` eşleşir:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-194">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="1bb6b-195">`HomeController.Index()` Herhangi bir URL yolu için eylem yürütülür `/`, `/Home`, veya `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-195">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="1bb6b-196">Bu örnekte, öznitelik Yönlendirme ve geleneksel yönlendirme anahtar programlama birbirinden vurgular.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-196">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="1bb6b-197">Öznitelik yönlendirme bir yol belirtmek için daha fazla girdi gerektirir; Geleneksel varsayılan yol yolları daha temellerini işler.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-197">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="1bb6b-198">Bununla birlikte, öznitelik yönlendirme sağlar (ve gerektirir) hangi rota şablonlarının her işlem için geçerli kesin bir denetim.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-198">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="1bb6b-199">Denetleyici adı ve eylem adları yönlendirme özniteliğiyle yürütmek **hiçbir** rol eylem seçilidir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-199">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="1bb6b-200">Bu örnek önceki örnekteki gibi aynı URL'ler ile eşleşir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-200">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="1bb6b-201">Rota şablonlarının yukarıdaki rota parametrelerini tanımlayın yok `action`, `area`, ve `controller`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-201">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="1bb6b-202">Aslında, bu rota parametrelerinin öznitelik rotaları izin verilmiyor.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-202">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="1bb6b-203">Rota şablonu zaten bir eylem ile ilişkili olduğundan, bu eylem adı URL'den ayrıştırmak için anlamlı olmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-203">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="1bb6b-204">HTTP [eylem] özniteliklerle yönlendirme özniteliği</span><span class="sxs-lookup"><span data-stu-id="1bb6b-204">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="1bb6b-205">Öznitelik yönlendirme de yapabilirsiniz kullanımı `Http[Verb]` gibi öznitelikleri `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-205">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="1bb6b-206">Tüm bu özniteliklerin bir rota şablonu kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-206">All of these attributes can accept a route template.</span></span> <span data-ttu-id="1bb6b-207">Bu örnek aynı yol şablonu eşleşen iki eylemleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-207">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="1bb6b-208">Gibi bir URL yolu için `/products` `ProductsApi.ListProducts` HTTP fiili olduğunda eylem yürütülür `GET` ve `ProductsApi.CreateProduct` HTTP fiili olduğunda yürütülecek `POST`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-208">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="1bb6b-209">Öznitelik ilk yönlendirme URL rota öznitelikleri tarafından tanımlanan rota şablonlarının dizi eşleşir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-209">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="1bb6b-210">Eşleşen bir rota şablonu sonra `IActionConstraint` kısıtlamaları hangi eylemleri yürütülebilecek belirlemek için uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-210">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="1bb6b-211">Bir REST API oluştururken, kullanmak istediğiniz nadir `[Route(...)]` bir eylem yöntemi.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-211">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method.</span></span> <span data-ttu-id="1bb6b-212">Daha fazla özel kullanmak en iyisidir `Http*Verb*Attributes` API'nizi destekleyen hakkında kesin olarak.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-212">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="1bb6b-213">REST API'leri istemcileri için belirli mantıksal işlemler ne yolları ve HTTP fiillerini eşleme bilmeniz beklenir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-213">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="1bb6b-214">Öznitelik rotası belirli bir eylem için geçerli olduğundan, rota şablonu tanımının bir parçası gerekli parametreleri yapmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-214">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="1bb6b-215">Bu örnekte, `id` URL yolu bir parçası olarak gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-215">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="1bb6b-216">`ProductsApi.GetProduct(int)` Eylem, URL yolu gibi yürütülür `/products/3` ancak gibi bir URL yolu için `/products`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-216">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="1bb6b-217">Bkz: [yönlendirme](../../fundamentals/routing.md) rota şablonlarının ve ilgili seçenekleri tam bir açıklaması.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-217">See [Routing](../../fundamentals/routing.md) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="1bb6b-218">Rota adı</span><span class="sxs-lookup"><span data-stu-id="1bb6b-218">Route Name</span></span>

<span data-ttu-id="1bb6b-219">Aşağıdaki kodu tanımlayan bir *rota adı* , `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-219">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="1bb6b-220">Rota adları, belirli bir rotaya bağlı bir URL'yi oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-220">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="1bb6b-221">Rota adları, yönlendirme davranışını eşleşen URL üzerinde hiçbir etkisi ve yalnızca URL üretmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-221">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="1bb6b-222">Rota adları benzersiz uygulama kapsamında olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-222">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="1bb6b-223">Bunu geleneksel ile karşılaştırın *varsayılan yol*, tanımlayan `id` parametre isteğe bağlı olarak (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="1bb6b-223">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="1bb6b-224">Tam olarak API'leri belirtmek için bu özelliği izin verme gibi avantaj `/products` ve `/products/5` farklı eylemler için gönderilecek.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-224">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="1bb6b-225">Birleştirme yolları</span><span class="sxs-lookup"><span data-stu-id="1bb6b-225">Combining routes</span></span>

<span data-ttu-id="1bb6b-226">Öznitelik yönlendirme daha az yinelenen yapmak için rota denetleyici özniteliklerinden yola rotası özniteliklerinin ayrı Eylemler ile birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-226">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="1bb6b-227">Denetleyicide tanımlanan tüm rota şablonlarının eylemlere rota şablonlarının $a.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-227">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="1bb6b-228">Yol özniteliği denetleyiciye yerleştirmek yapar **tüm** denetleyici eylemlerini kullanmak özniteliği yönlendirme.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-228">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="1bb6b-229">Bu örnekte URL yolunu `/products` eşleşebilir `ProductsApi.ListProducts`ve URL yolunu `/products/5` eşleşebilir `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-229">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="1bb6b-230">Bu eylemlerin her ikisini de yalnızca HTTP eşleşen `GET` ile donatılmış çünkü `HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-230">Both of these actions only match HTTP `GET` because they're decorated with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="1bb6b-231">Rota şablonları ile başlayan bir eyleme uygulanan bir `/` rota şablonuyla denetleyiciye uygulanan birleştirilmiş yok.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-231">Route templates applied to an action that begin with a `/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="1bb6b-232">Bu örnek URL yollarını benzer birtakım eşleşen *varsayılan yol*.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-232">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="1bb6b-233">Öznitelik rotaları sıralama</span><span class="sxs-lookup"><span data-stu-id="1bb6b-233">Ordering attribute routes</span></span>

<span data-ttu-id="1bb6b-234">Tanımsız bir sırayla yürütmek geleneksel yollar aksine özniteliği yönlendirme bir ağaç oluşturur ve tüm yollar aynı anda eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-234">In contrast to conventional routes which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="1bb6b-235">Bu olarak davranır-rota girişleri bir ideal sıralamada; yerleştirilmişse en belirli yollar daha genel yolları önce yürütülecek şansına sahip olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-235">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="1bb6b-236">Örneğin, bir rota gibi `blog/search/{topic}` gibi bir yol daha fazla özel `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-236">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="1bb6b-237">Mantıksal olarak konuşarak `blog/search/{topic}` rota 'çalışır' ilk olarak, varsayılan olarak, yalnızca duyarlı sıralama olduğundan.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-237">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="1bb6b-238">Geleneksel yönlendirme kullanarak, geliştirici yollar sıralamada yerleştirmek için sorumludur.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-238">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="1bb6b-239">Öznitelik rotaları bir sipariş yapılandırabilir kullanarak `Order` tüm sağlanan çerçevesi rota özniteliklerini özelliği.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-239">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="1bb6b-240">Yollar, artan göre işlenir tür `Order` özelliği.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-240">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="1bb6b-241">Varsayılan sıra `0`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-241">The default order is `0`.</span></span> <span data-ttu-id="1bb6b-242">Bir rota kullanarak ayarlama `Order = -1` sipariş ayarlamazsanız yolları önce çalışır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-242">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="1bb6b-243">Bir rota kullanarak ayarlama `Order = 1` varsayılan rota sıralandıktan sonra çalışır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-243">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="1bb6b-244">Bağlı olarak kaçının `Order`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-244">Avoid depending on `Order`.</span></span> <span data-ttu-id="1bb6b-245">Ardından, URL alanınızın doğru yönlendirmek için açık sırası değerlerinin gerektiriyorsa, istemcilere de büyük olasılıkla kafa karıştırıcı.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-245">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="1bb6b-246">Genel özniteliği yönlendirme URL eşleşen doğru yolu seçer.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-246">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="1bb6b-247">URL üretmek için kullanılan varsayılan sırası çalışmıyorsa, bir geçersiz kılma uygulamaktan daha genellikle daha basit olduğu rota adı kullanılarak `Order` özelliği.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-247">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="1bb6b-248">Simge rota şablonlarındaki değiştirme ([denetleyicisi], [action] [alanı])</span><span class="sxs-lookup"><span data-stu-id="1bb6b-248">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="1bb6b-249">Öznitelik rotaları kolaylık sağlamak için destek *belirteci değiştirme* köşeli ayraçlar içinde bir belirteç kapsayan tarafından (`[`, `]`).</span><span class="sxs-lookup"><span data-stu-id="1bb6b-249">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="1bb6b-250">Belirteçleri `[action]`, `[area]`, ve `[controller]` eylem adı, alan adı ve Denetleyici adı rota tanımlandığı eylemden değerleri ile değiştirilecek.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-250">The tokens `[action]`, `[area]`, and `[controller]` will be replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="1bb6b-251">Bu örnekte açıklamaları açıklandığı gibi eylemleri URL yollarını eşleşebilir:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-251">In this example the actions can match URL paths as described in the comments:</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="1bb6b-252">Belirteç değiştirme öznitelik rotaları oluşturmanın son adım olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-252">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="1bb6b-253">Yukarıdaki örnekte, aşağıdaki kod ile aynı davranır:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-253">The above example will behave the same as the following code:</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="1bb6b-254">Öznitelik rotaları da devralma ile birleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-254">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="1bb6b-255">Bu belirteç değiştirme ile birleştirilmiş özellikle güçlüdür.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-255">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPost("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="1bb6b-256">Belirteç değiştirme öznitelik rotaları tarafından tanımlanan rota adları için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-256">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="1bb6b-257">`[Route("[controller]/[action]", Name="[controller]_[action]")]`Her eylem için bir benzersiz rota adı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-257">`[Route("[controller]/[action]", Name="[controller]_[action]")]` will generate a unique route name for each action.</span></span>

<span data-ttu-id="1bb6b-258">Değişmez değer belirteci değiştirme sınırlayıcı eşleşecek şekilde `[` veya `]`, karakteri tekrarlayarak kaçış (`[[` veya `]]`).</span><span class="sxs-lookup"><span data-stu-id="1bb6b-258">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="1bb6b-259">Birden çok yol</span><span class="sxs-lookup"><span data-stu-id="1bb6b-259">Multiple Routes</span></span>

<span data-ttu-id="1bb6b-260">Aynı eylemi ulaşmak birden çok yol tanımlama yönlendirme desteklediği öznitelik.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-260">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="1bb6b-261">Davranışını taklit etmek için bu en yaygın kullanımı olan *varsayılan geleneksel yol* aşağıdaki örnekte gösterildiği gibi:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-261">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="1bb6b-262">Birden çok yol özniteliğine denetleyicisinde her biri her eylem yöntemlerini rotası özniteliklerinin birleştirir toplamaktır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-262">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="1bb6b-263">Zaman birden çok yol özniteliğine (uygulayan `IActionConstraint`) bir eylem, her eylem kısıtlaması tanımlanmış özniteliğindeki rota şablonu birleştirir sonra yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-263">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="1bb6b-264">Birden çok yol eylemleri kullanarak güçlü görünebilir, ancak uygulamanızın URL alanı basit ve iyi tanımlanmış durumda tutmak daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-264">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="1bb6b-265">Birden çok yol yalnızca gerektiğinde, örneğin mevcut istemcileri desteklemek için eylemleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-265">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="1bb6b-266">Öznitelik yol isteğe bağlı parametreler, varsayılan değerleri ile kısıtlamaları belirtme</span><span class="sxs-lookup"><span data-stu-id="1bb6b-266">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="1bb6b-267">Öznitelik rotaları isteğe bağlı parametreler, varsayılan değerleri ile kısıtlamaları belirtmek için geleneksel yollar aynı satır içi söz dizimini destekler.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-267">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="1bb6b-268">Bkz: [rota şablon başvurusu](../../fundamentals/routing.md#route-template-reference) rota şablon söz dizimi ayrıntılı bir açıklaması.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-268">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="1bb6b-269">Özel rota öznitelikleri kullanma`IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="1bb6b-269">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="1bb6b-270">Tüm framework sağlanan rota özniteliklerini ( `[Route(...)]`, `[HttpGet(...)]` , vs.) uygulama `IRouteTemplateProvider` arabirimi.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-270">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="1bb6b-271">MVC arar denetleyicisi sınıfları ve eylem yöntemleri özniteliklerinde uygulamayı başlatır ve uygulama olanları kullandığında `IRouteTemplateProvider` rota ilk kümesini oluşturmak için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-271">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="1bb6b-272">Uygulayabileceğiniz `IRouteTemplateProvider` kendi rota öznitelikleri tanımlamak için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-272">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="1bb6b-273">Her `IRouteTemplateProvider` özel rota şablonuyla tek bir yol tanımlamanıza olanak verir sipariş ve adlandırın:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-273">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="1bb6b-274">Yukarıdaki örnekte özniteliğinden otomatik olarak ayarlar `Template` için `"api/[controller]"` zaman `[MyApiController]` uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-274">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="1bb6b-275">Öznitelik rotaları özelleştirmek için uygulama modelini kullanarak</span><span class="sxs-lookup"><span data-stu-id="1bb6b-275">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="1bb6b-276">*Uygulama modeli* Başlangıçta tüm yönlendirmek ve eylemleri yürütmek için MVC tarafından kullanılan meta verileri ile oluşturulan bir nesne model.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-276">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="1bb6b-277">*Uygulama modeli* rota özniteliklerini toplanan verilerin tümünü içerir (aracılığıyla `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="1bb6b-277">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="1bb6b-278">Yazabileceğiniz *kuralları* yönlendirme biçimini özelleştirmek için başlangıç sırasında uygulama modeli değiştirmek için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-278">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="1bb6b-279">Bu bölümde, uygulama modeli kullanarak yönlendirme özelleştirme basit bir örnek gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-279">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[Main](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="1bb6b-280">Yönlendirme karma: vs geleneksel yönlendirmesi özniteliği</span><span class="sxs-lookup"><span data-stu-id="1bb6b-280">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="1bb6b-281">MVC uygulamaları geleneksel Yönlendirme ve öznitelik yönlendirme kullanımını karıştırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-281">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="1bb6b-282">Tarayıcılar için HTML sayfalarını sunmadan denetleyicileri için geleneksel rotaları kullanır ve REST API'leri hizmet veren denetleyicileri için yönlendirme öznitelik için genel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-282">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="1bb6b-283">Öznitelik yönlendirilebilir veya Eylemler işleme, genel yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-283">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="1bb6b-284">Bir rota denetleyici veya eylem getirir, yönlendirilmiş özniteliği.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-284">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="1bb6b-285">Öznitelik rotaları tanımlayan eylemlerini geleneksel yolları ve tam tersini erişilemiyor.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-285">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="1bb6b-286">**Tüm** yol özniteliği denetleyicisinde yönlendirilen denetleyicisi özniteliğinde tüm eylemleri yapar.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-286">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="1bb6b-287">Ne yönlendirme sistemleri iki tür ayıran bir URL eşleşen bir rota şablonu sonra uygulanan işlemidir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-287">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="1bb6b-288">Geleneksel yönlendirme içinde eşleşme rota değerleri için eylem ve denetleyici tüm geleneksel yönlendirilmiş eylemleri bir arama tablosu seçmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-288">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="1bb6b-289">Öznitelik akışındaki her bir şablon zaten bir eylem ile ilişkili ve başka hiçbir arama gereklidir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-289">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="1bb6b-290">URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="1bb6b-290">URL Generation</span></span>

<span data-ttu-id="1bb6b-291">MVC uygulamaları 's yönlendirme URL nesil özellikleri Eylemler URL bağlantılarını oluşturmak için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-291">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="1bb6b-292">URL'ler oluşturulurken cmdlet'e kod URL'ler, kodunuzu daha sağlam ve korunabilir hale ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-292">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="1bb6b-293">Bu bölümde, MVC tarafından sağlanan URL nesil özelliklere odaklanır ve sadece URL nesil nasıl çalıştığını temel kavramları kapsar.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-293">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="1bb6b-294">Bkz: [yönlendirme](../../fundamentals/routing.md) URL nesil ayrıntılı bir açıklaması.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-294">See [Routing](../../fundamentals/routing.md) for a detailed description of URL generation.</span></span>

<span data-ttu-id="1bb6b-295">`IUrlHelper` Arabirimidir MVC arasındaki URL üretmek için yönlendirme altyapısını temel parçası.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-295">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="1bb6b-296">Bir örneğini bulabilirsiniz `IUrlHelper` aracılığıyla kullanılabilen `Url` denetleyicileri, görünümleri ve görünüm bileşenleri bir özellik.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-296">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="1bb6b-297">Bu örnekte, `IUrlHelper` arabirimi aracılığıyla kullanılır `Controller.Url` başka bir eylem URL'si oluşturmak için özellik.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-297">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="1bb6b-298">Uygulama geleneksel varsayılan kullanıyorsa, yol, değeri `url` değişkeni URL yolu dizesi olacaktır `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-298">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="1bb6b-299">Bu URL yolu geçerli istek (ortam değerleri), rota değerlerini birleştirerek yönlendirme tarafından geçirilen değerlerle oluşturulur `Url.Action` ve bu değerler rota şablonuna değiştirerek:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-299">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="1bb6b-300">Rota şablonu her yol parametresinde ve ortam değerleri ile eşleşen adları yerine kendi değere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-300">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="1bb6b-301">Varsa veya isteğe bağlı ise atlanması durumunda varsayılan bir değer bir değere sahip olmayan bir rota parametresini kullanabilirsiniz (olarak durumunda `id` Bu örnekte).</span><span class="sxs-lookup"><span data-stu-id="1bb6b-301">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="1bb6b-302">Tüm gerekli rota parametresini karşılık gelen bir değer yoksa, URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-302">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="1bb6b-303">Bir rota için URL oluşturma başarısız olursa, sonraki yol tüm yollar çalıştı veya herhangi bir eşleşme kadar denenir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-303">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="1bb6b-304">Örnek `Url.Action` kavramları farklı ancak geleneksel yönlendirme, ancak URL nesil works benzer şekilde yönlendirmesi özniteliği, yukarıdaki varsayar.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-304">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="1bb6b-305">Geleneksel yönlendirme ile rota değerleri bir şablon ve rota değerleri için genişletmek için kullanılan `controller` ve `action` genellikle görünür bu şablonda - yönlendirerek eşleşen URL'leri uygun olduğundan işlediğine bir *kuralı*.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-305">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="1bb6b-306">Öznitelik yönlendirmeye, rota değerleri için `controller` ve `action` görünmesi şablonda - yerine kullandıkları kullanılacak şablonunu aramak için izin verilmiyor.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-306">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="1bb6b-307">Bu örnekte, öznitelik yönlendirme kullanır:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-307">This example uses attribute routing:</span></span>

[!code-csharp[Main](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="1bb6b-308">MVC tüm yönlendirilen özniteliği eylemlerin bir arama tablosu oluşturur ve eşleşir `controller` ve `action` değerleri URL üretmek için kullanılacak rota şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-308">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="1bb6b-309">Yukarıdaki örnekteki `custom/url/to/destination` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-309">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="1bb6b-310">Eylem adına göre URL'ler oluşturulurken</span><span class="sxs-lookup"><span data-stu-id="1bb6b-310">Generating URLs by action name</span></span>

<span data-ttu-id="1bb6b-311">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="1bb6b-311">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="1bb6b-312">`Action`) ve ilişkili tüm tüm temel ne bir denetleyici adı ve eylem adı belirterek bağlantıyı yaptığınız belirtmek istediğiniz bu fikir aşırı.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-312">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="1bb6b-313">Kullanırken `Url.Action`, geçerli rota değerleri için `controller` ve `action` sizin için - değerini belirtilen `controller` ve `action` her ikisi de parçası olan *ortam değerleri* **ve** *değerleri*.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-313">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="1bb6b-314">Yöntem `Url.Action`, her zaman geçerli değerlerini kullanır `action` ve `controller` ve geçerli eylem yönlendiren bir URL yolu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-314">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="1bb6b-315">Yönlendirme bir URL oluşturulurken sağlamadı bilgilerinde doldurmak için ortam değerleri değerleri kullanmayı dener.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-315">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="1bb6b-316">Gibi bir yol kullanarak `{a}/{b}/{c}/{d}` ve ortam değerleri `{ a = Alice, b = Bob, c = Carol, d = David }`, Yönlendirme ek değer içermeyen bir URL'yi oluşturmak için yeterli bilgiye sahip - tüm rota beri parametreler bir değere sahip.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-316">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="1bb6b-317">Değer eklediyseniz `{ d = Donovan }`, değer `{ d = David }` yok sayılırdı, ve oluşturulan URL yolu olacağını `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-317">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="1bb6b-318">URL yollarını hiyerarşik.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-318">URL paths are hierarchical.</span></span> <span data-ttu-id="1bb6b-319">Eğer yukarıdaki örnekte değer katan `{ c = Cheryl }`, değerlerin her ikisi de `{ c = Carol, d = David }` göz ardı.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-319">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="1bb6b-320">İçin bir değer artık bu durumda sahibiz `d` ve URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-320">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="1bb6b-321">İstenen değeri belirtmek zorunda kalmaz `c` ve `d`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-321">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="1bb6b-322">Bu sorun varsayılan yol isabet bekleyebilirsiniz (`{controller}/{action}/{id?}`)- ancak uygulama olarak bu davranışı nadiren karşılaşır `Url.Action` her zaman açık olarak belirleyeceksiniz bir `controller` ve `action` değeri.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-322">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="1bb6b-323">Uzun aşırı `Url.Action` de ek bir ele *rota değerleri* dışında rota parametrelerinin değerlerini sağlamak için nesne `controller` ve `action`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-323">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="1bb6b-324">Bu ile kullanılan en yaygın olarak görürsünüz `id` gibi `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-324">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="1bb6b-325">Kural tarafından *rota değerleri* nesne, genellikle anonim türünde bir nesne ancak aynı zamanda olabilir bir `IDictionary<>` veya *düz eski .NET nesne*.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-325">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="1bb6b-326">Rota parametrelerine eşleşmeyen herhangi bir ek rota değeri sorgu dizesinde yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-326">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="1bb6b-327">Mutlak bir URL oluşturmak için kabul eden bir aşırı yüklemesini kullanın bir `protocol`:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="1bb6b-327">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="1bb6b-328">Rota tarafından URL'ler oluşturulurken</span><span class="sxs-lookup"><span data-stu-id="1bb6b-328">Generating URLs by route</span></span>

<span data-ttu-id="1bb6b-329">Yukarıdaki kod, denetleyici ve eylem adı geçirerek bir URL oluşturmanın gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-329">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="1bb6b-330">`IUrlHelper`Ayrıca sağlar `Url.RouteUrl` yöntemlerin ailesi.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-330">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="1bb6b-331">Bu yöntemlere benzer `Url.Action`, ancak geçerli değerlerini kopyalama `action` ve `controller` rota değerleri için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-331">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="1bb6b-332">Belirli bir yolu URL'yi genellikle oluşturmak için kullanılacak rota adı belirtmek için en yaygın kullanımdır *olmadan* bir denetleyici veya eylem adı belirterek.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-332">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="1bb6b-333">HTML dosyasındaki URL'ler oluşturulurken</span><span class="sxs-lookup"><span data-stu-id="1bb6b-333">Generating URLs in HTML</span></span>

<span data-ttu-id="1bb6b-334">`IHtmlHelper`sağlar `HtmlHelper` yöntemleri `Html.BeginForm` ve `Html.ActionLink` oluşturmak için `<form>` ve `<a>` öğeleri sırasıyla.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-334">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="1bb6b-335">Bu yöntemleri kullanmak `Url.Action` bir URL üretmek için yöntemi ve bunlar benzer bağımsız değişkenlerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-335">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="1bb6b-336">`Url.RouteUrl` İçin arkadaşlarımız `HtmlHelper` olan `Html.BeginRouteForm` ve `Html.RouteLink` benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-336">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="1bb6b-337">TagHelpers oluşturmak URL'ler aracılığıyla `form` TagHelper ve `<a>` TagHelper.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-337">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="1bb6b-338">Bunların her ikisi de kullanmak `IUrlHelper` kendi uygulama.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-338">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="1bb6b-339">Bkz: [formlarla çalışmak](../views/working-with-forms.md) daha fazla bilgi için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-339">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="1bb6b-340">Görünümler içinde `IUrlHelper` aracılığıyla kullanılabilir `Url` özelliği yukarıdaki tarafından kapsanmayan tüm geçici URL üretmek için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-340">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="1bb6b-341">Eylem sonuçlarında URL'ler oluşturulurken</span><span class="sxs-lookup"><span data-stu-id="1bb6b-341">Generating URLS in Action Results</span></span>

<span data-ttu-id="1bb6b-342">Yukarıdaki örneklerde kullanarak göstermiştir `IUrlHelper` bir denetleyicide en yaygın kullanımı bir eylem sonucu bir parçası olarak bir URL üretmek için olsa da bir denetleyicide.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-342">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="1bb6b-343">`ControllerBase` Ve `Controller` kullanışlı yöntemler başka bir eylem başvuru eylem sonuçlarına yönelik temel sınıflar sağlar.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-343">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="1bb6b-344">Bir genel kullanıcı girişi kabul ettikten sonra yeniden yönlendirmek için kullanımdır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-344">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public Task<IActionResult> Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
}
```

<span data-ttu-id="1bb6b-345">Eylem sonuçlarını Fabrika yöntemleri yöntemlere benzer bir desen izleyin `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-345">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="1bb6b-346">Ayrılmış geleneksel rotalar için özel durum</span><span class="sxs-lookup"><span data-stu-id="1bb6b-346">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="1bb6b-347">Geleneksel yönlendirme route tanımını adlı özel bir tür kullanabileceğiniz bir *ayrılmış geleneksel rota*.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-347">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="1bb6b-348">Aşağıdaki örnekte, yol adında `blog` adanmış bir geleneksel yoldur.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-348">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="1bb6b-349">Bu rota tanımları kullanarak `Url.Action("Index", "Home")` URL yolu oluşturur `/` ile `default` neden ancak rota?</span><span class="sxs-lookup"><span data-stu-id="1bb6b-349">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="1bb6b-350">Rota değerleri tahmin `{ controller = Home, action = Index }` bir URL'yi oluşturmak için yeterli kullanıyor `blog`, ve sonucu olacağını `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-350">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="1bb6b-351">Özel bir davranış rota engeller karşılık gelen bir yol parametresi yok varsayılan değerlerin Bel ayrılmış geleneksel yolları "çok hızlı" URL oluşturma ile.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-351">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="1bb6b-352">Bu durumda varsayılan değerler `{ controller = Blog, action = Article }`ve hiçbiri `controller` ya da `action` bir rota parametresi olarak görünür.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-352">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="1bb6b-353">Yönlendirme URL nesil gerçekleştirdiğinde, sağlanan değerler varsayılan değerler eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-353">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="1bb6b-354">Nesil URL'yi kullanarak `blog` başarısız olur değerleri `{ controller = Home, action = Index }` eşleşmeyen `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-354">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="1bb6b-355">Yönlendirme sonra kalan tekrar denemek için `default`, hangi başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-355">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="1bb6b-356">Alanları</span><span class="sxs-lookup"><span data-stu-id="1bb6b-356">Areas</span></span>

<span data-ttu-id="1bb6b-357">[Alanları](areas.md) ilgili işlevselliği, ayrı yönlendirme-için ad alanı (denetleyici eylemleri) ve klasör yapısı (için görünümler) olarak bir gruba düzenlemek için kullanılan bir MVC özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-357">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="1bb6b-358">Alanları kullanarak sağlar - aynı ada sahip birden çok denetleyicilerine sahip bir uygulama farklı olduğu sürece *alanları*.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-358">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="1bb6b-359">Alanları kullanarak başka bir rota parametresini ekleyerek yönlendirme amacıyla bir hiyerarşi oluşturur `area` için `controller` ve `action`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-359">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="1bb6b-360">Bu bölümde alanlarla - yönlendirme nasıl etkileşim ele alınacaktır bkz [alanları](areas.md) alanları görünümlerle nasıl kullanıldığı hakkında ayrıntılar için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-360">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="1bb6b-361">Aşağıdaki örnek MVC varsayılan geleneksel yol kullanacak şekilde yapılandırır ve bir *alanı rota* adlı bir alan için `Blog`:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-361">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="1bb6b-362">Bir URL yolu gibi eşleştirirken `/Manage/Users/AddUser`, ilk rota rota değerlerini üretecektir `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-362">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="1bb6b-363">`area` Rota değeri için varsayılan bir değer tarafından oluşturulur `area`, aslında tarafından oluşturulan rota `MapAreaRoute` şuna eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-363">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="1bb6b-364">`MapAreaRoute`Varsayılan değer hem için kısıtlamayı kullanan bir yol oluşturur `area` sağlanan alan adı, bu durumda kullanarak `Blog`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-364">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="1bb6b-365">Varsayılan değer rota her zaman üretir sağlar `{ area = Blog, ... }`, değer kısıtlaması gerektirir `{ area = Blog, ... }` URL üretmek için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-365">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="1bb6b-366">Geleneksel yönlendirme sipariş bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-366">Conventional routing is order-dependent.</span></span> <span data-ttu-id="1bb6b-367">Genel olarak, bir alan olmadan yolları daha ayrıntılı olarak alanları yollar önceki rota tablosunda yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-367">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="1bb6b-368">Yukarıdaki örneği kullanarak, rota değerlerini aşağıdaki eylemi eşleşir:</span><span class="sxs-lookup"><span data-stu-id="1bb6b-368">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="1bb6b-369">`AreaAttribute` Ne bir denetleyici gösterir olan bir alan bir parçası olarak, bu denetleyicisi olduğundan emin olan dediğimiz `Blog` alanı.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-369">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="1bb6b-370">Denetleyicileri olmadan bir `[Area]` özniteliği herhangi bir alan üyesi olmayan ve olacak **değil** ne zaman eşleşen `area` rota değeri yönlendirme tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-370">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="1bb6b-371">Aşağıdaki örnekte, rota değerleri yalnızca listelenen ilk denetleyicisi eşleştirebilirsiniz `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-371">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="1bb6b-372">Her denetleyici ad alanı bütünlük açısından buraya gösterilen - çakışan ve derleme hatasına neden adlandırma denetleyicileri Aksi durumda olurdu.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-372">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="1bb6b-373">Sınıf ad alanları MVC'ın yönlendirme üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-373">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="1bb6b-374">İlk iki denetleyicileri alanları üyeleri olan ve ilgili alan adlarının tarafından sağlandığında yalnızca eşleşen `area` rota değeri.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-374">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="1bb6b-375">Üçüncü denetleyicisi hiçbir alan ve can yalnızca eşleşme için hiçbir değer olduğunda üyesi olmayan `area` yönlendirme tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-375">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="1bb6b-376">Eşleşen bakımından *hiçbir değer*, yokluğu `area` değerdir aynı gibi değeri `area` null ya da boş dize.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-376">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="1bb6b-377">Bir alanı içinde bir eylemi yürütürken rota değeri için `area` olarak kullanılabilecek bir *ortam değeri* URL üretmek için kullanılacak yönlendirme.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-377">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="1bb6b-378">Varsayılan olarak alanları hareket yani *Yapışkan* aşağıdaki örneği tarafından gösterildiği gibi URL üretmek için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-378">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="1bb6b-379">Anlama IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="1bb6b-379">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="1bb6b-380">Bu bölüm, derin Dalış framework iç Ayrıntılar ve MVC yürütmek için bir eylem nasıl seçtiği değildir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-380">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="1bb6b-381">Özel bir genel uygulama gerekmez`IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="1bb6b-381">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="1bb6b-382">Büyük olasılıkla zaten kullanmış `IActionConstraint` arabirimiyle hakkında bilgi sahibi olmadığınız halde.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-382">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="1bb6b-383">`[HttpGet]` Özniteliği ve benzer `[Http-VERB]` öznitelikleri uygulamak `IActionConstraint` bir eylem yönteminin yürütülmesi sınırlamak için.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-383">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="1bb6b-384">Varsayılan geleneksel yol, URL yolunu varsayılarak `/Products/Edit` değerleri oluşturur `{ controller = Products, action = Edit }`, hangi eşleşir **her ikisi de** burada gösterilen eylem.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-384">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="1bb6b-385">İçinde `IActionConstraint` dediğimiz bu eylemlerin her ikisini de adayları - kabul edilen her ikisi de rota verilerini eşleşecek şekilde terminolojisi.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-385">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="1bb6b-386">Zaman `HttpGetAttribute` yürütür, sorar *Edit()* bir eşleşen *almak* ve başka bir HTTP fiili yönelik bir eşleşme değil.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-386">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="1bb6b-387">`Edit(...)` Eylem tanımlı kısıtlamalar yok ve bu nedenle herhangi bir HTTP fiili ile eşleşir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-387">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="1bb6b-388">Bu nedenle varsayarak bir `POST` - yalnızca `Edit(...)` eşleşir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-388">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="1bb6b-389">Ancak, bir `GET` hem Eylemler hala - ancak, bir eylem ile eşleşebilir bir `IActionConstraint` her zaman kabul *daha iyi* bir eylem daha.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-389">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="1bb6b-390">Bu nedenle çünkü `Edit()` sahip `[HttpGet]` daha belirgin olarak kabul edilir ve her iki Eylemler eşleşebilmesi durumunda seçilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-390">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="1bb6b-391">Kavramsal olarak, `IActionConstraint` biçimidir *aşırı yüklemesi*, ancak aşırı yükleme yöntemleri aynı ada sahip yerine onu aynı URL'ye eşleşen eylemler arasında aşırı yüklemesi.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-391">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="1bb6b-392">Öznitelik yönlendirme de kullanır `IActionConstraint` ve farklı denetleyicileri eylemlerden her iki kabul adayları neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-392">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="1bb6b-393">IActionConstraint uygulama</span><span class="sxs-lookup"><span data-stu-id="1bb6b-393">Implementing IActionConstraint</span></span>

<span data-ttu-id="1bb6b-394">En basit yolu uygulamak için bir `IActionConstraint` türetilmiş bir sınıf oluşturmak için `System.Attribute` ve eylemlerin ve denetleyicilerin üzerinde yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-394">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="1bb6b-395">MVC otomatik olarak bulmak herhangi `IActionConstraint` öznitelikleri olarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-395">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="1bb6b-396">Uygulama modeli kısıtlamaları uygulamak için kullanabileceğiniz ve nasıl uygulanacağını metaprogram izin verdiği kadar bu büyük olasılıkla en esnek bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-396">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="1bb6b-397">Aşağıdaki örnekte bir kısıtlama dayalı bir eylem seçtiği bir *ülke kodu* rota verileri.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-397">In the following example a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="1bb6b-398">[Github'da tam örnek](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="1bb6b-398">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="1bb6b-399">Uygulamak için sorumlu `Accept` yöntemi ve 'yürütmek için sipariş' kısıtlaması için seçme.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-399">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="1bb6b-400">Bu durumda, `Accept` yöntemi döndürür `true` eylemi bir eşleşme olduğunu belirtmek için zaman `country` değeri eşleşmeleri rota.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-400">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="1bb6b-401">Bu farklıdır bir `RouteValueAttribute` öznitelikli olmayan bir eylem için geri dönüş olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-401">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="1bb6b-402">Tanımlarsanız, gösteren örnek bir `en-US` eylemi ardından gibi bir ülke kodu `fr-FR` yok daha genel bir denetleyiciye döner `[CountrySpecific(...)]` uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-402">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="1bb6b-403">`Order` Özelliği, karar *aşama* kısıtlaması bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-403">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="1bb6b-404">Eylem kısıtlamaları çalıştırmak göre gruplarındaki `Order`.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-404">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="1bb6b-405">Örneğin, tüm framework'ün HTTP yöntem öznitelikleri kullanmak aynı sağlanan `Order` aynı aşamasında çalışabilmesi değeri.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-405">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="1bb6b-406">İstenen ilkelerinizi uygulamak gerektiği kadar aşama olabilir.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-406">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="1bb6b-407">İçin bir değer karar vermek için `Order` olsun veya olmasın, kısıtlama HTTP yöntemleri önce uygulanması gereken düşünün.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-407">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="1bb6b-408">Düşük numaraların ilk çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bb6b-408">Lower numbers run first.</span></span>
