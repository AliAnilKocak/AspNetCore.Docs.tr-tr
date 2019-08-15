---
title: ASP.NET Core Blazor yönlendirme
author: guardrex
description: Uygulamalardaki istekleri yönlendirme ve gezinti bağlantısı bileşeni hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/routing
ms.openlocfilehash: 197b1a91b3540d21639c3ee775b2c490da7b23fe
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030391"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="ace3f-103">ASP.NET Core Blazor yönlendirme</span><span class="sxs-lookup"><span data-stu-id="ace3f-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="ace3f-104">Tarafından [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ace3f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ace3f-105">İsteklerin nasıl yönlendirileceğini ve Blazor uygulamalarında gezinti bağlantıları oluşturmak için `NavLink` bileşenin nasıl kullanılacağını öğrenin.</span><span class="sxs-lookup"><span data-stu-id="ace3f-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="ace3f-106">Uç nokta yönlendirme tümleştirmesi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ace3f-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="ace3f-107">Blazor sunucu tarafı [ASP.NET Core uç nokta yönlendirme](xref:fundamentals/routing)ile tümleşiktir.</span><span class="sxs-lookup"><span data-stu-id="ace3f-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="ace3f-108">ASP.NET Core bir uygulama, `MapBlazorHub` içindeki `Startup.Configure`etkileşimli bileşenler için gelen bağlantıları kabul edecek şekilde yapılandırılmıştır:</span><span class="sxs-lookup"><span data-stu-id="ace3f-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="ace3f-109">Rota şablonları</span><span class="sxs-lookup"><span data-stu-id="ace3f-109">Route templates</span></span>

<span data-ttu-id="ace3f-110">`Router` Bileşen yönlendirmeyi sağlar ve erişilebilir her bileşene bir yol şablonu sağlanır.</span><span class="sxs-lookup"><span data-stu-id="ace3f-110">The `Router` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="ace3f-111">Bileşen App. Razor dosyasında görünür: `Router`</span><span class="sxs-lookup"><span data-stu-id="ace3f-111">The `Router` component appears in the *App.razor* file:</span></span>

<span data-ttu-id="ace3f-112">Blazor sunucu tarafı bir uygulamada:</span><span class="sxs-lookup"><span data-stu-id="ace3f-112">In a Blazor server-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly" />
```

<span data-ttu-id="ace3f-113">Blazor istemci tarafı uygulamasında:</span><span class="sxs-lookup"><span data-stu-id="ace3f-113">In a Blazor client-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="ace3f-114">Bir`@page` yönergeyle bir *. Razor* dosyası derlendiğinde, oluşturulan sınıf, yol şablonunu belirten bir <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="ace3f-114">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="ace3f-115">Çalışma zamanında, yönlendirici bileşen sınıflarını bir `RouteAttribute` ile arar ve bileşeni istenen URL ile eşleşen bir rota şablonuyla işler.</span><span class="sxs-lookup"><span data-stu-id="ace3f-115">At runtime, the router looks for component classes with a `RouteAttribute` and renders the component with a route template that matches the requested URL.</span></span>

<span data-ttu-id="ace3f-116">Birden çok yol şablonu, bir bileşene uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="ace3f-116">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="ace3f-117">Aşağıdaki bileşen ve `/BlazorRoute` `/DifferentBlazorRoute`için isteklere yanıt verir:</span><span class="sxs-lookup"><span data-stu-id="ace3f-117">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="ace3f-118">Yolları doğru bir şekilde oluşturmak için `<base>` , uygulama, `href` özniteliğinde belirtilen uygulama temel yolu ile *Wwwroot/index.html* File (Blazor Client-Side) veya *Pages/_host. cshtml* dosyasında (Blazor sunucu-tarafı) bir etiket içermelidir ( `<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="ace3f-118">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="ace3f-119">Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/client-side#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="ace3f-119">For more information, see <xref:host-and-deploy/blazor/client-side#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="ace3f-120">İçerik bulunamadığında özel içerik sağla</span><span class="sxs-lookup"><span data-stu-id="ace3f-120">Provide custom content when content isn't found</span></span>

<span data-ttu-id="ace3f-121">`Router` Bileşen, istenen rota için içerik bulunmazsa uygulamanın özel içerik belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ace3f-121">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="ace3f-122">*App. Razor* dosyasında, `<NotFoundContent>` `Router` bileşenin öğesinde özel içerik ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="ace3f-122">In the *App.razor* file, set custom content in the `<NotFoundContent>` element of the `Router` component:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <NotFoundContent>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFoundContent>
</Router>
```

<span data-ttu-id="ace3f-123">İçeriği `<NotFoundContent>` , diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="ace3f-123">The content of `<NotFoundContent>` can include arbitrary items, such as other interactive components.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="ace3f-124">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="ace3f-124">Route parameters</span></span>

<span data-ttu-id="ace3f-125">Yönlendirici, karşılık gelen bileşen parametrelerini aynı ada (büyük/küçük harfe duyarsız) doldurmak için yol parametrelerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="ace3f-125">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="ace3f-126">ASP.NET Core 3,0 önizlemesinde Blazor uygulamaları için isteğe bağlı parametreler desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="ace3f-126">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="ace3f-127">Önceki `@page` örnekte iki yönergeler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ace3f-127">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="ace3f-128">İlki, bir parametre olmadan bileşene gezinmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="ace3f-128">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="ace3f-129">İkinci `@page` yönerge, `{text}` yol parametresini alır ve değeri `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="ace3f-129">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="ace3f-130">Yol kısıtlamaları</span><span class="sxs-lookup"><span data-stu-id="ace3f-130">Route constraints</span></span>

<span data-ttu-id="ace3f-131">Yol kısıtlaması bir yönlendirme segmentinde bir bileşene tür eşleştirmeyi zorlar.</span><span class="sxs-lookup"><span data-stu-id="ace3f-131">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="ace3f-132">Aşağıdaki örnekte, `Users` bileşen yolu yalnızca şu durumlarda eşleşir:</span><span class="sxs-lookup"><span data-stu-id="ace3f-132">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="ace3f-133">İstek `Id` URL 'sinde bir yol kesimi var.</span><span class="sxs-lookup"><span data-stu-id="ace3f-133">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="ace3f-134">Segment bir tamsayıdır (`int`). `Id`</span><span class="sxs-lookup"><span data-stu-id="ace3f-134">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="ace3f-135">Aşağıdaki tabloda gösterilen yol kısıtlamaları mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="ace3f-135">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="ace3f-136">Sabit kültür ile eşleşen yol kısıtlamaları için daha fazla bilgi için tablonun altındaki uyarıya bakın.</span><span class="sxs-lookup"><span data-stu-id="ace3f-136">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="ace3f-137">Kısıtlaması</span><span class="sxs-lookup"><span data-stu-id="ace3f-137">Constraint</span></span> | <span data-ttu-id="ace3f-138">Örnek</span><span class="sxs-lookup"><span data-stu-id="ace3f-138">Example</span></span>           | <span data-ttu-id="ace3f-139">Örnek eşleşmeler</span><span class="sxs-lookup"><span data-stu-id="ace3f-139">Example Matches</span></span>                                                                  | <span data-ttu-id="ace3f-140">Bilmesi</span><span class="sxs-lookup"><span data-stu-id="ace3f-140">Invariant</span></span><br><span data-ttu-id="ace3f-141">kültür</span><span class="sxs-lookup"><span data-stu-id="ace3f-141">culture</span></span><br><span data-ttu-id="ace3f-142">eşleştirme</span><span class="sxs-lookup"><span data-stu-id="ace3f-142">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="ace3f-143">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="ace3f-143">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="ace3f-144">Hayır</span><span class="sxs-lookup"><span data-stu-id="ace3f-144">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="ace3f-145">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="ace3f-145">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="ace3f-146">Evet</span><span class="sxs-lookup"><span data-stu-id="ace3f-146">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="ace3f-147">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="ace3f-147">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="ace3f-148">Evet</span><span class="sxs-lookup"><span data-stu-id="ace3f-148">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="ace3f-149">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="ace3f-149">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="ace3f-150">Evet</span><span class="sxs-lookup"><span data-stu-id="ace3f-150">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="ace3f-151">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="ace3f-151">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="ace3f-152">Evet</span><span class="sxs-lookup"><span data-stu-id="ace3f-152">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="ace3f-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="ace3f-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="ace3f-154">Hayır</span><span class="sxs-lookup"><span data-stu-id="ace3f-154">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="ace3f-155">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="ace3f-155">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="ace3f-156">Evet</span><span class="sxs-lookup"><span data-stu-id="ace3f-156">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="ace3f-157">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="ace3f-157">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="ace3f-158">Evet</span><span class="sxs-lookup"><span data-stu-id="ace3f-158">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="ace3f-159">URL 'yi doğrulayan ve bir clr türüne ( `int` veya `DateTime`gibi) dönüştürülen yol kısıtlamaları, her zaman sabit kültürü kullanır.</span><span class="sxs-lookup"><span data-stu-id="ace3f-159">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="ace3f-160">Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="ace3f-160">These constraints assume that the URL is non-localizable.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="ace3f-161">Gezinti bağlantısı bileşeni</span><span class="sxs-lookup"><span data-stu-id="ace3f-161">NavLink component</span></span>

<span data-ttu-id="ace3f-162">Gezinti bağlantıları `NavLink` oluştururken, HTML köprü öğelerinin (`<a>`) yerine bir bileşen kullanın.</span><span class="sxs-lookup"><span data-stu-id="ace3f-162">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="ace3f-163">Bir `NavLink` bileşen `<a>` ,geçerli`href` URL ile eşleşip eşleşmediğini temel alarak bir CSSsınıfınageçişyaptığısürecebiröğesigibidavranır.`active`</span><span class="sxs-lookup"><span data-stu-id="ace3f-163">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="ace3f-164">`active` Sınıfı, bir kullanıcının hangi sayfanın etkin sayfa olduğunu anladığı gezinti bağlantıları arasında yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="ace3f-164">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="ace3f-165">Aşağıdaki `NavMenu` bileşen, bileşenlerin nasıl [](https://getbootstrap.com/docs/) kullanılacağını `NavLink` gösteren bir önyükleme gezinti çubuğu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="ace3f-165">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="ace3f-166">Öğesinin özniteliğine atayabilmeniz için kullanabileceğiniz iki `NavLinkMatch` `Match`seçenekvardır `<NavLink>` :</span><span class="sxs-lookup"><span data-stu-id="ace3f-166">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="ace3f-167">`NavLinkMatch.All`Tüm geçerli URL ile eşleştiğinde etkin olur.`NavLink` &ndash;</span><span class="sxs-lookup"><span data-stu-id="ace3f-167">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="ace3f-168">`NavLinkMatch.Prefix`(*varsayılan*) Geçerli URL 'nin herhangi bir önekiyle eşleştiğinde etkin olur.`NavLink` &ndash;</span><span class="sxs-lookup"><span data-stu-id="ace3f-168">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="ace3f-169">`NavLink` Yukarıdaki örnekte, ana `href=""` giriş `active` URL 'siyle eşleşir ve yalnızca uygulamanın varsayılan temel yol URL 'sindeki CSS sınıfını alır (örneğin, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="ace3f-169">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="ace3f-170">`NavLink` İkincisi, Kullanıcı ön `active` eki olan herhangi bir `MyComponent` URL 'yi ziyaret ettiğinde sınıfı alır (örneğin, `https://localhost:5001/MyComponent` ve `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="ace3f-170">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="ace3f-171">Ek `NavLink` bileşen öznitelikleri, işlenen tutturucu etiketine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="ace3f-171">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="ace3f-172">Aşağıdaki örnekte, `NavLink` bileşen `target` özniteliğini içerir:</span><span class="sxs-lookup"><span data-stu-id="ace3f-172">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="ace3f-173">Aşağıdaki HTML biçimlendirmesi işlenir:</span><span class="sxs-lookup"><span data-stu-id="ace3f-173">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="ace3f-174">URI ve gezinti durumu yardımcıları</span><span class="sxs-lookup"><span data-stu-id="ace3f-174">URI and navigation state helpers</span></span>

<span data-ttu-id="ace3f-175">Kod `Microsoft.AspNetCore.Components.IUriHelper` içinde C# URI ve gezinme ile çalışmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ace3f-175">Use `Microsoft.AspNetCore.Components.IUriHelper` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="ace3f-176">`IUriHelper`Aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="ace3f-176">`IUriHelper` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="ace3f-177">Üye</span><span class="sxs-lookup"><span data-stu-id="ace3f-177">Member</span></span> | <span data-ttu-id="ace3f-178">Açıklama</span><span class="sxs-lookup"><span data-stu-id="ace3f-178">Description</span></span> |
| ------ | ----------- |
| `GetAbsoluteUri` | <span data-ttu-id="ace3f-179">Geçerli mutlak URI 'yi alır.</span><span class="sxs-lookup"><span data-stu-id="ace3f-179">Gets the current absolute URI.</span></span> |
| `GetBaseUri` | <span data-ttu-id="ace3f-180">Mutlak bir URI oluşturmak için göreli URI yollarına eklenebilir olan temel URI 'yi (sondaki eğik çizgiyle birlikte) alır.</span><span class="sxs-lookup"><span data-stu-id="ace3f-180">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="ace3f-181">Genellikle, `GetBaseUri` *Wwwroot/index.html* (Blazor `href` Client-Side) veya `<base>` *Pages/_host. cshtml* (Blazor sunucu-tarafı) içindeki belgenin öğesinde bulunan özniteliğe karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="ace3f-181">Typically, `GetBaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="ace3f-182">Belirtilen URI 'ye gider.</span><span class="sxs-lookup"><span data-stu-id="ace3f-182">Navigates to the specified URI.</span></span> <span data-ttu-id="ace3f-183">`forceLoad` Şu`true`ise:</span><span class="sxs-lookup"><span data-stu-id="ace3f-183">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="ace3f-184">İstemci tarafı yönlendirme atlanır.</span><span class="sxs-lookup"><span data-stu-id="ace3f-184">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="ace3f-185">Bu tarayıcı, URI 'nin normalde istemci tarafı yönlendirici tarafından işlenip işlenmediğini sunucudan yeni sayfayı yüklemeye zorlanır.</span><span class="sxs-lookup"><span data-stu-id="ace3f-185">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `OnLocationChanged` | <span data-ttu-id="ace3f-186">Gezinti konumu değiştiğinde harekete gelen bir olay.</span><span class="sxs-lookup"><span data-stu-id="ace3f-186">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="ace3f-187">Göreli bir URI 'yi mutlak bir URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ace3f-187">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="ace3f-188">Temel URI (örneğin, daha önce tarafından `GetBaseUri`döndürülen bir URI) verildiğinde, mutlak bir URI 'yi taban URI önekine göre bir URI 'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ace3f-188">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="ace3f-189">Aşağıdaki bileşen, düğme seçildiğinde uygulamanın `Counter` bileşenine gider:</span><span class="sxs-lookup"><span data-stu-id="ace3f-189">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```cshtml
@page "/navigate"
@using Microsoft.AspNetCore.Components
@inject IUriHelper UriHelper

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        UriHelper.NavigateTo("counter");
    }
}
```
