---
title: ASP.NET Core Ara yazılımıyla HTTP işleyicilerini ve modülleri geçirme
author: rick-anderson
description: ''
ms.author: tdykstra
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: 84381210910c66a7d121120b8c6b0f046cae8c4f
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207801"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="6e85d-102">ASP.NET Core Ara yazılımıyla HTTP işleyicilerini ve modülleri geçirme</span><span class="sxs-lookup"><span data-stu-id="6e85d-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="6e85d-103">Bu makale mevcut ASP.NET geçirme [HTTP modüller ve işleyiciler system.webserver gelen](/iis/configuration/system.webserver/) ASP.NET core'a [ara yazılım](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="6e85d-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="6e85d-104">Modüller ve işleyiciler revisited</span><span class="sxs-lookup"><span data-stu-id="6e85d-104">Modules and handlers revisited</span></span>

<span data-ttu-id="6e85d-105">ASP.NET Core ara yazılımı için devam etmeden önce şimdi ilk HTTP modüller ve işleyiciler nasıl özeti:</span><span class="sxs-lookup"><span data-stu-id="6e85d-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Modül işleyicisi](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="6e85d-107">**İşleyicileri şunlardır:**</span><span class="sxs-lookup"><span data-stu-id="6e85d-107">**Handlers are:**</span></span>

* <span data-ttu-id="6e85d-108">Uygulayan sınıflar [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="6e85d-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="6e85d-109">Gibi belirli dosya adı veya uzantısı, istekleri işlemek için kullanılan *.report*</span><span class="sxs-lookup"><span data-stu-id="6e85d-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="6e85d-110">[Yapılandırılmış](/iis/configuration/system.webserver/handlers/) içinde *Web.config*</span><span class="sxs-lookup"><span data-stu-id="6e85d-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="6e85d-111">**Modüller şunlardır:**</span><span class="sxs-lookup"><span data-stu-id="6e85d-111">**Modules are:**</span></span>

* <span data-ttu-id="6e85d-112">Uygulayan sınıflar [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="6e85d-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="6e85d-113">Her istek için çağrılır</span><span class="sxs-lookup"><span data-stu-id="6e85d-113">Invoked for every request</span></span>

* <span data-ttu-id="6e85d-114">Şunları (bir isteğin diğer işlemleri durdurmak) kısa devre oluşturur</span><span class="sxs-lookup"><span data-stu-id="6e85d-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="6e85d-115">HTTP yanıtı ekleyin veya kendi oluşturmak için</span><span class="sxs-lookup"><span data-stu-id="6e85d-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="6e85d-116">[Yapılandırılmış](/iis/configuration/system.webserver/modules/) içinde *Web.config*</span><span class="sxs-lookup"><span data-stu-id="6e85d-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="6e85d-117">**Hangi modüllerin gelen istekleri işleme sırası tarafından belirlenir:**</span><span class="sxs-lookup"><span data-stu-id="6e85d-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="6e85d-118">[Uygulama yaşam döngüsü](https://msdn.microsoft.com/library/ms227673.aspx), ASP.NET tarafından tetiklenen bir serisi olayları olduğu: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Her modülü, bir veya daha fazla olay işleyicisi oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6e85d-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="6e85d-119">Aynı olay, siparişin, bunlar yapılandırılmış için *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="6e85d-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="6e85d-120">Modüller yanı sıra, yaşam döngüsü olayları için işleyiciler ekleyebilirsiniz, *Global.asax.cs* dosya.</span><span class="sxs-lookup"><span data-stu-id="6e85d-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="6e85d-121">Bu işleyiciler, yapılandırılmış modülleri işleyiciler sonra çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6e85d-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="6e85d-122">İşleyicileri ve modüllerinden Ara yazılıma</span><span class="sxs-lookup"><span data-stu-id="6e85d-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="6e85d-123">**Ara yazılım HTTP modüller ve işleyiciler basit şunlardır:**</span><span class="sxs-lookup"><span data-stu-id="6e85d-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="6e85d-124">Modüller, işleyiciler *Global.asax.cs*, *Web.config* (hariç IIS yapılandırması) ve uygulama yaşam döngüsü kayboldu</span><span class="sxs-lookup"><span data-stu-id="6e85d-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="6e85d-125">Ara yazılım tarafından devralınırsa hem modüller ve işleyiciler rollerini alınmış</span><span class="sxs-lookup"><span data-stu-id="6e85d-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="6e85d-126">Ara yazılım, kod kullanarak yapılandırılmış yerine *Web.config*</span><span class="sxs-lookup"><span data-stu-id="6e85d-126">Middleware are configured using code rather than in *Web.config*</span></span>

* <span data-ttu-id="6e85d-127">[İşlem hattı dallanma](xref:fundamentals/middleware/index#use-run-and-map) yalnızca URL de istek üst bilgileri, sorgu dizeleri, vb. üzerinde dayalı belirli ara yazılımı için istekleri gönderdiğiniz sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e85d-127">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="6e85d-128">**Ara yazılım modüllerini çok benzer:**</span><span class="sxs-lookup"><span data-stu-id="6e85d-128">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="6e85d-129">Her istek için asıl çağrılır</span><span class="sxs-lookup"><span data-stu-id="6e85d-129">Invoked in principle for every request</span></span>

* <span data-ttu-id="6e85d-130">Bir istek tarafından iki duruma [isteğin sonraki Ara yazılıma geçmiyor.](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="6e85d-130">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="6e85d-131">Kendi HTTP yanıtı oluşturmak için</span><span class="sxs-lookup"><span data-stu-id="6e85d-131">Able to create their own HTTP response</span></span>

<span data-ttu-id="6e85d-132">**Ara yazılım ve modülleri farklı bir sırada işlenir:**</span><span class="sxs-lookup"><span data-stu-id="6e85d-132">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="6e85d-133">Ara yazılım sırası içinde eklenir istek ardışık düzende modülleri sırasını çoğunlukla tabanlı çalıştırırken sırasını temel [uygulama yaşam döngüsü](https://msdn.microsoft.com/library/ms227673.aspx) olayları</span><span class="sxs-lookup"><span data-stu-id="6e85d-133">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="6e85d-134">Modüller sırasını istekleri ve yanıtları için aynı olsa da Ara yazılım yanıtları için istekleri, tersine sırasıdır</span><span class="sxs-lookup"><span data-stu-id="6e85d-134">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="6e85d-135">Bkz: [IApplicationBuilder ile bir ara yazılım ardışık düzenini oluşturun](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="6e85d-135">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Ara yazılım](http-modules/_static/middleware.png)

<span data-ttu-id="6e85d-137">Yukarıdaki görüntüde, kimlik doğrulaması ara yazılım istek nasıl short-circuited unutmayın.</span><span class="sxs-lookup"><span data-stu-id="6e85d-137">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="6e85d-138">Ara yazılıma modülü kod geçirme</span><span class="sxs-lookup"><span data-stu-id="6e85d-138">Migrating module code to middleware</span></span>

<span data-ttu-id="6e85d-139">Var olan bir HTTP modülü, aşağıdakine benzer görünecektir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-139">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="6e85d-140">Gösterildiği [ara yazılım](xref:fundamentals/middleware/index) sayfasında, bir ASP.NET Core ara yazılımı olduğunu gösteren bir sınıf bir `Invoke` yöntemi alma bir `HttpContext` ve döndüren bir `Task`.</span><span class="sxs-lookup"><span data-stu-id="6e85d-140">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="6e85d-141">Yeni Ara yazılımınızı şöyle görünür:</span><span class="sxs-lookup"><span data-stu-id="6e85d-141">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="6e85d-142">Yukarıdaki ara yazılım şablonu bölümünden alındığı [yazma ara yazılımı](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="6e85d-142">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="6e85d-143">*MyMiddlewareExtensions* yardımcı bir sınıf içinde ara yazılımını yapılandırma kolaylaştırır, `Startup` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="6e85d-143">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="6e85d-144">`UseMyMiddleware` Yöntemi, ara yazılım sınıfı istek ardışık düzene ekler.</span><span class="sxs-lookup"><span data-stu-id="6e85d-144">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="6e85d-145">Ara yazılım tarafından gerekli hizmetleri Ara oluşturucuda eklenen.</span><span class="sxs-lookup"><span data-stu-id="6e85d-145">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="6e85d-146">Örneğin kullanıcının yetkili olmayan bir istek modülünüzde sonlandırabilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-146">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="6e85d-147">Bir ara yazılım değil çağırarak bu işleme `Invoke` ardışık düzende sonraki ara yazılımı üzerinde.</span><span class="sxs-lookup"><span data-stu-id="6e85d-147">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="6e85d-148">Yanıt yolu geri ardışık düzeninden yaptığında, yine de önceki middlewares çağrılacak çünkü bu tam olarak istek sonlandırmaz aklınızda bulundurun.</span><span class="sxs-lookup"><span data-stu-id="6e85d-148">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="6e85d-149">Modülün işlevi yeni Ara yazılımınızı geçirdiğinizde, kodunuzu için derle değil bulabilirsiniz `HttpContext` sınıf, ASP.NET Core önemli ölçüde değişti.</span><span class="sxs-lookup"><span data-stu-id="6e85d-149">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="6e85d-150">[Daha sonra](#migrating-to-the-new-httpcontext), yeni ASP.NET temel HttpContext geçirmeyi öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="6e85d-150">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="6e85d-151">İstek ardışık düzenini uygulamasına geçirme modülü ekleme</span><span class="sxs-lookup"><span data-stu-id="6e85d-151">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="6e85d-152">HTTP modüllerinden, istek kullanarak işlem hattı genellikle eklenir *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="6e85d-152">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="6e85d-153">Bu dönüştürme [yeni Ara yazılımınızı ekleme](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) istek işlem hattı için `Startup` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="6e85d-153">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="6e85d-154">Bir modül olarak işlenen bir olayın noktanın tam yeni Ara yazılımınızı eklediğiniz yerde işlem hattındaki bağlıdır (`BeginRequest`, `EndRequest`, vs.) ve modül listenizde, siparişi *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="6e85d-154">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="6e85d-155">Daha önce belirtildiği gibi ASP.NET Core hiçbir uygulama yaşam döngüsü yoktur ve modülleri tarafından kullanılan sırası yanıtlarını ara yazılımı tarafından işlenme sırasını farklıdır.</span><span class="sxs-lookup"><span data-stu-id="6e85d-155">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="6e85d-156">Bu daha zor sıralama kullanacağınıza karar vermeniz.</span><span class="sxs-lookup"><span data-stu-id="6e85d-156">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="6e85d-157">Sıralama bir soruna dönüşmesi durumunda, bağımsız olarak sıralanabilir birden fazla ara yazılım bileşenlerine modülünüzde bölebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6e85d-157">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="6e85d-158">Ara yazılıma geçirilmesi işleyici kodu</span><span class="sxs-lookup"><span data-stu-id="6e85d-158">Migrating handler code to middleware</span></span>

<span data-ttu-id="6e85d-159">Bir HTTP işleyicisini şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="6e85d-159">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="6e85d-160">ASP.NET Core projenizde, bu şuna benzer bir ara yazılım için çevirir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-160">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="6e85d-161">Bu ara yazılım modüllerini karşılık gelen bir ara yazılım çok benzer.</span><span class="sxs-lookup"><span data-stu-id="6e85d-161">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="6e85d-162">Yalnızca gerçek fark burada hiçbir yoktur `_next.Invoke(context)`.</span><span class="sxs-lookup"><span data-stu-id="6e85d-162">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="6e85d-163">Olacaktır işleyici çağırmak için hiçbir sonraki ara yazılım istek ardışık düzenini sonunda olduğundan, mantıklıdır.</span><span class="sxs-lookup"><span data-stu-id="6e85d-163">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="6e85d-164">İstek ardışık düzenini uygulamasına geçirme işleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="6e85d-164">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="6e85d-165">Bir HTTP işleyicisini yapılandırma yapılır *Web.config* ve şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="6e85d-165">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="6e85d-166">Bu istek işlem hattı, yeni bir işleyici Ara yazılımınızı ekleyerek dönüştüremedi, `Startup` sınıfı, ara yazılım modüllerini dönüştürülen benzer.</span><span class="sxs-lookup"><span data-stu-id="6e85d-166">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="6e85d-167">Bu yaklaşım, tüm istekler için yeni bir işleyici Ara yazılımınızı gönderir sorunudur.</span><span class="sxs-lookup"><span data-stu-id="6e85d-167">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="6e85d-168">Ancak, yalnızca ara yazılımınızı ulaşmak için istekleri belirli bir uzantıya sahip istersiniz.</span><span class="sxs-lookup"><span data-stu-id="6e85d-168">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="6e85d-169">Bu, HTTP işleyicisi ile olan aynı işlevleri verirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6e85d-169">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="6e85d-170">İstekleri belirli bir uzantıya sahip bir işlem hattı oluşturmak için bir çözüm ise kullanarak `MapWhen` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="6e85d-170">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="6e85d-171">Aynı bunu `Configure` diğer ara yazılımdan eklediğiniz yöntemi:</span><span class="sxs-lookup"><span data-stu-id="6e85d-171">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="6e85d-172">`MapWhen` Bu parametreleri alır:</span><span class="sxs-lookup"><span data-stu-id="6e85d-172">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="6e85d-173">Alan bir lambda `HttpContext` ve döndürür `true` istek dalını giderseniz.</span><span class="sxs-lookup"><span data-stu-id="6e85d-173">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="6e85d-174">Bu, yalnızca kendi uzantı üzerinde ancak istek üstbilgilerini, sorgu dizesi parametrelerini temel istekleri dallandırabilirsiniz anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="6e85d-174">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="6e85d-175">Alan bir lambda bir `IApplicationBuilder` ve dal için tüm ara yazılımı ekler.</span><span class="sxs-lookup"><span data-stu-id="6e85d-175">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="6e85d-176">Başka bir deyişle, ek bir ara yazılım dala önünde işleyici Ara yazılımınızı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6e85d-176">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="6e85d-177">Tüm istekleri dal çağrılacak önce ara yazılım ardışık düzenine eklenen; Dal üzerinde herhangi bir etkisi olacaktır.</span><span class="sxs-lookup"><span data-stu-id="6e85d-177">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="6e85d-178">Yükleme seçenekleri desenini kullanarak, ara yazılım seçenekleri</span><span class="sxs-lookup"><span data-stu-id="6e85d-178">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="6e85d-179">Bazı modüller ve işleyiciler depolanan yapılandırma seçeneğiniz *Web.config*. ASP.NET Core yerine yeni bir yapılandırma modeli ancak kullanılır *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="6e85d-179">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="6e85d-180">Yeni [yapılandırma sistemi](xref:fundamentals/configuration/index) bunu çözmek için bu seçeneği sunar:</span><span class="sxs-lookup"><span data-stu-id="6e85d-180">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="6e85d-181">Ara yazılım seçeneklere gösterildiği gibi doğrudan ekleme [sonraki bölümde](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="6e85d-181">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="6e85d-182">Kullanım [seçenekleri deseni](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="6e85d-182">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="6e85d-183">Örneğin, ara yazılım seçenekleri tutmak için bir sınıf oluşturun:</span><span class="sxs-lookup"><span data-stu-id="6e85d-183">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="6e85d-184">Seçenek değerleri Store</span><span class="sxs-lookup"><span data-stu-id="6e85d-184">Store the option values</span></span>

   <span data-ttu-id="6e85d-185">Yapılandırma sistemi seçenek değerleri istediğiniz herhangi bir yerde depolamanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e85d-185">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="6e85d-186">Ancak, en siteleri kullanım *appsettings.json*, biz bu yaklaşımı benimsemeye:</span><span class="sxs-lookup"><span data-stu-id="6e85d-186">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="6e85d-187">*MyMiddlewareOptionsSection* bir bölüm adı'aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="6e85d-187">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="6e85d-188">Seçenekleri sınıfınızın adıyla aynı olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="6e85d-188">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="6e85d-189">Seçenek değerleri seçenekleri sınıfıyla ilişkilendirme</span><span class="sxs-lookup"><span data-stu-id="6e85d-189">Associate the option values with the options class</span></span>

    <span data-ttu-id="6e85d-190">Seçenek türünün ilişkilendirmek için ASP.NET Core'nın bağımlılık ekleme framework seçenekleri deseni kullanır (gibi `MyMiddlewareOptions`) ile bir `MyMiddlewareOptions` gerçek seçenekleri nesnesi.</span><span class="sxs-lookup"><span data-stu-id="6e85d-190">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="6e85d-191">Güncelleştirme, `Startup` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="6e85d-191">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="6e85d-192">Kullanıyorsanız *appsettings.json*, yapılandırma Oluşturucu'da ekleme `Startup` Oluşturucusu:</span><span class="sxs-lookup"><span data-stu-id="6e85d-192">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="6e85d-193">Seçenekleri Hizmeti'ni yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="6e85d-193">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="6e85d-194">Seçeneklerinizi, seçenek sınıfı ile ilişkilendirin:</span><span class="sxs-lookup"><span data-stu-id="6e85d-194">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="6e85d-195">Seçenekler, ara yazılım Oluşturucu içine yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="6e85d-195">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="6e85d-196">Bu, seçenekleri bir denetleyici içine ekleme için benzer.</span><span class="sxs-lookup"><span data-stu-id="6e85d-196">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="6e85d-197">[UseMiddleware](#http-modules-usemiddleware) , ara yazılım ekler genişletme yöntemi `IApplicationBuilder` bağımlılık ekleme üstlenir.</span><span class="sxs-lookup"><span data-stu-id="6e85d-197">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="6e85d-198">Bu sınırlı değildir `IOptions` nesneleri.</span><span class="sxs-lookup"><span data-stu-id="6e85d-198">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="6e85d-199">Ara yazılımınızı gerektiren herhangi bir nesne, bu şekilde yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e85d-199">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="6e85d-200">Ara yazılım seçenekleri aracılığıyla doğrudan ekleme yükleniyor</span><span class="sxs-lookup"><span data-stu-id="6e85d-200">Loading middleware options through direct injection</span></span>

<span data-ttu-id="6e85d-201">Seçenekleri deseni gevşek seçenekleri değerleri ve tüketicilerini eşlemeyi oluşturan avantajına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="6e85d-201">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="6e85d-202">Bir seçenek sınıfı gerçek seçenekleri değerleri ile ilişkilendirdiğiniz sonra başka bir sınıf seçenekleri bağımlılık ekleme çerçevesi aracılığıyla erişebilir.</span><span class="sxs-lookup"><span data-stu-id="6e85d-202">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="6e85d-203">Geçici bir çözüm seçenekleri değerleri geçirmek için gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="6e85d-203">There's no need to pass around options values.</span></span>

<span data-ttu-id="6e85d-204">Aynı ara yazılım farklı seçeneklerle iki kez kullanmak istiyorsanız, bunu ancak ayırır.</span><span class="sxs-lookup"><span data-stu-id="6e85d-204">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="6e85d-205">Kullanılan farklı dallara farklı rollere izin verme örneğin bir yetkilendirme ara yazılımı.</span><span class="sxs-lookup"><span data-stu-id="6e85d-205">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="6e85d-206">İki farklı seçenekler nesneleri bir seçenek sınıfı ile ilişkilendiremezsiniz.</span><span class="sxs-lookup"><span data-stu-id="6e85d-206">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="6e85d-207">Çözüm seçenekleri nesneleri gerçek seçenekleri değerleriyle almaktır, `Startup` sınıfı ve bunları doğrudan Ara yazılımınızı her bir örneğini geçirin.</span><span class="sxs-lookup"><span data-stu-id="6e85d-207">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="6e85d-208">İkinci bir anahtar ekler *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="6e85d-208">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="6e85d-209">İkinci bir set seçenekleri eklemek için *appsettings.json* dosya, yeni bir anahtar benzersiz şekilde tanımlamak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="6e85d-209">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="6e85d-210">Seçenekleri değerleri almak ve bunları ara yazılımıyla geçirin.</span><span class="sxs-lookup"><span data-stu-id="6e85d-210">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="6e85d-211">`Use...` (Bu, ara yazılım ardışık düzene ekler) genişletme yöntemi olduğunu seçeneği değerleri geçirmek için mantıksal bir yer:</span><span class="sxs-lookup"><span data-stu-id="6e85d-211">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="6e85d-212">Seçenekler parametresi gerçekleştirilecek Ara yazılımlarını etkinleştir.</span><span class="sxs-lookup"><span data-stu-id="6e85d-212">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="6e85d-213">Bir aşırı yüklemesini sağlamak `Use...` uzantı metodu (Seçenekler parametresi alır ve buna ileten `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="6e85d-213">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="6e85d-214">Zaman `UseMiddleware` çağrılır ara yazılım nesnesi örneğini oluşturduğunda parametrelerle, bu parametreleri, ara yazılım Oluşturucu için geçirir.</span><span class="sxs-lookup"><span data-stu-id="6e85d-214">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="6e85d-215">Bu seçenekler nesnesinde nasıl sarmalar Not bir `OptionsWrapper` nesne.</span><span class="sxs-lookup"><span data-stu-id="6e85d-215">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="6e85d-216">Bu uygulayan `IOptions`ara yazılım Oluşturucu tarafından beklendiği gibi.</span><span class="sxs-lookup"><span data-stu-id="6e85d-216">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="6e85d-217">İçin yeni HttpContext geçirme</span><span class="sxs-lookup"><span data-stu-id="6e85d-217">Migrating to the new HttpContext</span></span>

<span data-ttu-id="6e85d-218">Daha önce gördüğünüzle, `Invoke` Ara yazılımınızı yönteminde türünde bir parametre alan `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="6e85d-218">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="6e85d-219">`HttpContext` ASP.NET Core önemli ölçüde değişti.</span><span class="sxs-lookup"><span data-stu-id="6e85d-219">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="6e85d-220">Bu bölümde, en sık kullanılan özelliklerini çevrilecek gösterilmektedir [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) yeni `Microsoft.AspNetCore.Http.HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="6e85d-220">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="6e85d-221">HttpContext</span><span class="sxs-lookup"><span data-stu-id="6e85d-221">HttpContext</span></span>

<span data-ttu-id="6e85d-222">**HttpContext.Items** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-222">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="6e85d-223">**Benzersiz istek kimliği (System.Web.HttpContext karşılık gelen)**</span><span class="sxs-lookup"><span data-stu-id="6e85d-223">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="6e85d-224">Her istek için benzersiz bir kimlik sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e85d-224">Gives you a unique id for each request.</span></span> <span data-ttu-id="6e85d-225">Günlükleri dahil etmek çok kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="6e85d-225">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="6e85d-226">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="6e85d-226">HttpContext.Request</span></span>

<span data-ttu-id="6e85d-227">**HttpContext.Request.HttpMethod** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-227">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="6e85d-228">**HttpContext.Request.QueryString** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-228">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="6e85d-229">**HttpContext.Request.Url** ve **HttpContext.Request.RawUrl** için çevir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-229">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="6e85d-230">**HttpContext.Request.IsSecureConnection** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-230">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="6e85d-231">**HttpContext.Request.UserHostAddress** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-231">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="6e85d-232">**HttpContext.Request.Cookies** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-232">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="6e85d-233">**HttpContext.Request.RequestContext.RouteData** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="6e85d-234">**HttpContext.Request.Headers** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-234">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="6e85d-235">**HttpContext.Request.UserAgent** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-235">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="6e85d-236">**HttpContext.Request.UrlReferrer** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-236">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="6e85d-237">**HttpContext.Request.ContentType** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-237">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="6e85d-238">**HttpContext.Request.Form** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-238">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="6e85d-239">Yalnızca içerik alt tür ise form değerlerini okumasını *x-www-form-urlencoded işlemek* veya *form verisinin*.</span><span class="sxs-lookup"><span data-stu-id="6e85d-239">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="6e85d-240">**HttpContext.Request.InputStream** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-240">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="6e85d-241">Bu kod yalnızca bir işlem hattı, sonunda bir işleyici türü Ara kullanın.</span><span class="sxs-lookup"><span data-stu-id="6e85d-241">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="6e85d-242">İstek başına yalnızca bir kez yukarıda da gösterildiği gibi ham gövde okuyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6e85d-242">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="6e85d-243">İlk salt okunur, boş bir gövdeyi okuyacaksa sonra gövdesi okumaya çalışırken bir ara yazılım.</span><span class="sxs-lookup"><span data-stu-id="6e85d-243">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="6e85d-244">Bu, bir arabellek yapıldığı için bir form daha önce gösterildiği gibi okuma için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="6e85d-244">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="6e85d-245">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="6e85d-245">HttpContext.Response</span></span>

<span data-ttu-id="6e85d-246">**HttpContext.Response.Status** ve **HttpContext.Response.StatusDescription** için çevir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-246">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="6e85d-247">**HttpContext.Response.ContentEncoding** ve **HttpContext.Response.ContentType** için çevir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-247">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="6e85d-248">**HttpContext.Response.ContentType** üzerinde kendi ayrıca için çevirir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-248">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="6e85d-249">**HttpContext.Response.Output** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-249">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="6e85d-250">**HttpContext.Response.TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="6e85d-250">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="6e85d-251">Dosya tanıtıcısıyla ele [burada](../fundamentals/request-features.md#middleware-and-request-features).</span><span class="sxs-lookup"><span data-stu-id="6e85d-251">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="6e85d-252">**HttpContext.Response.Headers**</span><span class="sxs-lookup"><span data-stu-id="6e85d-252">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="6e85d-253">Yanıt üst bilgileri gönderme gerçeğiyle karmaşık yanıt gövdesi için herhangi bir şey yazıldıktan sonra ayarını yaparsanız, bunlar değil gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6e85d-253">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="6e85d-254">Sağ yanıt başlatır için yazmadan önce çağrılacak bir geri çağırma yöntemi ayarlamak için kullanılan çözümüdür.</span><span class="sxs-lookup"><span data-stu-id="6e85d-254">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="6e85d-255">Bu en iyi başlangıcında gerçekleştirilir `Invoke` Ara yazılımınızı yöntemi.</span><span class="sxs-lookup"><span data-stu-id="6e85d-255">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="6e85d-256">Buna, yanıt üstbilgilerini ayarlar bu geri çağırma yöntemi var.</span><span class="sxs-lookup"><span data-stu-id="6e85d-256">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="6e85d-257">Aşağıdaki kod olarak adlandırılan bir geri çağırma yöntemi ayarlar `SetHeaders`:</span><span class="sxs-lookup"><span data-stu-id="6e85d-257">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="6e85d-258">`SetHeaders` Geri çağırma yöntemi şuna benzeyecektir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-258">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="6e85d-259">**HttpContext.Response.Cookies**</span><span class="sxs-lookup"><span data-stu-id="6e85d-259">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="6e85d-260">Tanımlama bilgilerini seyahat tarayıcıya bir *Set-Cookie* yanıtı üstbilgisi.</span><span class="sxs-lookup"><span data-stu-id="6e85d-260">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="6e85d-261">Sonuç olarak, tanımlama bilgileri gönderme kullanılanlarla aynı geri çağırma yanıt üst bilgilerini göndermek için gerektirir:</span><span class="sxs-lookup"><span data-stu-id="6e85d-261">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="6e85d-262">`SetCookies` Geri çağırma yöntemi, aşağıdaki gibi görünür:</span><span class="sxs-lookup"><span data-stu-id="6e85d-262">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="6e85d-263">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6e85d-263">Additional resources</span></span>

* [<span data-ttu-id="6e85d-264">HTTP işleyicilerini ve HTTP modüllerini genel bakış</span><span class="sxs-lookup"><span data-stu-id="6e85d-264">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="6e85d-265">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6e85d-265">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="6e85d-266">Uygulama Başlatma</span><span class="sxs-lookup"><span data-stu-id="6e85d-266">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="6e85d-267">Ara Yazılım</span><span class="sxs-lookup"><span data-stu-id="6e85d-267">Middleware</span></span>](xref:fundamentals/middleware/index)
