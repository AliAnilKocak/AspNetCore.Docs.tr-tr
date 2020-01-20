---
title: ASP.NET Core Blazor uygulamalarda hataları işleme
author: guardrex
description: Blazor işlenmemiş özel durumları nasıl yönettiğini ve hataları algılayan ve işleyen uygulamalar geliştirme Blazor nasıl ASP.NET Core olduğunu öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: fe4cc13b1efb8c70c9632f032626aa938fb65ea3
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159956"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="6d5b0-103">ASP.NET Core Blazor uygulamalarda hataları işleme</span><span class="sxs-lookup"><span data-stu-id="6d5b0-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="6d5b0-104">[Steve Sanderson](https://github.com/SteveSandersonMS) tarafından</span><span class="sxs-lookup"><span data-stu-id="6d5b0-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="6d5b0-105">Bu makalede, işlenmemiş özel durumları Blazor nasıl yönettiği ve hataları algılayan ve işleyen uygulamalar geliştirileceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="6d5b0-106">Geliştirme sırasında ayrıntılı hatalar</span><span class="sxs-lookup"><span data-stu-id="6d5b0-106">Detailed errors during development</span></span>

<span data-ttu-id="6d5b0-107">Geliştirme sırasında Blazor bir uygulama düzgün bir şekilde çalışmadığı zaman, uygulamanın ayrıntılı hata bilgilerinin alınması sorunu gidermeye ve sorunun giderilmesine yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="6d5b0-108">Bir hata oluştuğunda, Blazor uygulamalar ekranın alt kısmında altın bir çubuk görüntüler:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="6d5b0-109">Geliştirme sırasında altın çubuk, özel durumu görebileceğiniz tarayıcı konsoluna yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="6d5b0-110">Üretimde, altın çubuk kullanıcıya bir hata oluştuğunu bildirir ve tarayıcıyı yenilemeyi önerir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="6d5b0-111">Bu hata işleme deneyimi için Kullanıcı arabirimi Blazor projesi şablonlarının bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-111">The UI for this error handling experience is part of the Blazor project templates.</span></span> <span data-ttu-id="6d5b0-112">Blazor WebAssembly uygulamasında, *Wwwroot/index.html* dosyasındaki deneyimi özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="6d5b0-113">Blazor sunucu uygulamasında, *Pages/_Host. cshtml* dosyasındaki deneyimi özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="6d5b0-114">`blazor-error-ui` öğesi Blazor şablonlarına eklenen stillerle gizlenir ve bir hata oluştuğunda gösterilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-114">The `blazor-error-ui` element is hidden by the styles included with the Blazor templates and then shown when an error occurs.</span></span>

## <a name="how-the-opno-locblazor-framework-reacts-to-unhandled-exceptions"></a><span data-ttu-id="6d5b0-115">Blazor Framework işlenmemiş özel durumlara nasıl yeniden davranır</span><span class="sxs-lookup"><span data-stu-id="6d5b0-115">How the Blazor framework reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="6d5b0-116"> sunucusu durum bilgisi olan bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-116"> Server is a stateful framework.</span></span> <span data-ttu-id="6d5b0-117">Kullanıcılar bir uygulamayla etkileşim kurarken, *devre*olarak bilinen sunucuya bir bağlantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="6d5b0-118">Devre, etkin bileşen örneklerini ve diğer birçok durum düzeyini barındırır; örneğin:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="6d5b0-119">Bileşenlerin en son işlenmiş çıktısı.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="6d5b0-120">İstemci tarafı olayları tarafından tetiklenebilecek geçerli olay işleme temsilcileri kümesi.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="6d5b0-121">Bir Kullanıcı uygulamayı birden çok tarayıcı sekmelerinde açarsa, birden çok bağımsız devreler vardır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="6d5b0-122">, işlenmemiş özel durumların çoğunu gerçekleştiği devreyi önemli olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="6d5b0-123">İşlenmeyen bir özel durum nedeniyle devre sonlandırılırsa, Kullanıcı yalnızca yeni bir bağlantı oluşturmak için sayfayı yeniden yükleyerek uygulamayla etkileşime geçerek devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="6d5b0-124">Diğer kullanıcılar veya diğer tarayıcı sekmeleri için devre dışı bırakılmış olan Sonlandırıcı dışındaki devreleri etkilenmez.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="6d5b0-125">Bu senaryo, kilitlenen uygulamanın yeniden başlatılması gereken&mdash;, ancak diğer uygulamaların etkilenmemesi gerektiğini engelleyen bir masaüstü uygulamasına benzerdir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="6d5b0-126">Aşağıdaki nedenlerden dolayı işlenmeyen bir özel durum oluştuğunda devre sonlandırılır:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="6d5b0-127">İşlenmeyen bir özel durum, genellikle devre dışı bir durumda bırakır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="6d5b0-128">İşlenmemiş bir özel durumdan sonra uygulamanın normal işlemi garanti edilemez.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="6d5b0-129">Devre devam ederse uygulamada güvenlik açıkları görünebilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="6d5b0-130">Geliştirici kodundaki işlenmemiş özel durumları yönetme</span><span class="sxs-lookup"><span data-stu-id="6d5b0-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="6d5b0-131">Bir uygulamanın bir hatadan sonra devam edebilmesi için, uygulamanın hata işleme mantığı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="6d5b0-132">Bu makalenin sonraki bölümlerinde işlenmemiş özel durumların olası kaynakları açıklanır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="6d5b0-133">Üretimde, çerçeve özel durum iletilerini veya yığın izlemelerini Kullanıcı arabiriminde işlemez.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="6d5b0-134">Özel durum iletilerini veya yığın izlemelerini işleme şu şekilde olabilir:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="6d5b0-135">Hassas bilgileri son kullanıcılara açıklayın.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="6d5b0-136">Kötü niyetli bir kullanıcının uygulama, sunucu veya ağ güvenliğini tehlikeye atabilecek bir uygulamada zayıf yanları bulmasına yardımcı olun.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="6d5b0-137">Kalıcı bir sağlayıcıyla hataları günlüğe kaydet</span><span class="sxs-lookup"><span data-stu-id="6d5b0-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="6d5b0-138">İşlenmeyen bir özel durum oluşursa, özel durum hizmet kapsayıcısında yapılandırılmış <xref:Microsoft.Extensions.Logging.ILogger> örneklerine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="6d5b0-139">Varsayılan olarak, Blazor uygulamalar konsol günlüğü sağlayıcısı ile konsol çıktısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="6d5b0-140">Günlük boyutunu ve günlük döndürmesini yöneten bir sağlayıcı ile daha kalıcı bir konuma oturum açmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="6d5b0-141">Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="6d5b0-142">Geliştirme sırasında, Blazor hata ayıklamaya yardımcı olması için genellikle özel durumların tüm ayrıntılarını tarayıcı konsoluna gönderir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="6d5b0-143">Üretimde, tarayıcı konsolundaki ayrıntılı hatalar varsayılan olarak devre dışıdır. Bu, hataların istemcilere gönderilmediği, ancak özel durumun tam ayrıntılarının hala sunucu tarafında günlüğe kaydedildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="6d5b0-144">Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="6d5b0-145">Hangi olayların günlüğe kaydedileceğini ve günlüğe kaydedilen olayların önem düzeyi düzeyini karar vermelisiniz.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="6d5b0-146">Saldırgan kullanıcılar hataları kasıtlı olarak tetikleyebiliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="6d5b0-147">Örneğin, ürün ayrıntılarını görüntüleyen bir bileşenin URL 'sinde bilinmeyen bir `ProductId` sağlandığı bir hatadan olay günlüğe kaydetme.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="6d5b0-148">Tüm hatalar günlüğe kaydetme için yüksek önem derecesine sahip olaylar olarak değerlendirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="6d5b0-149">Hataların gerçekleşebileceği yerleri</span><span class="sxs-lookup"><span data-stu-id="6d5b0-149">Places where errors may occur</span></span>

<span data-ttu-id="6d5b0-150">Çerçeve ve uygulama kodu aşağıdaki konumlardan herhangi birinde işlenmeyen özel durumları tetikleyebiliriz:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-150">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="6d5b0-151">Bileşen örneği oluşturma</span><span class="sxs-lookup"><span data-stu-id="6d5b0-151">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="6d5b0-152">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="6d5b0-152">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="6d5b0-153">İşleme mantığı</span><span class="sxs-lookup"><span data-stu-id="6d5b0-153">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="6d5b0-154">Olay işleyicileri</span><span class="sxs-lookup"><span data-stu-id="6d5b0-154">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="6d5b0-155">Bileşen elden çıkarma</span><span class="sxs-lookup"><span data-stu-id="6d5b0-155">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="6d5b0-156">JavaScript birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="6d5b0-156">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="6d5b0-157">Devre işleyicileri</span><span class="sxs-lookup"><span data-stu-id="6d5b0-157">Circuit handlers</span></span>](#circuit-handlers)
* [<span data-ttu-id="6d5b0-158">Devre elden çıkarma</span><span class="sxs-lookup"><span data-stu-id="6d5b0-158">Circuit disposal</span></span>](#circuit-disposal)
* [<span data-ttu-id="6d5b0-159">Prerendering</span><span class="sxs-lookup"><span data-stu-id="6d5b0-159">Prerendering</span></span>](#prerendering)

<span data-ttu-id="6d5b0-160">Önceki işlenmemiş özel durumlar, bu makalenin aşağıdaki bölümlerinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="6d5b0-161">Bileşen örneği oluşturma</span><span class="sxs-lookup"><span data-stu-id="6d5b0-161">Component instantiation</span></span>

<span data-ttu-id="6d5b0-162">Blazor bir bileşenin örneğini oluşturduğunda:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-162">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="6d5b0-163">Bileşenin Oluşturucusu çağrılır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="6d5b0-164">[`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) yönergesi veya [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) özniteliği aracılığıyla bileşen oluşturucusuna sağlanan Singleton olmayan her türlü hizmeti Oluşturucu.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span> 

<span data-ttu-id="6d5b0-165">Herhangi bir `[Inject]` özelliği için yürütülen bir Oluşturucu veya ayarlayıcı işlenmeyen bir özel durum oluşturduğunda devre dışı olur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-165">A circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="6d5b0-166">Framework bileşeni örneklemediğinden özel durum önemlidir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="6d5b0-167">Oluşturucu mantığı özel durumlar oluşturmayabilir, uygulama hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesini kullanarak özel durumları yakalemelidir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="6d5b0-168">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="6d5b0-168">Lifecycle methods</span></span>

<span data-ttu-id="6d5b0-169">Bir bileşenin kullanım ömrü boyunca Blazor aşağıdaki [yaşam döngüsü yöntemlerini](xref:blazor/lifecycle)çağırır:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-169">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="6d5b0-170">Herhangi bir yaşam döngüsü yöntemi, zaman uyumlu veya zaman uyumsuz olarak bir özel durum oluşturursa, özel durum devre dışı olur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to the circuit.</span></span> <span data-ttu-id="6d5b0-171">Bileşenler için yaşam döngüsü yöntemlerinde hatalarla başa çıkmak için hata işleme mantığı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="6d5b0-172">Aşağıdaki örnekte `OnParametersSetAsync` bir ürünü almak için bir yöntemi çağırır:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-172">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="6d5b0-173">`ProductRepository.GetProductByIdAsync` yönteminde oluşan bir özel durum `try-catch` ifadesiyle işlenir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="6d5b0-174">`catch` bloğu yürütüldüğünde:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="6d5b0-175">`loadFailed`, kullanıcıya bir hata iletisi göstermek için kullanılan `true`olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="6d5b0-176">Hata günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="6d5b0-177">İşleme mantığı</span><span class="sxs-lookup"><span data-stu-id="6d5b0-177">Rendering logic</span></span>

<span data-ttu-id="6d5b0-178">`.razor` bileşen dosyasındaki bildirim temelli biçimlendirme `BuildRenderTree`adlı bir C# yönteme derlenir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-178">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="6d5b0-179">Bir bileşen işlendiğinde `BuildRenderTree` yürütülür ve işlenmiş bileşenin öğelerini, metnini ve alt bileşenlerini açıklayan bir veri yapısı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-179">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="6d5b0-180">İşleme mantığı bir özel durum oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="6d5b0-181">`@someObject.PropertyName` değerlendiriliyorsa ancak `@someObject` `null`bu senaryoya bir örnek oluşur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="6d5b0-182">İşleme mantığı tarafından oluşturulan işlenmeyen bir özel durum, devre için önemli bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-182">An unhandled exception thrown by rendering logic is fatal to the circuit.</span></span>

<span data-ttu-id="6d5b0-183">Oluşturma mantığındaki null başvuru özel durumunu engellemek için, üyelerine erişmeden önce bir `null` nesnesini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="6d5b0-184">Aşağıdaki örnekte, `person.Address` `null``person.Address` özelliklere erişilmez:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="6d5b0-185">Yukarıdaki kod, `person` `null`olmadığını varsayar.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="6d5b0-186">Genellikle, kodun yapısı, bileşenin işlendiği sırada bir nesnenin var olmasını garanti eder.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="6d5b0-187">Bu durumlarda, işleme mantığındaki `null` denetlemek gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="6d5b0-188">Önceki örnekte, bileşen örneği oluşturulduğunda `person` oluşturulduğu için `person` olması garanti edilebilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="6d5b0-189">Olay işleyicileri</span><span class="sxs-lookup"><span data-stu-id="6d5b0-189">Event handlers</span></span>

<span data-ttu-id="6d5b0-190">İstemci tarafı kod, kullanarak olay işleyicileri C# oluşturulduğunda kodun çağırmaları tetikler:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="6d5b0-191">Diğer `@on...` öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="6d5b0-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="6d5b0-192">Olay işleyici kodu, bu senaryolarda işlenmeyen bir özel durum oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="6d5b0-193">Bir olay işleyicisi işlenmeyen bir özel durum oluşturursa (örneğin, bir veritabanı sorgusu başarısız olursa), özel durum devre dışı olarak önemli olur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to the circuit.</span></span> <span data-ttu-id="6d5b0-194">Uygulama, dış nedenlerle başarısız olabilecek kodu çağırırsa, hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesini kullanarak özel durumlar yakalayın.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-194">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="6d5b0-195">Kullanıcı kodu yakalanmazsa ve özel durumu işlemezse çerçeve özel durumu günlüğe kaydeder ve devre sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="6d5b0-196">Bileşen elden çıkarma</span><span class="sxs-lookup"><span data-stu-id="6d5b0-196">Component disposal</span></span>

<span data-ttu-id="6d5b0-197">Örneğin, Kullanıcı başka bir sayfaya gezindiği için, bir bileşen kullanıcı arabiriminden kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="6d5b0-198"><xref:System.IDisposable?displayProperty=fullName> uygulayan bir bileşen kullanıcı arabiriminden kaldırıldığında, çerçeve bileşenin <xref:System.IDisposable.Dispose*> yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span> 

<span data-ttu-id="6d5b0-199">Bileşenin `Dispose` yöntemi işlenmeyen bir özel durum oluşturursa, bu özel durum devre dışı olarak önemli olur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="6d5b0-200">Çıkarma mantığı özel durumlar oluşturmayabilir, uygulama hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesini kullanarak özel durumları yakalemelidir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="6d5b0-201">Bileşen aktiften çıkarma hakkında daha fazla bilgi için bkz. <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-201">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="6d5b0-202">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="6d5b0-202">JavaScript interop</span></span>

<span data-ttu-id="6d5b0-203">`IJSRuntime.InvokeAsync<T>`, .NET kodunun Kullanıcı tarayıcısındaki JavaScript çalışma zamanına zaman uyumsuz çağrılar yapmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-203">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="6d5b0-204">`InvokeAsync<T>`ile ilgili hata işleme için aşağıdaki koşullar geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-204">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="6d5b0-205">`InvokeAsync<T>` çağrısı eşzamanlı olarak başarısız olursa, .NET özel durumu oluşur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-205">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="6d5b0-206">`InvokeAsync<T>` çağrısı başarısız olabilir, örneğin, sağlanan bağımsız değişkenler serileştirilemiyor.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-206">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="6d5b0-207">Geliştirici kodu özel durumu yakalamalı.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-207">Developer code must catch the exception.</span></span> <span data-ttu-id="6d5b0-208">Bir olay işleyicisindeki veya bileşen yaşam döngüsü yöntemindeki uygulama kodu bir özel durumu işlemezse, ortaya çıkan özel durum devre dışı olur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to the circuit.</span></span>
* <span data-ttu-id="6d5b0-209">`InvokeAsync<T>` çağrısı zaman uyumsuz olarak başarısız olursa, .NET <xref:System.Threading.Tasks.Task> başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-209">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="6d5b0-210">Örneğin, JavaScript tarafı kodu bir özel durum oluşturduğundan veya `rejected`olarak tamamlanan bir `Promise` döndürdüğünden `InvokeAsync<T>` çağrısı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-210">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="6d5b0-211">Geliştirici kodu özel durumu yakalamalı.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-211">Developer code must catch the exception.</span></span> <span data-ttu-id="6d5b0-212">[Await](/dotnet/csharp/language-reference/keywords/await) işleci kullanılıyorsa, yöntem çağrısını hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesinde sarmalamalı olarak düşünün.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-212">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="6d5b0-213">Aksi takdirde, başarısız kod, devre için önemli olan işlenmemiş bir özel durumla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-213">Otherwise, the failing code results in an unhandled exception that's fatal to the circuit.</span></span>
* <span data-ttu-id="6d5b0-214">Varsayılan olarak, `InvokeAsync<T>` çağrıları belirli bir süre içinde tamamlanmalıdır veya çağrı zaman aşımına uğrar. Varsayılan zaman aşımı süresi bir dakikadır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-214">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="6d5b0-215">Zaman aşımı, kodu ağ bağlantısında veya hiçbir zaman bir tamamlanma iletisi göndermeme JavaScript kodundaki bir kaybına karşı korur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="6d5b0-216">Çağrı zaman aşımına uğrarsa, elde edilen `Task` bir <xref:System.OperationCanceledException>başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-216">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="6d5b0-217">Günlüğe kaydetme ile özel durumu yakalar ve işleyin.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="6d5b0-218">Benzer şekilde, JavaScript kodu [`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions) özniteliği tarafından belirtilen .net yöntemlerine çağrı başlatabilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions) attribute.</span></span> <span data-ttu-id="6d5b0-219">Bu .NET yöntemleri işlenmeyen bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="6d5b0-220">Özel durum devre için önemli olarak değerlendirilmez.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-220">The exception isn't treated as fatal to the circuit.</span></span>
* <span data-ttu-id="6d5b0-221">JavaScript tarafı `Promise` reddedilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="6d5b0-222">.NET tarafında ya da yöntem çağrısının JavaScript tarafında hata işleme kodu kullanma seçeneğiniz vardır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="6d5b0-223">Daha fazla bilgi için bkz. <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-223">For more information, see <xref:blazor/javascript-interop>.</span></span>

### <a name="circuit-handlers"></a><span data-ttu-id="6d5b0-224">Devre işleyicileri</span><span class="sxs-lookup"><span data-stu-id="6d5b0-224">Circuit handlers</span></span>

Blazor<span data-ttu-id="6d5b0-225">, bir kullanıcının devresi durumu değiştiğinde bildirimleri alan bir *devre işleyicisi*tanımlamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-225"> allows code to define a *circuit handler*, which receives notifications when the state of a user's circuit changes.</span></span> <span data-ttu-id="6d5b0-226">Aşağıdaki durumlar kullanılır:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-226">The following states are used:</span></span>

* `initialized`
* `connected`
* `disconnected`
* `disposed`

<span data-ttu-id="6d5b0-227">Bildirimler, `CircuitHandler` soyut temel sınıfından devralan bir DI hizmeti kaydedilerek yönetilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-227">Notifications are managed by registering a DI service that inherits from the `CircuitHandler` abstract base class.</span></span>

<span data-ttu-id="6d5b0-228">Özel bir devre işleyicisindeki Yöntemler işlenmeyen bir özel durum oluşturuyorsam, özel durum devre için önemli olur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-228">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="6d5b0-229">Bir işleyicinin kodundaki veya yöntemleri çağrılan özel durumlara tolerans sağlamak için kodu bir veya daha fazla [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyiminde hata işleme ve günlüğe kaydetme ile sarın.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-229">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

### <a name="circuit-disposal"></a><span data-ttu-id="6d5b0-230">Devre elden çıkarma</span><span class="sxs-lookup"><span data-stu-id="6d5b0-230">Circuit disposal</span></span>

<span data-ttu-id="6d5b0-231">Bir kullanıcının bağlantısı kesilmediği ve Framework devre durumunu temizlemede bir devre dışı bırakıldığında, çerçeve devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-231">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="6d5b0-232">Kapsamı elden atılırken, <xref:System.IDisposable?displayProperty=fullName>uygulayan hiçbir devre kapsamlı DI hizmeti yok.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-232">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="6d5b0-233">Herhangi bir DI hizmeti, elden çıkarma sırasında işlenmeyen bir özel durum oluşturursa, çerçeve özel durumu günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-233">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

### <a name="prerendering"></a><span data-ttu-id="6d5b0-234">Prerendering</span><span class="sxs-lookup"><span data-stu-id="6d5b0-234">Prerendering</span></span>

Blazor<span data-ttu-id="6d5b0-235"> bileşenleri, `Component` etiketi Yardımcısı kullanılarak, işlenmiş HTML biçimlendirmesinin kullanıcının ilk HTTP isteğinin bir parçası olarak döndürülmesi için önceden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-235"> components can be prerendered using the `Component` Tag Helper so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="6d5b0-236">Bu şu şekilde geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-236">This works by:</span></span>

* <span data-ttu-id="6d5b0-237">Aynı sayfanın parçası olan tüm ön işlenmiş bileşenler için yeni bir devre oluşturma.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-237">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="6d5b0-238">İlk HTML oluşturuluyor.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-238">Generating the initial HTML.</span></span>
* <span data-ttu-id="6d5b0-239">Kullanıcı tarayıcısının aynı sunucuya geri SignalR bir bağlantı kuruncaya kadar devreyi `disconnected` olarak kabul edin.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-239">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="6d5b0-240">Bağlantı oluşturulduğunda, devre üzerindeki etkileşim sürdürülür ve bileşenlerin HTML işaretlemesi güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-240">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="6d5b0-241">Herhangi bir bileşen prerendering sırasında, örneğin bir yaşam döngüsü yöntemi veya işleme mantığı sırasında işlenmeyen bir özel durum oluşturursa:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-241">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="6d5b0-242">Bu, devre için önemli bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-242">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="6d5b0-243">Özel durum, `Component` Tag Yardımcısı 'ndan çağrı yığınını ortaya atılır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-243">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="6d5b0-244">Bu nedenle, özel durum geliştirici kodu tarafından açıkça yakalanmadığı takdirde tüm HTTP isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-244">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="6d5b0-245">Normal koşullarda, prerendering başarısız olduğunda bileşeni oluşturma ve işleme devam etmek, çalışan bir bileşen işlenemediği için mantıklı değildir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-245">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="6d5b0-246">Prerendering sırasında oluşabilecek hatalara tolerans sağlamak için hata işleme mantığı özel durum oluşturabilecek bir bileşenin içine yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-246">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="6d5b0-247">[Try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimlerini hata işleme ve günlüğe kaydetme ile kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-247">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="6d5b0-248">Bir `try-catch` bildiriminde `Component` etiketi yardımcısını sarmalama yerine, `Component` etiketi Yardımcısı tarafından işlenen bileşene hata işleme mantığını koyun.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-248">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="6d5b0-249">Gelişmiş senaryolar</span><span class="sxs-lookup"><span data-stu-id="6d5b0-249">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="6d5b0-250">Özyinelemeli işleme</span><span class="sxs-lookup"><span data-stu-id="6d5b0-250">Recursive rendering</span></span>

<span data-ttu-id="6d5b0-251">Bileşenler yinelemeli olarak iç içe olabilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-251">Components can be nested recursively.</span></span> <span data-ttu-id="6d5b0-252">Bu, özyinelemeli veri yapılarını temsil etmek için kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-252">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="6d5b0-253">Örneğin, bir `TreeNode` bileşeni, düğüm alt öğelerinin her biri için daha fazla `TreeNode` bileşeni işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-253">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="6d5b0-254">Yinelemeli olarak işlenirken, sonsuz özyineleme sonucu veren kodlama desenlerinden kaçının:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-254">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="6d5b0-255">Bir döngüyü içeren bir veri yapısını yinelemeli olarak işlemez.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-255">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="6d5b0-256">Örneğin, alt öğeleri içeren bir ağaç düğümünü işlemez.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-256">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="6d5b0-257">Bir döngüyü içeren bir düzen zinciri oluşturmayın.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-257">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="6d5b0-258">Örneğin, düzeni kendisi olan bir düzen oluşturmayın.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-258">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="6d5b0-259">Son kullanıcının, kötü amaçlı veri girişi veya JavaScript birlikte çalışabilirlik çağrıları aracılığıyla özyineleme/varyantları (kurallar) ihlal etmemenizi izin verme.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-259">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="6d5b0-260">Oluşturma sırasında sonsuz döngüler:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-260">Infinite loops during rendering:</span></span>

* <span data-ttu-id="6d5b0-261">İşleme işleminin süresiz olarak devam etmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-261">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="6d5b0-262">Sonlandırılmamış bir döngü oluşturmaya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-262">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="6d5b0-263">Bu senaryolarda, etkilenen devre askıda kalır ve iş parçacığı genellikle şunları yapmayı dener:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-263">In these scenarios, the affected circuit hangs, and the thread usually attempts to:</span></span>

* <span data-ttu-id="6d5b0-264">Süresiz olarak işletim sisteminin izin verdiği CPU süresini çok fazla kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-264">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="6d5b0-265">Sınırsız miktarda sunucu belleği tükettin.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-265">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="6d5b0-266">Sınırsız bellek tüketme, Sonlandırılmamış bir döngünün her yinelemede bir koleksiyona giriş eklediği senaryoya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-266">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="6d5b0-267">Sonsuz özyineleme desenlerinin önüne geçmek için, özyinelemeli işleme kodunun uygun durdurma koşullarını içerdiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-267">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="6d5b0-268">Özel işleme ağacı mantığı</span><span class="sxs-lookup"><span data-stu-id="6d5b0-268">Custom render tree logic</span></span>

<span data-ttu-id="6d5b0-269">Çoğu Blazor bileşen *. Razor* dosyaları olarak uygulanır ve çıktılarını oluşturmak için `RenderTreeBuilder` üzerinde çalışan Logic üretmek için derlenir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-269">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="6d5b0-270">Bir geliştirici, yordamsal C# kodu kullanarak `RenderTreeBuilder` mantığını el ile uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-270">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="6d5b0-271">Daha fazla bilgi için bkz. <xref:blazor/components#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-271">For more information, see <xref:blazor/components#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="6d5b0-272">El ile işleme ağacı Oluşturucu mantığının kullanımı, genel bileşen geliştirme için önerilmeyen gelişmiş ve güvenli olmayan bir senaryo olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-272">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="6d5b0-273">`RenderTreeBuilder` kod yazılmışsa, geliştirici kodun doğruluğunu garanti etmelidir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-273">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="6d5b0-274">Örneğin, geliştirici şunları sağlamalıdır:</span><span class="sxs-lookup"><span data-stu-id="6d5b0-274">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="6d5b0-275">`OpenElement` ve `CloseElement` çağrıları doğru dengelenir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-275">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="6d5b0-276">Öznitelikler yalnızca doğru yerlere eklenir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-276">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="6d5b0-277">Hatalı elle işleme ağacı Oluşturucu mantığı kilitlenmeler, sunucu askıda kalma ve güvenlik açıkları dahil rastgele tanımsız davranışa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-277">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="6d5b0-278">El ile işleme ağacı Oluşturucu mantığını aynı karmaşıklık düzeyinde ve aynı *tehlike* düzeyiyle, el ile derleme kodu veya MSIL yönergeleri yazarak değerlendirin.</span><span class="sxs-lookup"><span data-stu-id="6d5b0-278">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
