---
title: ASP.NET Core .NET metotlarından JavaScript işlevlerini çağırın Blazor
author: guardrex
description: Uygulamalarda .NET metotlarından JavaScript işlevlerini çağırmayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: a62462e3a0a2366a8662573ada5d2e7589c14c0d
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722481"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="7cda7-103">ASP.NET Core .NET metotlarından JavaScript işlevlerini çağırın Blazor</span><span class="sxs-lookup"><span data-stu-id="7cda7-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="7cda7-104">Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7cda7-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7cda7-105">Bir Blazor uygulama, JavaScript işlevlerinden .net yöntemleri ve .net yöntemlerinden JavaScript işlevlerini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="7cda7-106">Bu senaryolar *JavaScript birlikte çalışabilirliği* (*js birlikte çalışma*) olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="7cda7-107">Bu makalede, .NET 'ten JavaScript işlevlerini çağırma ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="7cda7-108">JavaScript 'ten .NET yöntemlerini çağırma hakkında daha fazla bilgi için bkz <xref:blazor/call-dotnet-from-javascript> ..</span><span class="sxs-lookup"><span data-stu-id="7cda7-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="7cda7-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7cda7-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7cda7-110">.NET 'ten JavaScript 'e çağrı yapmak için <xref:Microsoft.JSInterop.IJSRuntime> soyutlamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="7cda7-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="7cda7-111">JS birlikte çalışma çağrıları vermek için, bu <xref:Microsoft.JSInterop.IJSRuntime> soyutlamayı bileşeninizdeki ekler.</span><span class="sxs-lookup"><span data-stu-id="7cda7-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="7cda7-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> herhangi bir sayıda JSON seri hale getirilebilir bağımsız değişkenle birlikte çağırmak istediğiniz JavaScript işlevi için bir tanımlayıcı alır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="7cda7-113">İşlev tanımlayıcısı genel kapsama ( `window` ) göredir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="7cda7-114">Çağırmak isterseniz `window.someScope.someFunction` , tanımlayıcı olur `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="7cda7-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="7cda7-115">Çağrılmadan önce işlevi kaydetmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7cda7-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="7cda7-116">Dönüş türünün `T` de JSON seri hale getirilebilir olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="7cda7-117">`T` döndürülen JSON türüyle en iyi eşleşen .NET türüyle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="7cda7-118">Blazor ServerPrerendering etkin olan uygulamalar için, ilk prerendering sırasında JavaScript 'e çağrı yapılamaz.</span><span class="sxs-lookup"><span data-stu-id="7cda7-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="7cda7-119">JavaScript birlikte çalışma çağrılarının, tarayıcıyla bağlantı kurulana kadar ertelenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="7cda7-120">Daha fazla bilgi için bkz. [bir uygulamanın ne zaman Blazor Server prerendering](#detect-when-a-blazor-server-app-is-prerendering) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="7cda7-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="7cda7-121">Aşağıdaki örnek [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) , JavaScript tabanlı bir kod çözücüsünü temel alır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-121">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="7cda7-122">Örnek, geliştirici koddan mevcut bir JavaScript API 'sine bir gereksinimi boşaltan bir C# yönteminden JavaScript işlevinin nasıl çağrılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-122">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="7cda7-123">JavaScript işlevi bir C# yönteminden bir bayt dizisi kabul eder, dizinin kodunu çözer ve görüntülenecek metni bileşene döndürür.</span><span class="sxs-lookup"><span data-stu-id="7cda7-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="7cda7-124">`<head>` `wwwroot/index.html` ( Blazor WebAssembly ) Veya () öğesinin içinde `Pages/_Host.cshtml` Blazor Server , geçirilen bir dizinin kodunu çözmek için kullanılan bir JavaScript işlevi sağlayın `TextDecoder` ve kodu çözülen değeri döndürün:</span><span class="sxs-lookup"><span data-stu-id="7cda7-124">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="7cda7-125">Önceki örnekte gösterilen kod gibi JavaScript kodu, `.js` betik dosyasına yönelik bir başvuruya sahip bir JavaScript dosyasından () de yüklenebilir:</span><span class="sxs-lookup"><span data-stu-id="7cda7-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="7cda7-126">Aşağıdaki bileşen:</span><span class="sxs-lookup"><span data-stu-id="7cda7-126">The following component:</span></span>

* <span data-ttu-id="7cda7-127">`convertArray` `JSRuntime` Bir bileşen düğmesi () seçildiğinde JavaScript işlevini çağırır **`Convert Array`** .</span><span class="sxs-lookup"><span data-stu-id="7cda7-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="7cda7-128">JavaScript işlevi çağrıldıktan sonra, geçirilen dizi bir dizeye dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="7cda7-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="7cda7-129">Dize, görüntüleme için bileşene döndürülür.</span><span class="sxs-lookup"><span data-stu-id="7cda7-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="7cda7-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="7cda7-130">IJSRuntime</span></span>

<span data-ttu-id="7cda7-131"><xref:Microsoft.JSInterop.IJSRuntime>Soyutlamayı kullanmak için aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="7cda7-131">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="7cda7-132"><xref:Microsoft.JSInterop.IJSRuntime>Soyutlamayı Razor bileşene ekleyin ( `.razor` ):</span><span class="sxs-lookup"><span data-stu-id="7cda7-132">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="7cda7-133">`<head>` `wwwroot/index.html` ( Blazor WebAssembly ) Veya () öğesinin içinde `Pages/_Host.cshtml` Blazor Server bir `handleTickerChanged` JavaScript işlevi sağlar.</span><span class="sxs-lookup"><span data-stu-id="7cda7-133">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="7cda7-134">İşlevi ile çağrılır <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> ve bir değer döndürmez:</span><span class="sxs-lookup"><span data-stu-id="7cda7-134">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="7cda7-135"><xref:Microsoft.JSInterop.IJSRuntime>Soyutlamayı bir sınıfa () Ekle `.cs` :</span><span class="sxs-lookup"><span data-stu-id="7cda7-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="7cda7-136">`<head>` `wwwroot/index.html` ( Blazor WebAssembly ) Veya () öğesinin içinde `Pages/_Host.cshtml` Blazor Server bir `handleTickerChanged` JavaScript işlevi sağlar.</span><span class="sxs-lookup"><span data-stu-id="7cda7-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="7cda7-137">İşlevi ile çağrılır `JSRuntime.InvokeAsync` ve bir değer döndürür:</span><span class="sxs-lookup"><span data-stu-id="7cda7-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="7cda7-138">[Buildrendertree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)ile dinamik içerik oluşturma için `[Inject]` özniteliğini kullanın:</span><span class="sxs-lookup"><span data-stu-id="7cda7-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="7cda7-139">Bu konuya eşlik eden istemci tarafı örnek uygulamada, Kullanıcı girişi almak ve bir hoş geldiniz iletisi göstermek üzere DOM ile etkileşime geçen uygulama için iki JavaScript işlevi mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="7cda7-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="7cda7-140">`showPrompt`: Kullanıcı girişini kabul etmek için bir istem üretir (kullanıcının adı) ve arayan adına adı döndürür.</span><span class="sxs-lookup"><span data-stu-id="7cda7-140">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="7cda7-141">`displayWelcome`: Çağıran bir DOM nesnesine çağırandan bir hoş geldiniz iletisi atar `id` `welcome` .</span><span class="sxs-lookup"><span data-stu-id="7cda7-141">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="7cda7-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="7cda7-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="7cda7-143">`<script>` `wwwroot/index.html` Dosyadaki ( Blazor WebAssembly ) veya `Pages/_Host.cshtml` dosyadaki () JavaScript dosyasına başvuran etiketi yerleştirin Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="7cda7-143">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="7cda7-144">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="7cda7-144">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="7cda7-145">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="7cda7-145">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="7cda7-146">`<script>` `<script>` Etiket dinamik olarak güncelleştirilemediğinden bir bileşen dosyasına etiket yerleştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="7cda7-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="7cda7-147">.NET yöntemleri, çağırarak dosyadaki JavaScript işlevleriyle birlikte çalışır `exampleJsInterop.js` <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="7cda7-147">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="7cda7-148"><xref:Microsoft.JSInterop.IJSRuntime>Soyutlama, senaryolara izin vermek için zaman uyumsuzdur Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="7cda7-148">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="7cda7-149">Uygulama bir Blazor WebAssembly uygulama ise ve bir JavaScript işlevini zaman uyumlu olarak çağırmak istiyorsanız, bunun yerine alt türe çevirme yapın <xref:Microsoft.JSInterop.IJSInProcessRuntime> ve çağırın <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> .</span><span class="sxs-lookup"><span data-stu-id="7cda7-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="7cda7-150">Çoğu JS birlikte çalışma kitaplıklarının, kitaplıkların tüm senaryolarda kullanılabilir olmasını sağlamak için zaman uyumsuz API 'Leri kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="7cda7-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="7cda7-151">Standart [JavaScript modüllerinde](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)JavaScript yalıtımını etkinleştirmek için [ Blazor JavaScript yalıtım ve nesne başvuruları](#blazor-javascript-isolation-and-object-references) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="7cda7-151">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="7cda7-152">Örnek uygulama, JS birlikte çalışabilirliği göstermek için bir bileşeni içerir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="7cda7-153">Bileşen:</span><span class="sxs-lookup"><span data-stu-id="7cda7-153">The component:</span></span>

* <span data-ttu-id="7cda7-154">Bir JavaScript istemi aracılığıyla Kullanıcı girişini alır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="7cda7-155">İşlemek için bileşene metni döndürür.</span><span class="sxs-lookup"><span data-stu-id="7cda7-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="7cda7-156">Bir hoş geldiniz iletisini göstermek için DOM ile etkileşime sahip ikinci bir JavaScript işlevini çağırır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="7cda7-157">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="7cda7-157">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="7cda7-158">Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="7cda7-158">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="7cda7-159">`TriggerJsPrompt`Bileşen düğme seçilerek yürütüldüğünde **`Trigger JavaScript Prompt`** , `showPrompt` dosyada verilen JavaScript işlevi `wwwroot/exampleJsInterop.js` çağırılır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-159">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="7cda7-160">`showPrompt`İşlevi, HTML kodlu ve bileşene döndürülen kullanıcı girişini (kullanıcının adı) kabul eder.</span><span class="sxs-lookup"><span data-stu-id="7cda7-160">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="7cda7-161">Bileşen, kullanıcının adını yerel bir değişkende depolar `name` .</span><span class="sxs-lookup"><span data-stu-id="7cda7-161">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="7cda7-162">İçinde depolanan dize, `name` `displayWelcome` hoş geldiniz iletisini bir başlık etiketine Işleyen bir JavaScript işlevine iletilen bir hoş geldiniz iletisine eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-162">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="7cda7-163">Void JavaScript işlevini çağırın</span><span class="sxs-lookup"><span data-stu-id="7cda7-163">Call a void JavaScript function</span></span>

<span data-ttu-id="7cda7-164">[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) veya [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) döndüren JavaScript işlevleri ile çağırılır <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="7cda7-164">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="7cda7-165">Bir uygulamanın ne zaman Blazor Server prerendering olduğunu Algıla</span><span class="sxs-lookup"><span data-stu-id="7cda7-165">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="7cda7-166">Öğelere başvuruları yakala</span><span class="sxs-lookup"><span data-stu-id="7cda7-166">Capture references to elements</span></span>

<span data-ttu-id="7cda7-167">Bazı JS birlikte çalışma senaryoları HTML öğelerine başvurular gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-167">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="7cda7-168">Örneğin, bir kullanıcı arabirimi kitaplığı başlatma için bir öğe başvurusu gerektirebilir veya ya da gibi bir öğe üzerinde komut benzeri API 'Ler çağırmanız gerekebilir `focus` `play` .</span><span class="sxs-lookup"><span data-stu-id="7cda7-168">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="7cda7-169">Aşağıdaki yaklaşımı kullanarak bir bileşen içindeki HTML öğelerine başvuruları yakalayın:</span><span class="sxs-lookup"><span data-stu-id="7cda7-169">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="7cda7-170">`@ref`HTML öğesine bir öznitelik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7cda7-170">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="7cda7-171"><xref:Microsoft.AspNetCore.Components.ElementReference>Adı özniteliğin değeriyle eşleşen bir tür alanı tanımlayın `@ref` .</span><span class="sxs-lookup"><span data-stu-id="7cda7-171">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="7cda7-172">Aşağıdaki örnek, öğesine bir başvuru yakalama göstermektedir `username` `<input>` :</span><span class="sxs-lookup"><span data-stu-id="7cda7-172">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="7cda7-173">Yalnızca ile etkileşimde bulunmayan boş bir öğenin içeriğini bulunmamalıdır için bir öğe başvurusu kullanın Blazor .</span><span class="sxs-lookup"><span data-stu-id="7cda7-173">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="7cda7-174">Bu senaryo, üçüncü taraf bir API 'nin öğeye içerik sağladığı durumlarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-174">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="7cda7-175">BlazorÖğesiyle etkileşmediği için, Blazor öğe ve Dom gösterimi arasında bir çakışma olabilir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-175">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="7cda7-176">Aşağıdaki örnekte, *dangerous* `ul` Blazor Bu öğenin liste öğelerini () doldurmak üzere Dom ile etkileşimde bulunduğundan, sıralanmamış listenin () içeriğini zaman içinde () () zaman zaman aşmaktır `<li>` .</span><span class="sxs-lookup"><span data-stu-id="7cda7-176">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="7cda7-177">JS birlikte çalışma öğesi içeriğini değiştiriyorsa `MyList` ve Blazor öğe için SLA 'lar uygulamaya çalışırsa, DIFFLER Dom ile eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="7cda7-177">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="7cda7-178">.NET kodu açısından düşünüldüğünde, <xref:Microsoft.AspNetCore.Components.ElementReference> donuk bir tanıtıcıdır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-178">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="7cda7-179">İle yapabileceğiniz *tek* şey, <xref:Microsoft.AspNetCore.Components.ElementReference> js birlikte çalışma yoluyla JavaScript koduna geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="7cda7-179">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="7cda7-180">Bunu yaptığınızda, JavaScript tarafı kodu `HTMLElement` normal Dom API 'leri ile kullanılabilecek bir örnek alır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-180">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="7cda7-181">Örneğin, aşağıdaki kod bir öğe üzerinde odağı ayarlamaya izin veren bir .NET genişletme yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="7cda7-181">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="7cda7-182">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="7cda7-182">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="7cda7-183">Değer döndürmeyen bir JavaScript işlevini çağırmak için kullanın <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="7cda7-183">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="7cda7-184">Aşağıdaki kod, yakalanmış olan önceki JavaScript işlevini çağırarak Kullanıcı adı girişi üzerinde odağı ayarlar <xref:Microsoft.AspNetCore.Components.ElementReference> :</span><span class="sxs-lookup"><span data-stu-id="7cda7-184">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="7cda7-185">Bir genişletme yöntemi kullanmak için, örneği alan bir statik genişletme yöntemi oluşturun <xref:Microsoft.JSInterop.IJSRuntime> :</span><span class="sxs-lookup"><span data-stu-id="7cda7-185">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="7cda7-186">`Focus`Yöntemi doğrudan nesnesi üzerinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-186">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="7cda7-187">Aşağıdaki örnek, `Focus` yöntemin ad alanından kullanılabilir olduğunu varsayar `JsInteropClasses` :</span><span class="sxs-lookup"><span data-stu-id="7cda7-187">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="7cda7-188">`username`Değişken yalnızca bileşen işlendikten sonra doldurulur.</span><span class="sxs-lookup"><span data-stu-id="7cda7-188">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="7cda7-189">Doldurulmamış bir <xref:Microsoft.AspNetCore.Components.ElementReference> JavaScript koduna geçirilirse JavaScript kodu bir değeri alır `null` .</span><span class="sxs-lookup"><span data-stu-id="7cda7-189">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="7cda7-190">Bileşen işlemeyi tamamladıktan sonra öğe başvurularını değiştirmek için (bir öğe üzerinde ilk odağı ayarlamak için) [ `OnAfterRenderAsync` veya `OnAfterRender` bileşen yaşam döngüsü yöntemlerini](xref:blazor/components/lifecycle#after-component-render)kullanın.</span><span class="sxs-lookup"><span data-stu-id="7cda7-190">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="7cda7-191">Genel türlerle çalışırken ve bir değer döndürürken şunu kullanın <xref:System.Threading.Tasks.ValueTask%601> :</span><span class="sxs-lookup"><span data-stu-id="7cda7-191">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="7cda7-192">`GenericMethod` , bir türü olan doğrudan nesnesi üzerinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-192">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="7cda7-193">Aşağıdaki örnek, `GenericMethod` ad alanından kullanılabilir olduğunu varsayar `JsInteropClasses` :</span><span class="sxs-lookup"><span data-stu-id="7cda7-193">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="7cda7-194">Bileşenler arasında başvuru öğeleri</span><span class="sxs-lookup"><span data-stu-id="7cda7-194">Reference elements across components</span></span>

<span data-ttu-id="7cda7-195"><xref:Microsoft.AspNetCore.Components.ElementReference>Yalnızca bir bileşen <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> yönteminde (ve bir öğe başvurusu bir) garanti edilir `struct` , bu nedenle bileşenler arasında bir öğe başvurusu geçirilememelidir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-195">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="7cda7-196">Bir üst bileşen için bir öğe başvurusunu diğer bileşenlere kullanılabilir hale getirmek için, üst bileşen şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="7cda7-196">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="7cda7-197">Alt bileşenlerin geri çağırmaları kaydetmesine izin ver.</span><span class="sxs-lookup"><span data-stu-id="7cda7-197">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="7cda7-198">Geçirilen öğe başvurusuyla olay sırasında kayıtlı geri çağırmaları çağırın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> .</span><span class="sxs-lookup"><span data-stu-id="7cda7-198">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="7cda7-199">Bu yaklaşım dolaylı olarak, alt bileşenlerin üst öğenin öğe başvurusuyla etkileşime geçmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="7cda7-199">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="7cda7-200">Aşağıdaki Blazor WebAssembly örnekte yaklaşım gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-200">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="7cda7-201">İçinde `<head>` `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="7cda7-201">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="7cda7-202">İçinde `<body>` `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="7cda7-202">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="7cda7-203">`Pages/Index.razor` (üst bileşen):</span><span class="sxs-lookup"><span data-stu-id="7cda7-203">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="7cda7-204">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="7cda7-204">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="7cda7-205">Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="7cda7-205">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="7cda7-206">`Shared/SurveyPrompt.razor` (alt bileşen):</span><span class="sxs-lookup"><span data-stu-id="7cda7-206">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="7cda7-207">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="7cda7-207">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="7cda7-208">Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="7cda7-208">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="7cda7-209">Harden JS birlikte çalışma çağrıları</span><span class="sxs-lookup"><span data-stu-id="7cda7-209">Harden JS interop calls</span></span>

<span data-ttu-id="7cda7-210">JS birlikte çalışması, ağ hataları nedeniyle başarısız olabilir ve güvenilmez olarak değerlendirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-210">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="7cda7-211">Varsayılan olarak, bir Blazor Server uygulama bir dakika sonra sunucu üzerinde bir kez js birlikte çalışabilirlik çağrısı çağırır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-211">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="7cda7-212">Bir uygulama daha agresif zaman aşımına uğrayedebiliyorsanız, aşağıdaki yaklaşımlardan birini kullanarak zaman aşımını ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="7cda7-212">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="7cda7-213">İçinde genel olarak `Startup.ConfigureServices` , zaman aşımını belirtin:</span><span class="sxs-lookup"><span data-stu-id="7cda7-213">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="7cda7-214">Bileşen kodunda çağrı başına, tek bir çağrı zaman aşımını belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="7cda7-214">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="7cda7-215">Kaynak tükenmesi hakkında daha fazla bilgi için bkz <xref:blazor/security/server/threat-mitigation> ..</span><span class="sxs-lookup"><span data-stu-id="7cda7-215">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="7cda7-216">Döngüsel nesne başvurularından kaçının</span><span class="sxs-lookup"><span data-stu-id="7cda7-216">Avoid circular object references</span></span>

<span data-ttu-id="7cda7-217">Döngüsel başvurular içeren nesneler, her biri için istemcide serileştirilemiyor:</span><span class="sxs-lookup"><span data-stu-id="7cda7-217">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="7cda7-218">.NET yöntemi çağrıları.</span><span class="sxs-lookup"><span data-stu-id="7cda7-218">.NET method calls.</span></span>
* <span data-ttu-id="7cda7-219">Dönüş türünün döngüsel başvuruları olduğunda C# ' den JavaScript Yöntem çağrıları.</span><span class="sxs-lookup"><span data-stu-id="7cda7-219">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="7cda7-220">Daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="7cda7-220">For more information, see the following issues:</span></span>

* [<span data-ttu-id="7cda7-221">Döngüsel başvurular desteklenmez, iki alma (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="7cda7-221">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="7cda7-222">Teklif: serileştirilirken döngüsel başvuruları işlemek için mekanizma ekleyin (DotNet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="7cda7-222">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="7cda7-223">Blazor JavaScript yalıtımı ve nesne başvuruları</span><span class="sxs-lookup"><span data-stu-id="7cda7-223">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="7cda7-224">Blazor Standart [JavaScript modüllerinde](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)JavaScript yalıtımına izin vermez.</span><span class="sxs-lookup"><span data-stu-id="7cda7-224">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="7cda7-225">JavaScript yalıtımı aşağıdaki avantajları sağlar:</span><span class="sxs-lookup"><span data-stu-id="7cda7-225">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="7cda7-226">İçeri aktarılan JavaScript artık genel ad alanını pollutes.</span><span class="sxs-lookup"><span data-stu-id="7cda7-226">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="7cda7-227">İlişkili JavaScript 'in içe aktarılması için bir kitaplık ve bileşenlerin tüketicileri gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="7cda7-227">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="7cda7-228">Örneğin, aşağıdaki JavaScript modülü bir tarayıcı istemi göstermek için bir JavaScript işlevi dışarı aktarır:</span><span class="sxs-lookup"><span data-stu-id="7cda7-228">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="7cda7-229">Önceki JavaScript modülünü bir .NET kitaplığına statik web varlığı () olarak ekleyin `wwwroot/exampleJsInterop.js` ve ardından hizmeti kullanarak modülü .net koduna aktarın <xref:Microsoft.JSInterop.IJSRuntime> .</span><span class="sxs-lookup"><span data-stu-id="7cda7-229">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="7cda7-230">Hizmet, `jsRuntime` Aşağıdaki örnek için (gösterilmez) olarak eklenir:</span><span class="sxs-lookup"><span data-stu-id="7cda7-230">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<JSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="7cda7-231">`import`Önceki örnekteki tanımlayıcı, özellikle bir JavaScript modülünü içeri aktarmak için kullanılan özel bir tanıtıcıdır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-231">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="7cda7-232">Sabit statik Web varlık yolunu kullanarak modülü belirtin: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` .</span><span class="sxs-lookup"><span data-stu-id="7cda7-232">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="7cda7-233">Yer tutucu, `{LIBRARY NAME}` kitaplık adıdır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-233">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="7cda7-234">Yer tutucu, `{PATH UNDER WWWROOT}` altında betiğin yoludur `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="7cda7-234">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="7cda7-235"><xref:Microsoft.JSInterop.IJSRuntime> modülünü `JSObjectReference` .NET kodundan bir JavaScript nesnesine başvuruyu temsil eden bir olarak içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="7cda7-235"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `JSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="7cda7-236">Modülünden, `JSObjectReference` içe aktarılmış JavaScript işlevlerini çağırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="7cda7-236">Use the `JSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7cda7-237">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7cda7-237">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="7cda7-238">InteropComponent. Razor örneği (DotNet/AspNetCore GitHub deposu, 3,1 yayın dalı)</span><span class="sxs-lookup"><span data-stu-id="7cda7-238">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="7cda7-239">Uygulamalarda büyük veri aktarımları gerçekleştirme Blazor Server</span><span class="sxs-lookup"><span data-stu-id="7cda7-239">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
