---
title: Bir üçüncü taraf kapsayıcısında ASP.NET Core ile Ara yazılım etkinleştirme
author: guardrex
description: Kesin türü belirtilmiş bir ara yazılım ile Fabrika tabanlı etkinleştirme ve üçüncü taraf kapsayıcı içinde ASP.NET Core kullanmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2018
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 6af775c66a1de7f1a4f06a4a639ade20c6493b2a
ms.sourcegitcommit: 375e9a67f5e1f7b0faaa056b4b46294cc70f55b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50206815"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="83d88-103">Bir üçüncü taraf kapsayıcısında ASP.NET Core ile Ara yazılım etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="83d88-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

<span data-ttu-id="83d88-104">Tarafından [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="83d88-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="83d88-105">Bu makalede nasıl yapılacağı açıklanır [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) ve [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) için genişletilebilirlik noktası olarak [ara yazılım](xref:fundamentals/middleware/index) bir üçüncü taraf kapsayıcı ile etkinleştirme.</span><span class="sxs-lookup"><span data-stu-id="83d88-105">This article demonstrates how to use [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) and [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="83d88-106">Hakkında tanıtıcı bilgi `IMiddlewareFactory` ve `IMiddleware`, bkz: [Fabrika tabanlı ara yazılım etkinleştirme](xref:fundamentals/middleware/extensibility) konu.</span><span class="sxs-lookup"><span data-stu-id="83d88-106">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see the [Factory-based middleware activation](xref:fundamentals/middleware/extensibility) topic.</span></span>

<span data-ttu-id="83d88-107">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/sample) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="83d88-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="83d88-108">Örnek uygulama tarafından ara yazılım etkinleştirme gösterir bir `IMiddlewareFactory` uygulaması `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="83d88-108">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="83d88-109">Örnek kullanır [basit Enjektörü](https://simpleinjector.org) bağımlılık ekleme (dı) kapsayıcı.</span><span class="sxs-lookup"><span data-stu-id="83d88-109">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="83d88-110">Bir sorgu dizesi parametresi tarafından sağlanan değer örnek'ın Ara yazılım uygulama kayıtları (`key`).</span><span class="sxs-lookup"><span data-stu-id="83d88-110">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="83d88-111">Ara yazılım, sorgu dizesi değerini bir bellek içi veritabanına kaydetmek için bir eklenen veritabanı bağlamı (kapsamlı bir hizmet) kullanır.</span><span class="sxs-lookup"><span data-stu-id="83d88-111">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="83d88-112">Örnek uygulama kullandığı [basit Enjektörü](https://github.com/simpleinjector/SimpleInjector) yalnızca tanıtım amacıyla.</span><span class="sxs-lookup"><span data-stu-id="83d88-112">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="83d88-113">Kullanımı basit Enjektörü bir onay yok.</span><span class="sxs-lookup"><span data-stu-id="83d88-113">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="83d88-114">Ara yazılım etkinleştirme yaklaşım basit Enjektörü belgelerinde açıklanan ve GitHub sorunları, basit Enjektörü maintainers tarafından önerilir.</span><span class="sxs-lookup"><span data-stu-id="83d88-114">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="83d88-115">Daha fazla bilgi için [basit Enjektörü belgeleri](https://simpleinjector.readthedocs.io/en/latest/index.html) ve [basit Enjektörü GitHub deposu](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="83d88-115">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="83d88-116">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="83d88-116">IMiddlewareFactory</span></span>

<span data-ttu-id="83d88-117">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) ara yazılım oluşturmak için yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="83d88-117">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) provides methods to create middleware.</span></span>

<span data-ttu-id="83d88-118">Örnek uygulamada, bir ara yazılım fabrikası oluşturmak için uygulanan bir `SimpleInjectorActivatedMiddleware` örneği.</span><span class="sxs-lookup"><span data-stu-id="83d88-118">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="83d88-119">Ara yazılım Fabrika, ara yazılım çözümlemek için basit Enjektörü kapsayıcı kullanır:</span><span class="sxs-lookup"><span data-stu-id="83d88-119">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="83d88-120">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="83d88-120">IMiddleware</span></span>

<span data-ttu-id="83d88-121">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) ara yazılımı için uygulamanın istek ardışık düzenini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="83d88-121">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="83d88-122">Ara yazılım etkinleştirilmiş olarak bir `IMiddlewareFactory` uygulaması (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="83d88-122">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="83d88-123">Uzantı ara yazılımı için oluşturulan (*Middleware/MiddlewareExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="83d88-123">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="83d88-124">`Startup.ConfigureServices` çeşitli görevleri gerçekleştirmeniz gerekir:</span><span class="sxs-lookup"><span data-stu-id="83d88-124">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="83d88-125">Basit Enjektörü kapsayıcısı ayarlamak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="83d88-125">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="83d88-126">Ara yazılım ve üretici kaydedin.</span><span class="sxs-lookup"><span data-stu-id="83d88-126">Register the factory and middleware.</span></span>
* <span data-ttu-id="83d88-127">Uygulamanın veritabanı bağlamı kullanılabilir basit Enjektörü kapsayıcısından için bir Razor sayfası olun.</span><span class="sxs-lookup"><span data-stu-id="83d88-127">Make the app's database context available from the Simple Injector container for a Razor Page.</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="83d88-128">Ara yazılım istek işleme ardışık düzeninde kayıtlı `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="83d88-128">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet2&highlight=13)]

## <a name="additional-resources"></a><span data-ttu-id="83d88-129">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="83d88-129">Additional resources</span></span>

* [<span data-ttu-id="83d88-130">Ara Yazılım</span><span class="sxs-lookup"><span data-stu-id="83d88-130">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="83d88-131">Fabrika tabanlı ara yazılım etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="83d88-131">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="83d88-132">Basit Enjektörü GitHub deposu</span><span class="sxs-lookup"><span data-stu-id="83d88-132">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="83d88-133">Basit Enjektörü belgeleri</span><span class="sxs-lookup"><span data-stu-id="83d88-133">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
