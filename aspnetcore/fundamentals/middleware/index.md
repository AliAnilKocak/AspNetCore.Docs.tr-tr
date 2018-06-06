---
title: ASP.NET Core Ara
author: rick-anderson
description: ASP.NET Core ara yazılımı ve istek ardışık düzenini hakkında bilgi edinin.
manager: wpickett
ms.author: riande
ms.date: 01/22/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/middleware/index
ms.openlocfilehash: 381c1a5cecee945559ea0dabd0aa086c8d52b43a
ms.sourcegitcommit: 43bd79667bbdc8a07bd39fb4cd6f7ad3e70212fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34729174"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="79ef1-103">ASP.NET Core Ara</span><span class="sxs-lookup"><span data-stu-id="79ef1-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="79ef1-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="79ef1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="79ef1-105">[Görüntülemek veya karşıdan örnek kod](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/index/sample) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="79ef1-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/index/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="what-is-middleware"></a><span data-ttu-id="79ef1-106">Ara yazılım nedir?</span><span class="sxs-lookup"><span data-stu-id="79ef1-106">What is middleware?</span></span>

<span data-ttu-id="79ef1-107">Ara yazılım istekleri ve yanıtları işlemek için bir uygulama ardışık düzenine birleştirilmiş bir yazılımdır.</span><span class="sxs-lookup"><span data-stu-id="79ef1-107">Middleware is software that's assembled into an application pipeline to handle requests and responses.</span></span> <span data-ttu-id="79ef1-108">Her bileşen:</span><span class="sxs-lookup"><span data-stu-id="79ef1-108">Each component:</span></span>

* <span data-ttu-id="79ef1-109">İstek ardışık düzende sonraki bileşene geçmek seçer.</span><span class="sxs-lookup"><span data-stu-id="79ef1-109">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="79ef1-110">İş önce ve ardışık düzende sonraki bileşene çağrıldıktan sonra gerçekleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="79ef1-110">Can perform work before and after the next component in the pipeline is invoked.</span></span> 

<span data-ttu-id="79ef1-111">İstek temsilciler istek ardışık düzenini oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79ef1-111">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="79ef1-112">İstek temsilcileri her HTTP isteği işler.</span><span class="sxs-lookup"><span data-stu-id="79ef1-112">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="79ef1-113">Temsilcileri kullanarak yapılandırılmış olan istek [çalıştırmak](/dotnet/api/microsoft.aspnetcore.builder.runextensions), [harita](/dotnet/api/microsoft.aspnetcore.builder.mapextensions), ve [kullanım](/dotnet/api/microsoft.aspnetcore.builder.useextensions) genişletme yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="79ef1-113">Request delegates are configured using [Run](/dotnet/api/microsoft.aspnetcore.builder.runextensions), [Map](/dotnet/api/microsoft.aspnetcore.builder.mapextensions), and [Use](/dotnet/api/microsoft.aspnetcore.builder.useextensions) extension methods.</span></span> <span data-ttu-id="79ef1-114">Yeniden kullanılabilir bir sınıfta tanımlanabilir veya ayrı istek temsilci (satır içi ara yazılımı olarak adlandırılır) bir anonim yöntemi olarak belirtilen satır içi olabilir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-114">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="79ef1-115">Bu yeniden kullanılabilir sınıfları ve satır içi anonim yöntemler *ara yazılımı*, veya *ara yazılımı bileşenleri*.</span><span class="sxs-lookup"><span data-stu-id="79ef1-115">These reusable classes and in-line anonymous methods are *middleware*, or *middleware components*.</span></span> <span data-ttu-id="79ef1-116">Her ara yazılım bileşeni istek kanalında, ardışık düzende sonraki bileşene çağırma veya zincir uygunsa kısa devre sorumludur.</span><span class="sxs-lookup"><span data-stu-id="79ef1-116">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline, or short-circuiting the chain if appropriate.</span></span>

<span data-ttu-id="79ef1-117">[HTTP modülleri Ara geçirmek](xref:migration/http-modules) istek ardışık düzenlerinde ASP.NET Core ve ASP.NET arasındaki fark açıklanır 4.x ve daha fazla ara yazılımı örnekleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="79ef1-117">[Migrate HTTP Modules to Middleware](xref:migration/http-modules) explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides more middleware samples.</span></span>

## <a name="creating-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="79ef1-118">Bir ara yazılım ardışık düzenini IApplicationBuilder ile oluşturma</span><span class="sxs-lookup"><span data-stu-id="79ef1-118">Creating a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="79ef1-119">ASP.NET Core istek ardışık düzen isteği temsilciler (iş parçacığı yürütme aşağıdaki siyah ok) Bu diyagramda gösterildiği gibi birbiri ardından, olarak adlandırılan, bir dizi oluşur:</span><span class="sxs-lookup"><span data-stu-id="79ef1-119">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other, as this diagram shows (the thread of execution follows the black arrows):</span></span>

![İstek işleme düzeni ulaşan, üç middlewares ve uygulama bırakarak yanıt aracılığıyla işleme isteği gösteriliyor.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="79ef1-123">Her temsilci, önce ve sonra İleri temsilci işlemleri yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="79ef1-123">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="79ef1-124">Ayrıca, bir temsilci bir istek istek ardışık düzenini kısa devre adlı bir sonraki temsilci değil geçmesine karar verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="79ef1-124">A delegate can also decide to not pass a request to the next delegate, which is called short-circuiting the request pipeline.</span></span> <span data-ttu-id="79ef1-125">Gereksiz iş önler çünkü kısa devre genellikle iyi bir şeydir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-125">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="79ef1-126">Örneğin, statik dosya ara yazılımlarını statik bir dosya için bir istek dönün ve kalan ardışık düzenini kısa devre oluşturur.</span><span class="sxs-lookup"><span data-stu-id="79ef1-126">For example, the static file middleware can return a request for a static file and short-circuit the rest of the pipeline.</span></span> <span data-ttu-id="79ef1-127">Özel durum işleme temsilciler kanalının sonraki aşamalarında oluşan özel durumlarını yakalayabilirsiniz ardışık düzeninde çağrılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-127">Exception-handling delegates need to be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="79ef1-128">En basit olası ASP.NET Core uygulama tüm istekleri işleyen tek istek temsilci ayarlar.</span><span class="sxs-lookup"><span data-stu-id="79ef1-128">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="79ef1-129">Bu durumda, gerçek istek ardışık düzenini içermez.</span><span class="sxs-lookup"><span data-stu-id="79ef1-129">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="79ef1-130">Bunun yerine, tek bir anonim işlevi her HTTP isteğine yanıt olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="79ef1-130">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/sample/Middleware/Startup.cs)]

<span data-ttu-id="79ef1-131">İlk [uygulama. Çalıştırma](/dotnet/api/microsoft.aspnetcore.builder.runextensions) temsilci ardışık sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="79ef1-131">The first [app.Run](/dotnet/api/microsoft.aspnetcore.builder.runextensions) delegate terminates the pipeline.</span></span>

<span data-ttu-id="79ef1-132">İle birlikte birden çok istek temsilcileri zincirleme [uygulama. Kullanım](/dotnet/api/microsoft.aspnetcore.builder.useextensions).</span><span class="sxs-lookup"><span data-stu-id="79ef1-132">You can chain multiple request delegates together with [app.Use](/dotnet/api/microsoft.aspnetcore.builder.useextensions).</span></span> <span data-ttu-id="79ef1-133">`next` Parametresi ardışık düzende sonraki temsilci temsil eder.</span><span class="sxs-lookup"><span data-stu-id="79ef1-133">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="79ef1-134">(Ardışık düzen tarafından kısa devre oluşturur olduğunu unutmayın *değil* çağırma *sonraki* parametresi.) Bu örnekte gösterilmiştir gibi öncesinde ve sonrasında sonraki temsilci genellikle eylemleri gerçekleştirebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="79ef1-134">(Remember that you can short-circuit the pipeline by *not* calling the *next* parameter.) You can typically perform actions both before and after the next delegate, as this example demonstrates:</span></span>

[!code-csharp[](index/sample/Chain/Startup.cs?name=snippet1)]

>[!WARNING]
> <span data-ttu-id="79ef1-135">Çağrı yok `next.Invoke` yanıtı istemciye gönderildikten sonra.</span><span class="sxs-lookup"><span data-stu-id="79ef1-135">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="79ef1-136">Değişikliklerini `HttpResponse` yanıt başlatıldıktan sonra bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="79ef1-136">Changes to `HttpResponse` after the response has started will throw an exception.</span></span> <span data-ttu-id="79ef1-137">Örneğin, üst bilgileri, durum kodu, vb., ayarlama gibi değişiklikler, bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="79ef1-137">For example, changes such as setting headers, status code, etc,  will throw an exception.</span></span> <span data-ttu-id="79ef1-138">Yanıt gövdesi çağrıldıktan sonra Yazma `next`:</span><span class="sxs-lookup"><span data-stu-id="79ef1-138">Writing to the response body after calling `next`:</span></span>
> - <span data-ttu-id="79ef1-139">Bir protokolü ihlali neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-139">May cause a protocol violation.</span></span> <span data-ttu-id="79ef1-140">Örneğin, birden çok belirtilen yazma `content-length`.</span><span class="sxs-lookup"><span data-stu-id="79ef1-140">For example, writing more than the stated `content-length`.</span></span>
> - <span data-ttu-id="79ef1-141">Gövde biçimi bozulmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-141">May corrupt the body format.</span></span> <span data-ttu-id="79ef1-142">Örneğin, bir HTML altbilgi CSS dosyaya yazma.</span><span class="sxs-lookup"><span data-stu-id="79ef1-142">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="79ef1-143">[HttpResponse.HasStarted](/dotnet/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) üstbilgileri gönderilen ve/veya gövdesi yazılmış varsa göstermek için yararlı bir ipucu olur.</span><span class="sxs-lookup"><span data-stu-id="79ef1-143">[HttpResponse.HasStarted](/dotnet/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) is a useful hint to indicate if headers have been sent and/or the body has been written to.</span></span>

## <a name="ordering"></a><span data-ttu-id="79ef1-144">Sıralama</span><span class="sxs-lookup"><span data-stu-id="79ef1-144">Ordering</span></span>

<span data-ttu-id="79ef1-145">Ara yazılım bileşenlerinin içinde eklendiğinden sipariş `Configure` , bunlar çağrılan isteklerinde sırası ve yanıtı için ters sırada yöntemi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="79ef1-145">The order that middleware components are added in the `Configure` method defines the order in which they're invoked on requests, and the reverse order for the response.</span></span> <span data-ttu-id="79ef1-146">Bu sıralama, güvenlik, performans ve işlevselliği için önemlidir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-146">This ordering is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="79ef1-147">(Aşağıda gösterilen) yapılandırma yöntemi aşağıdaki ara yazılımı bileşenleri ekler:</span><span class="sxs-lookup"><span data-stu-id="79ef1-147">The Configure method (shown below) adds the following middleware components:</span></span>

1. <span data-ttu-id="79ef1-148">Özel durum/hata işleme</span><span class="sxs-lookup"><span data-stu-id="79ef1-148">Exception/error handling</span></span>
2. <span data-ttu-id="79ef1-149">Statik dosya sunucusu</span><span class="sxs-lookup"><span data-stu-id="79ef1-149">Static file server</span></span>
3. <span data-ttu-id="79ef1-150">Kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="79ef1-150">Authentication</span></span>
4. <span data-ttu-id="79ef1-151">MVC</span><span class="sxs-lookup"><span data-stu-id="79ef1-151">MVC</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="79ef1-152">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="79ef1-152">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)


```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseExceptionHandler("/Home/Error"); // Call first to catch exceptions
                                            // thrown in the following middleware.

    app.UseStaticFiles();                   // Return static files and end pipeline.

    app.UseAuthentication();               // Authenticate before you access
                                           // secure resources.

    app.UseMvcWithDefaultRoute();          // Add MVC to the request pipeline.
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="79ef1-153">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="79ef1-153">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseExceptionHandler("/Home/Error"); // Call first to catch exceptions
                                            // thrown in the following middleware.

    app.UseStaticFiles();                   // Return static files and end pipeline.

    app.UseIdentity();                     // Authenticate before you access
                                           // secure resources.

    app.UseMvcWithDefaultRoute();          // Add MVC to the request pipeline.
}
```

-----------

<span data-ttu-id="79ef1-154">Yukarıdaki kod `UseExceptionHandler` ardışık düzenine eklenen ilk ara yazılım bileşeni; bu nedenle, daha sonra çağrılarında oluşan özel durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="79ef1-154">In the code above, `UseExceptionHandler` is the first middleware component added to the pipeline—therefore, it catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="79ef1-155">Böylece istekleri işlemek ve kalan bileşenleri geçmeden kısa devre oluşturur statik dosya ara yazılımlarını erken ardışık düzen adı verilir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-155">The static file middleware is called early in the pipeline so it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="79ef1-156">Statik dosya ara yazılımlarını sağlar **hiçbir** yetkilendirme denetimleri.</span><span class="sxs-lookup"><span data-stu-id="79ef1-156">The static file middleware provides **no** authorization checks.</span></span> <span data-ttu-id="79ef1-157">Herhangi bir dosya sunulan işlem tarafından altında dahil olmak üzere *wwwroot*, genel olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-157">Any files served by it, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="79ef1-158">Bkz: [statik dosyalar](xref:fundamentals/static-files) statik dosyaları güvenli bir yaklaşım için.</span><span class="sxs-lookup"><span data-stu-id="79ef1-158">See [Static files](xref:fundamentals/static-files) for an approach to secure static files.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="79ef1-159">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="79ef1-159">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)


<span data-ttu-id="79ef1-160">İstek statik dosya ara yazılım tarafından işlenen değil, bu kimlik Ara geçirildiğinde (`app.UseAuthentication`), kimlik doğrulaması gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-160">If the request isn't handled by the static file middleware, it's passed on to the Identity middleware (`app.UseAuthentication`), which performs authentication.</span></span> <span data-ttu-id="79ef1-161">Kimlik, kimliği doğrulanmamış istekler kısa devre oluşturur değil.</span><span class="sxs-lookup"><span data-stu-id="79ef1-161">Identity doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="79ef1-162">İstek kimliğini doğrular rağmen yalnızca bir özel Razor sayfasını veya denetleyici ve eylem MVC seçtikten sonra yetkilendirme (ve reddetme) oluşur.</span><span class="sxs-lookup"><span data-stu-id="79ef1-162">Although Identity authenticates requests,  authorization (and rejection) occurs only after MVC selects a specific Razor Page or controller and action.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="79ef1-163">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="79ef1-163">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="79ef1-164">İstek statik dosya ara yazılım tarafından işlenen değil, bu kimlik Ara geçirildiğinde (`app.UseIdentity`), kimlik doğrulaması gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-164">If the request isn't handled by the static file middleware, it's passed on to the Identity middleware (`app.UseIdentity`), which performs authentication.</span></span> <span data-ttu-id="79ef1-165">Kimlik, kimliği doğrulanmamış istekler kısa devre oluşturur değil.</span><span class="sxs-lookup"><span data-stu-id="79ef1-165">Identity doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="79ef1-166">İstek kimliğini doğrular rağmen yalnızca belirli denetleyici ve eylem MVC seçtikten sonra yetkilendirme (ve reddetme) oluşur.</span><span class="sxs-lookup"><span data-stu-id="79ef1-166">Although Identity authenticates requests,  authorization (and rejection) occurs only after MVC selects a specific controller and action.</span></span>

-----------

<span data-ttu-id="79ef1-167">Aşağıdaki örnek, burada statik dosyalar için istek yanıt sıkıştırma Ara önce statik dosya ara yazılımı tarafından işlenen sıralama bir ara yazılımı gösterir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-167">The following example demonstrates a middleware ordering where requests for static files are handled by the static file middleware before the response compression middleware.</span></span> <span data-ttu-id="79ef1-168">Statik dosyalar bu ara yazılım sıralama ile sıkıştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="79ef1-168">Static files are not compressed with this ordering of the middleware.</span></span> <span data-ttu-id="79ef1-169">MVC yanıtlarının [UseMvcWithDefaultRoute](/dotnet/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) sıkıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-169">The MVC responses from [UseMvcWithDefaultRoute](/dotnet/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseStaticFiles();         // Static files not compressed
                                  // by middleware.
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

<a name="middleware-run-map-use"></a>

### <a name="use-run-and-map"></a><span data-ttu-id="79ef1-170">Kullanmak için çalıştırmak ve eşleme</span><span class="sxs-lookup"><span data-stu-id="79ef1-170">Use, Run, and Map</span></span>

<span data-ttu-id="79ef1-171">HTTP kullanarak ardışık düzen yapılandırma `Use`, `Run`, ve `Map`.</span><span class="sxs-lookup"><span data-stu-id="79ef1-171">You configure the HTTP pipeline using `Use`, `Run`, and `Map`.</span></span> <span data-ttu-id="79ef1-172">`Use` Yöntemi kısa devre oluşturur ardışık düzen (diğer bir deyişle, çağrı değil, bir `next` isteği temsilci).</span><span class="sxs-lookup"><span data-stu-id="79ef1-172">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="79ef1-173">`Run` bir kural ve bazı ara yazılımı bileşenleri getirebilir `Run[Middleware]` ardışık düzen sonunda çalışacak yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="79ef1-173">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="79ef1-174">`Map*` Uzantılar, ardışık düzen dallanma için bir kural kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79ef1-174">`Map*` extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="79ef1-175">[Harita](/dotnet/api/microsoft.aspnetcore.builder.mapextensions) istek ardışık düzenini belirtilen istek yolu eşleşmeleri üzerinde göre dallandırır.</span><span class="sxs-lookup"><span data-stu-id="79ef1-175">[Map](/dotnet/api/microsoft.aspnetcore.builder.mapextensions) branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="79ef1-176">İstek yolu belirtilen yolun ile başlarsa, şube yürütülür.</span><span class="sxs-lookup"><span data-stu-id="79ef1-176">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/sample/Chain/StartupMap.cs?name=snippet1)]

<span data-ttu-id="79ef1-177">Aşağıdaki tabloda isteklerinin ve yanıtlarının gösterilmektedir `http://localhost:1234` önceki kod kullanarak:</span><span class="sxs-lookup"><span data-stu-id="79ef1-177">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="79ef1-178">İstek</span><span class="sxs-lookup"><span data-stu-id="79ef1-178">Request</span></span> | <span data-ttu-id="79ef1-179">Yanıt</span><span class="sxs-lookup"><span data-stu-id="79ef1-179">Response</span></span> |
| --- | --- |
| <span data-ttu-id="79ef1-180">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="79ef1-180">localhost:1234</span></span> | <span data-ttu-id="79ef1-181">Merhaba harita olmayan temsilci gelen.</span><span class="sxs-lookup"><span data-stu-id="79ef1-181">Hello from non-Map delegate.</span></span>  |
| <span data-ttu-id="79ef1-182">localhost:1234 / map1</span><span class="sxs-lookup"><span data-stu-id="79ef1-182">localhost:1234/map1</span></span> | <span data-ttu-id="79ef1-183">Harita Test 1</span><span class="sxs-lookup"><span data-stu-id="79ef1-183">Map Test 1</span></span> |
| <span data-ttu-id="79ef1-184">localhost:1234 / map2</span><span class="sxs-lookup"><span data-stu-id="79ef1-184">localhost:1234/map2</span></span> | <span data-ttu-id="79ef1-185">Harita Test 2</span><span class="sxs-lookup"><span data-stu-id="79ef1-185">Map Test 2</span></span> |
| <span data-ttu-id="79ef1-186">localhost:1234 / map3</span><span class="sxs-lookup"><span data-stu-id="79ef1-186">localhost:1234/map3</span></span> | <span data-ttu-id="79ef1-187">Merhaba harita olmayan temsilci gelen.</span><span class="sxs-lookup"><span data-stu-id="79ef1-187">Hello from non-Map delegate.</span></span>  |

<span data-ttu-id="79ef1-188">Zaman `Map` olan kullanıldığında, eşleşen yolu segment(s) çıkarılır `HttpRequest.Path` ve için eklenen `HttpRequest.PathBase` her istek için.</span><span class="sxs-lookup"><span data-stu-id="79ef1-188">When `Map` is used, the matched path segment(s) are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="79ef1-189">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) istek ardışık düzenini belirtilen koşulun sonucuna göre dallandırır.</span><span class="sxs-lookup"><span data-stu-id="79ef1-189">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="79ef1-190">Herhangi bir koşul türü `Func<HttpContext, bool>` istekleri dalı ardışık eşlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79ef1-190">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="79ef1-191">Aşağıdaki örnekte, bir koşul bir sorgu dizesi değişkeni varolup olmadığını algılamak için kullanılan `branch`:</span><span class="sxs-lookup"><span data-stu-id="79ef1-191">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/sample/Chain/StartupMapWhen.cs?name=snippet1)]

<span data-ttu-id="79ef1-192">Aşağıdaki tabloda isteklerinin ve yanıtlarının gösterilmektedir `http://localhost:1234` önceki kod kullanarak:</span><span class="sxs-lookup"><span data-stu-id="79ef1-192">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="79ef1-193">İstek</span><span class="sxs-lookup"><span data-stu-id="79ef1-193">Request</span></span> | <span data-ttu-id="79ef1-194">Yanıt</span><span class="sxs-lookup"><span data-stu-id="79ef1-194">Response</span></span> |
| --- | --- |
| <span data-ttu-id="79ef1-195">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="79ef1-195">localhost:1234</span></span> | <span data-ttu-id="79ef1-196">Merhaba harita olmayan temsilci gelen.</span><span class="sxs-lookup"><span data-stu-id="79ef1-196">Hello from non-Map delegate.</span></span>  |
| <span data-ttu-id="79ef1-197">localhost:1234 /? şube Yöneticisi =</span><span class="sxs-lookup"><span data-stu-id="79ef1-197">localhost:1234/?branch=master</span></span> | <span data-ttu-id="79ef1-198">Kullanılan şube Yöneticisi =</span><span class="sxs-lookup"><span data-stu-id="79ef1-198">Branch used = master</span></span>|

<span data-ttu-id="79ef1-199">`Map` iç içe, örneğin destekler:</span><span class="sxs-lookup"><span data-stu-id="79ef1-199">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
       level1App.Map("/level2a", level2AApp => {
           // "/level1/level2a"
           //...
       });
       level1App.Map("/level2b", level2BApp => {
           // "/level1/level2b"
           //...
       });
   });
   ```

<span data-ttu-id="79ef1-200">`Map` Ayrıca birden çok parçalı bir kerede örneğin eşleştirebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="79ef1-200">`Map` can also match multiple segments at once, for example:</span></span>

 ```csharp
app.Map("/level1/level2", HandleMultiSeg);
```

## <a name="built-in-middleware"></a><span data-ttu-id="79ef1-201">Yerleşik Ara</span><span class="sxs-lookup"><span data-stu-id="79ef1-201">Built-in middleware</span></span>

<span data-ttu-id="79ef1-202">ASP.NET Core aşağıdaki ara yazılımı bileşenleri yanı sıra ile bunlar eklenmesi gereken sırayı açıklaması gelir:</span><span class="sxs-lookup"><span data-stu-id="79ef1-202">ASP.NET Core ships with the following middleware components, as well as a description of the order in which they should be added:</span></span>

| <span data-ttu-id="79ef1-203">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="79ef1-203">Middleware</span></span> | <span data-ttu-id="79ef1-204">Açıklama</span><span class="sxs-lookup"><span data-stu-id="79ef1-204">Description</span></span> | <span data-ttu-id="79ef1-205">Sırası</span><span class="sxs-lookup"><span data-stu-id="79ef1-205">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="79ef1-206">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="79ef1-206">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="79ef1-207">Kimlik doğrulama desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="79ef1-207">Provides authentication support.</span></span> | <span data-ttu-id="79ef1-208">Önce `HttpContext.User` gereklidir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-208">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="79ef1-209">Terminal OAuth geri aramalar için.</span><span class="sxs-lookup"><span data-stu-id="79ef1-209">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="79ef1-210">CORS</span><span class="sxs-lookup"><span data-stu-id="79ef1-210">CORS</span></span>](xref:security/cors) | <span data-ttu-id="79ef1-211">Çıkış noktaları arası kaynak paylaşımını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="79ef1-211">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="79ef1-212">CORS kullanan bileşenleri önce.</span><span class="sxs-lookup"><span data-stu-id="79ef1-212">Before components that use CORS.</span></span> |
| [<span data-ttu-id="79ef1-213">Tanılama</span><span class="sxs-lookup"><span data-stu-id="79ef1-213">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="79ef1-214">Tanılama yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="79ef1-214">Configures diagnostics.</span></span> | <span data-ttu-id="79ef1-215">Hatalar oluşturur bileşenlerini önce.</span><span class="sxs-lookup"><span data-stu-id="79ef1-215">Before components that generate errors.</span></span> |
| [<span data-ttu-id="79ef1-216">İletilen üstbilgileri/HTTP geçersiz kılmaları</span><span class="sxs-lookup"><span data-stu-id="79ef1-216">Forwarded Headers/HTTP Overrides</span></span>](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | <span data-ttu-id="79ef1-217">Geçerli istek üzerine yönlendirilirken üstbilgileri iletir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-217">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="79ef1-218">Güncelleştirilmiş alanları tüketen bileşenleri önce (örnek: düzeni, ana bilgisayar, ClientIP, yöntem).</span><span class="sxs-lookup"><span data-stu-id="79ef1-218">Before components that consume the updated fields (examples: Scheme, Host, ClientIP, Method).</span></span> |
| [<span data-ttu-id="79ef1-219">HTTPS yeniden yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="79ef1-219">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="79ef1-220">Tüm HTTP isteklerini yeniden yönlendir HTTPS (ASP.NET Core 2.1 veya sonrası).</span><span class="sxs-lookup"><span data-stu-id="79ef1-220">Redirect all HTTP requests to HTTPS (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="79ef1-221">URL tüketen bileşenleri önce.</span><span class="sxs-lookup"><span data-stu-id="79ef1-221">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="79ef1-222">HTTP katı taşıma güvenliği (HSTS)</span><span class="sxs-lookup"><span data-stu-id="79ef1-222">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="79ef1-223">Bir özel yanıt üst bilgisi (ASP.NET Core 2.1 veya sonrası) eklediği güvenlik geliştirme ara yazılımı.</span><span class="sxs-lookup"><span data-stu-id="79ef1-223">Security enhancement middleware that adds a special response header (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="79ef1-224">Yanıtları gönderilmeden önce ve sonra değiştirme isteklerini (örneğin, iletilen üst bilgiler, URL yeniden yazma işlemi) bileşenleri.</span><span class="sxs-lookup"><span data-stu-id="79ef1-224">Before responses are sent and after components that modify requests (for example, Forwarded Headers, URL Rewriting).</span></span> |
| [<span data-ttu-id="79ef1-225">Yanıtları Önbelleğe Alma</span><span class="sxs-lookup"><span data-stu-id="79ef1-225">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="79ef1-226">Yanıt önbelleğe alma işlemi için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="79ef1-226">Provides support for caching responses.</span></span> | <span data-ttu-id="79ef1-227">Önbelleğe alma gerektiren bileşenler önce.</span><span class="sxs-lookup"><span data-stu-id="79ef1-227">Before components that require caching.</span></span> |
| [<span data-ttu-id="79ef1-228">Yanıt sıkıştırma</span><span class="sxs-lookup"><span data-stu-id="79ef1-228">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="79ef1-229">Yanıtları sıkıştırma için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="79ef1-229">Provides support for compressing responses.</span></span> | <span data-ttu-id="79ef1-230">Sıkıştırma iste bileşenleri önce.</span><span class="sxs-lookup"><span data-stu-id="79ef1-230">Before components that require compression.</span></span> |
| [<span data-ttu-id="79ef1-231">İstek yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="79ef1-231">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="79ef1-232">Yerelleştirme desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="79ef1-232">Provides localization support.</span></span> | <span data-ttu-id="79ef1-233">Yerelleştirme önce hassas bileşenleri.</span><span class="sxs-lookup"><span data-stu-id="79ef1-233">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="79ef1-234">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="79ef1-234">Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="79ef1-235">Tanımlar ve istek yolları kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="79ef1-235">Defines and constrains request routes.</span></span> | <span data-ttu-id="79ef1-236">Yollar eşleştirmek için terminal.</span><span class="sxs-lookup"><span data-stu-id="79ef1-236">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="79ef1-237">Oturum</span><span class="sxs-lookup"><span data-stu-id="79ef1-237">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="79ef1-238">Kullanıcı oturumlarını yönetmek için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="79ef1-238">Provides support for managing user sessions.</span></span> | <span data-ttu-id="79ef1-239">Oturum gerektiren bileşenler önce.</span><span class="sxs-lookup"><span data-stu-id="79ef1-239">Before components that require Session.</span></span> |
| [<span data-ttu-id="79ef1-240">Statik dosyalar</span><span class="sxs-lookup"><span data-stu-id="79ef1-240">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="79ef1-241">Statik dosya ve Dizin tarama hizmet vermek için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="79ef1-241">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="79ef1-242">Bir isteği dosyaları eşleşirse terminal.</span><span class="sxs-lookup"><span data-stu-id="79ef1-242">Terminal if a request matches files.</span></span> |
| [<span data-ttu-id="79ef1-243">URL yeniden yazma işlemi</span><span class="sxs-lookup"><span data-stu-id="79ef1-243">URL Rewriting</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="79ef1-244">URL yeniden yazma işlemi ve istekleri yönlendirme için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="79ef1-244">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="79ef1-245">URL tüketen bileşenleri önce.</span><span class="sxs-lookup"><span data-stu-id="79ef1-245">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="79ef1-246">WebSockets</span><span class="sxs-lookup"><span data-stu-id="79ef1-246">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="79ef1-247">WebSockets Protokolü sağlar.</span><span class="sxs-lookup"><span data-stu-id="79ef1-247">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="79ef1-248">WebSocket isteklerini kabul etmek için gerekli bileşenleri önce.</span><span class="sxs-lookup"><span data-stu-id="79ef1-248">Before components that are required to accept WebSocket requests.</span></span> |

<a name="middleware-writing-middleware"></a>

## <a name="writing-middleware"></a><span data-ttu-id="79ef1-249">Yazma Ara</span><span class="sxs-lookup"><span data-stu-id="79ef1-249">Writing middleware</span></span>

<span data-ttu-id="79ef1-250">Ara yazılım genellikle bir sınıfta kapsüllenmiş ve bir genişletme yöntemi ile gösteriliyor.</span><span class="sxs-lookup"><span data-stu-id="79ef1-250">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="79ef1-251">Kültür geçerli istek için Sorgu dizesinden ayarlar aşağıdaki Ara göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="79ef1-251">Consider the following middleware, which sets the culture for the current request from the query string:</span></span>

[!code-csharp[](index/sample/Culture/StartupCulture.cs?name=snippet1)]

<span data-ttu-id="79ef1-252">Not: Yukarıdaki örnek kod, bir ara yazılım bileşeni oluşturma göstermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79ef1-252">Note: The sample code above is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="79ef1-253">Bkz: [ Genelleştirme ve Yerelleştirme](xref:fundamentals/localization) ASP.NET Core'nın yerleşik yerelleştirme desteği.</span><span class="sxs-lookup"><span data-stu-id="79ef1-253">See [ Globalization and localization](xref:fundamentals/localization) for ASP.NET Core's built-in localization support.</span></span>

<span data-ttu-id="79ef1-254">Kültürün, örneğin geçirerek ara yazılım sınayabilirsiniz `http://localhost:7997/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="79ef1-254">You can test the middleware by passing in the culture, for example `http://localhost:7997/?culture=no`.</span></span>

<span data-ttu-id="79ef1-255">Aşağıdaki kod bir sınıfa ara yazılım temsilci taşır:</span><span class="sxs-lookup"><span data-stu-id="79ef1-255">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](index/sample/Culture/RequestCultureMiddleware.cs)]

> [!NOTE]
> <span data-ttu-id="79ef1-256">ASP.NET Core içinde 1.x, ara yazılım `Task` yöntemin adı olmalı `Invoke`.</span><span class="sxs-lookup"><span data-stu-id="79ef1-256">In ASP.NET Core 1.x, the middleware `Task` method's name must be `Invoke`.</span></span> <span data-ttu-id="79ef1-257">ASP.NET Core 2.0 veya sonraki sürümlerde, adı ya da olabilir `Invoke` veya `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="79ef1-257">In ASP.NET Core 2.0 or later, the name can be either `Invoke` or `InvokeAsync`.</span></span>

<span data-ttu-id="79ef1-258">Ara yazılım aracılığıyla aşağıdaki uzantısı yöntemi gösterir [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder):</span><span class="sxs-lookup"><span data-stu-id="79ef1-258">The following extension method exposes the middleware through [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder):</span></span>

[!code-csharp[](index/sample/Culture/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="79ef1-259">Aşağıdaki kod Ara çağırır `Configure`:</span><span class="sxs-lookup"><span data-stu-id="79ef1-259">The following code calls the middleware from `Configure`:</span></span>

[!code-csharp[](index/sample/Culture/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="79ef1-260">Ara yazılım izlemelidir [açık bağımlılıkları ilkesine](http://deviq.com/explicit-dependencies-principle/) bağımlılıklarını kendi oluşturucusuna gösterme tarafından.</span><span class="sxs-lookup"><span data-stu-id="79ef1-260">Middleware should follow the [Explicit Dependencies Principle](http://deviq.com/explicit-dependencies-principle/) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="79ef1-261">Ara yazılım yapılandırılmıştır kez başına *uygulama ömrü*.</span><span class="sxs-lookup"><span data-stu-id="79ef1-261">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="79ef1-262">Bkz: *istek başına bağımlılıkları* üstündeyse ara yazılım istek içinde Hizmetleri paylaşmasına gerekir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-262">See *Per-request dependencies* below if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="79ef1-263">Ara yazılımı bileşenleri bağımlılıklarını bağımlılık ekleme Oluşturucu parametreleri üzerinden gelen çözebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="79ef1-263">Middleware components can resolve their dependencies from dependency injection through constructor parameters.</span></span> <span data-ttu-id="79ef1-264">[`UseMiddleware<T>`](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary) Ayrıca ek parametreler doğrudan kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-264">[`UseMiddleware<T>`](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary) can also accept additional parameters directly.</span></span>

### <a name="per-request-dependencies"></a><span data-ttu-id="79ef1-265">İstek başına bağımlılıkları</span><span class="sxs-lookup"><span data-stu-id="79ef1-265">Per-request dependencies</span></span>

<span data-ttu-id="79ef1-266">Ara yazılım değil istek başına, uygulama başlatma sırasında oluşturulur çünkü *kapsamlı* ara yazılım Oluşturucu tarafından kullanılan yaşam süresi olmayan paylaşılan hizmetler diğer bağımlılık tarafından eklenen türleriyle her isteği sırasında.</span><span class="sxs-lookup"><span data-stu-id="79ef1-266">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors are not  shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="79ef1-267">Gereken paylaşıyorsanız bir *kapsamlı* , Ara ve diğer türleri arasında hizmet, bu hizmetlere ekleme `Invoke` yöntemin imzası.</span><span class="sxs-lookup"><span data-stu-id="79ef1-267">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="79ef1-268">`Invoke` Yöntemi tarafından bağımlılık ekleme doldurulur ek parametreleri kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="79ef1-268">The `Invoke` method can accept additional parameters that are populated by dependency injection.</span></span> <span data-ttu-id="79ef1-269">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="79ef1-269">For example:</span></span>

```csharp
public class MyMiddleware
{
    private readonly RequestDelegate _next;

    public MyMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="79ef1-270">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="79ef1-270">Additional resources</span></span>

* [<span data-ttu-id="79ef1-271">HTTP modülleri Ara geçirme</span><span class="sxs-lookup"><span data-stu-id="79ef1-271">Migrate HTTP Modules to Middleware</span></span>](xref:migration/http-modules)
* [<span data-ttu-id="79ef1-272">Uygulama Başlatma</span><span class="sxs-lookup"><span data-stu-id="79ef1-272">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="79ef1-273">İstek Özellikleri</span><span class="sxs-lookup"><span data-stu-id="79ef1-273">Request Features</span></span>](xref:fundamentals/request-features)
* [<span data-ttu-id="79ef1-274">Ara yazılımı Fabrika tabanlı etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="79ef1-274">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="79ef1-275">Bir üçüncü taraf kapsayıcısı ile Ara yazılım etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="79ef1-275">Middleware activation with a third-party container</span></span>](xref:fundamentals/middleware/extensibility-third-party-container)
