---
title: Bellek içi ASP.NET Core, önbelleğe alma
author: rick-anderson
description: ASP.NET Core bellekte önbelleğe öğrenin.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 12/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: performance/caching/memory
ms.openlocfilehash: eca6610caf4e0a654c9a31f89a42e2ac82e94d23
ms.sourcegitcommit: 726ffab258070b4fe6cf950bf030ce10c0c07bb4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34734490"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="22d0f-103">Bellek içi ASP.NET Core, önbelleğe alma</span><span class="sxs-lookup"><span data-stu-id="22d0f-103">Cache in-memory in ASP.NET Core</span></span>

<span data-ttu-id="22d0f-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="22d0f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="22d0f-105">[Görüntülemek veya karşıdan örnek kod](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="22d0f-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="22d0f-106">Önbelleğe alma temelleri</span><span class="sxs-lookup"><span data-stu-id="22d0f-106">Caching basics</span></span>

<span data-ttu-id="22d0f-107">Önbelleğe alma önemli ölçüde performans ve ölçeklenebilirlik, bir uygulamanın içeriği oluşturmak için gereken iş azaltarak artırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="22d0f-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="22d0f-108">Seyrek değişen verileri ile en iyi önbelleğe alma çalışır.</span><span class="sxs-lookup"><span data-stu-id="22d0f-108">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="22d0f-109">Önbelleğe alma yapar çok döndürülen verilerin bir kopyasını özgün kaynağından hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="22d0f-109">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="22d0f-110">Yazma ve hiçbir zaman önbelleğe alınmış verileri bağımlı uygulamanızı test etme gerekir.</span><span class="sxs-lookup"><span data-stu-id="22d0f-110">You should write and test your app to never depend on cached data.</span></span>

<span data-ttu-id="22d0f-111">ASP.NET Core birkaç farklı önbellek destekler.</span><span class="sxs-lookup"><span data-stu-id="22d0f-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="22d0f-112">En basit önbellek dayanır [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), web sunucusu bellekte bir önbellek temsil eder.</span><span class="sxs-lookup"><span data-stu-id="22d0f-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="22d0f-113">Bir sunucu grubunda birden çok sunucu çalışan uygulamaları oturumları bellek içi önbellek kullanırken Yapışkan emin olun.</span><span class="sxs-lookup"><span data-stu-id="22d0f-113">Apps which run on a server farm of multiple servers should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="22d0f-114">Yapışkan oturumları tüm istemciden gelen sonraki istekleri aynı sunucuya gidin emin olun.</span><span class="sxs-lookup"><span data-stu-id="22d0f-114">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="22d0f-115">Örneğin, Azure Web apps kullanımı [uygulama isteği yönlendirme](https://www.iis.net/learn/extensions/planning-for-arr) tüm istekler aynı sunucuya yönlendirmek için (ARR).</span><span class="sxs-lookup"><span data-stu-id="22d0f-115">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="22d0f-116">Bir web grubunda olmayan Yapışkan oturumları gerektiren bir [dağıtılmış önbellek](distributed.md) önbellek tutarlılık sorunları önlemek için.</span><span class="sxs-lookup"><span data-stu-id="22d0f-116">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="22d0f-117">Bazı uygulamalar için bir bellek içi önbellek daha yüksek ölçek genişletme dağıtılmış önbellek destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="22d0f-117">For some apps, a distributed cache can support higher scale out than an in-memory cache.</span></span> <span data-ttu-id="22d0f-118">Dağıtılmış önbellek kullanarak bir dış işlem için önbelleği boşaltır.</span><span class="sxs-lookup"><span data-stu-id="22d0f-118">Using a distributed cache offloads the cache memory to an external process.</span></span>

<span data-ttu-id="22d0f-119">`IMemoryCache` Sürece, önbelleği önbellek girişlerinin bellek baskısı altında Tahliye [önbelleğe öncelik](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) ayarlanır `CacheItemPriority.NeverRemove`.</span><span class="sxs-lookup"><span data-stu-id="22d0f-119">The `IMemoryCache` cache will evict cache entries under memory pressure unless the [cache priority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) is set to `CacheItemPriority.NeverRemove`.</span></span> <span data-ttu-id="22d0f-120">Ayarlayabileceğiniz `CacheItemPriority` önbellek çıkarır öğeleri bellek baskısı altında önceliğini ayarlamak için.</span><span class="sxs-lookup"><span data-stu-id="22d0f-120">You can set the `CacheItemPriority` to adjust the priority with which the cache evicts items under memory pressure.</span></span>

<span data-ttu-id="22d0f-121">Bellek içi önbellek herhangi bir nesne depolayabilir; Dağıtılmış önbellek arabirimi sınırlıdır `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="22d0f-121">The in-memory cache can store any object; the distributed cache interface is limited to `byte[]`.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="22d0f-122">IMemoryCache kullanma</span><span class="sxs-lookup"><span data-stu-id="22d0f-122">Using IMemoryCache</span></span>

<span data-ttu-id="22d0f-123">Bellek içi önbelleğe alma bir *hizmet* , uygulamayı kullanarak başvurulan [bağımlılık ekleme](../../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="22d0f-123">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="22d0f-124">Çağrı `AddMemoryCache` içinde `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="22d0f-124">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="22d0f-125">İstek `IMemoryCache` oluşturucuda örneği:</span><span class="sxs-lookup"><span data-stu-id="22d0f-125">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="22d0f-126">`IMemoryCache` NuGet paketi gerekir [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).</span><span class="sxs-lookup"><span data-stu-id="22d0f-126">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="22d0f-127">`IMemoryCache` NuGet paketi gerekir [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), hub içinde olduğu [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="22d0f-127">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is avaiable in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="22d0f-128">`IMemoryCache` NuGet paketi gerekir [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), hub içinde olduğu [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="22d0f-128">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is avaiable in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="22d0f-129">Aşağıdaki kod [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) birer önbellekte olup olmadığını denetlemek için.</span><span class="sxs-lookup"><span data-stu-id="22d0f-129">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="22d0f-130">Bir süre önbelleğe değil, yeni bir girdi oluşturulur ve önbellek ile eklenen [ayarlamak](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span><span class="sxs-lookup"><span data-stu-id="22d0f-130">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="22d0f-131">Geçerli saati ve önbelleğe alınan saat görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="22d0f-131">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="22d0f-132">Önbelleğe alınan `DateTime` değeri, zaman aşımı süresi (ve bellek baskısı nedeniyle hiçbir çıkarma) içinde istekler varken önbellekte kalır.</span><span class="sxs-lookup"><span data-stu-id="22d0f-132">The cached `DateTime` value remains in the cache while there are requests within the timeout period (and no eviction due to memory pressure).</span></span> <span data-ttu-id="22d0f-133">Aşağıdaki resimde, geçerli saati ve önbellekten daha eski bir zaman gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="22d0f-133">The following image shows the current time and an older time retrieved from the cache:</span></span>

![Dizin görünümünün görüntülenen iki farklı saatleri](memory/_static/time.png)

<span data-ttu-id="22d0f-135">Aşağıdaki kod [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) ve [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) verileri önbelleğe.</span><span class="sxs-lookup"><span data-stu-id="22d0f-135">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span> 

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="22d0f-136">Aşağıdaki kod çağrıları [almak](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) önbelleğe alınmış zaman getirmek için:</span><span class="sxs-lookup"><span data-stu-id="22d0f-136">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="22d0f-137">Bkz: [IMemoryCache yöntemleri](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) ve [CacheExtensions yöntemleri](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) önbellek yöntemleri açıklaması.</span><span class="sxs-lookup"><span data-stu-id="22d0f-137">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of the cache methods.</span></span>

## <a name="using-memorycacheentryoptions"></a><span data-ttu-id="22d0f-138">MemoryCacheEntryOptions kullanma</span><span class="sxs-lookup"><span data-stu-id="22d0f-138">Using MemoryCacheEntryOptions</span></span>

<span data-ttu-id="22d0f-139">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="22d0f-139">The following sample:</span></span>

- <span data-ttu-id="22d0f-140">Mutlak sona erme zamanı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="22d0f-140">Sets the absolute expiration time.</span></span> <span data-ttu-id="22d0f-141">Bu giriş önbelleğe alınacak en fazla süreyi ve öğe kayan zaman aşımı sürekli olarak yenilendiğinde çok eski hale gelmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="22d0f-141">This is the maximum time the entry can be cached and prevents the item from becoming too stale when the sliding expiration is continuously renewed.</span></span>
- <span data-ttu-id="22d0f-142">Kayan süre sonu zamanı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="22d0f-142">Sets a sliding expiration time.</span></span> <span data-ttu-id="22d0f-143">Bu önbelleğe alınan öğe erişim istekleri kayan sona erme saati sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="22d0f-143">Requests that access this cached item will reset the sliding expiration clock.</span></span>
- <span data-ttu-id="22d0f-144">Önbellek önceliği ayarlar `CacheItemPriority.NeverRemove`.</span><span class="sxs-lookup"><span data-stu-id="22d0f-144">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
- <span data-ttu-id="22d0f-145">Ayarlar bir [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) , çağrılır giriş önbellekten çıkarılmasına sonra.</span><span class="sxs-lookup"><span data-stu-id="22d0f-145">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="22d0f-146">Öğeyi önbellekten kaldırır kodundan farklı bir iş parçacığı üzerinde geri arama çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="22d0f-146">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="cache-dependencies"></a><span data-ttu-id="22d0f-147">Önbellek bağımlılıkları</span><span class="sxs-lookup"><span data-stu-id="22d0f-147">Cache dependencies</span></span>

<span data-ttu-id="22d0f-148">Aşağıdaki örnek, bağımlı giriş süresi dolarsa önbellek girişinin süresi dolacak şekilde gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="22d0f-148">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="22d0f-149">A `CancellationChangeToken` önbelleğe alınmış öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="22d0f-149">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="22d0f-150">Zaman `Cancel` üzerinde adlı `CancellationTokenSource`, her iki önbellek girişlerinin çıkarılacak.</span><span class="sxs-lookup"><span data-stu-id="22d0f-150">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="22d0f-151">Kullanarak bir `CancellationTokenSource` grup olarak çıkarılacak birden fazla önbellek girişlerinin sağlar.</span><span class="sxs-lookup"><span data-stu-id="22d0f-151">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="22d0f-152">İle `using` önbellek girişlerinin Yukarıdaki kod modelinde oluşturulan içinde `using` blok tetikleyiciler ve sona erme ayarları devralır.</span><span class="sxs-lookup"><span data-stu-id="22d0f-152">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="22d0f-153">Ek Notlar</span><span class="sxs-lookup"><span data-stu-id="22d0f-153">Additional notes</span></span>

- <span data-ttu-id="22d0f-154">Bir önbellek öğesi yeniden doldurmak için bir geri çağırma kullanırken:</span><span class="sxs-lookup"><span data-stu-id="22d0f-154">When using a callback to repopulate a cache item:</span></span>

  - <span data-ttu-id="22d0f-155">Geri çağırma tamamlanmadığından kurmadı birden çok istek önbelleğe alınan anahtar değeri boş bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="22d0f-155">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  - <span data-ttu-id="22d0f-156">Bu, önbelleğe alınan öğe yeniden birkaç iş parçacığı neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="22d0f-156">This can result in several threads repopulating the cached item.</span></span>

- <span data-ttu-id="22d0f-157">Başka bir oluşturmak için bir önbellek girişi kullanıldığında, alt üst girişin sona erme belirteçleri ve zaman tabanlı sona erme ayarları kopyalar.</span><span class="sxs-lookup"><span data-stu-id="22d0f-157">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="22d0f-158">Alt tarafından el ile temizleme süresi dolmuş ya da üst girişinin güncelleştirme değil.</span><span class="sxs-lookup"><span data-stu-id="22d0f-158">The child isn't expired by manual removal or updating of the parent entry.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="22d0f-159">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="22d0f-159">Additional resources</span></span>

* [<span data-ttu-id="22d0f-160">Dağıtılmış önbellekle çalışma</span><span class="sxs-lookup"><span data-stu-id="22d0f-160">Work with a distributed cache</span></span>](xref:performance/caching/distributed)
* [<span data-ttu-id="22d0f-161">Değişiklik belirteçleri değişikliklerle Algıla</span><span class="sxs-lookup"><span data-stu-id="22d0f-161">Detect changes with change tokens</span></span>](xref:fundamentals/primitives/change-tokens)
* [<span data-ttu-id="22d0f-162">Yanıtları önbelleğe alma</span><span class="sxs-lookup"><span data-stu-id="22d0f-162">Response caching</span></span>](xref:performance/caching/response)
* [<span data-ttu-id="22d0f-163">Yanıtları Önbelleğe Alma Ara Yazılımı</span><span class="sxs-lookup"><span data-stu-id="22d0f-163">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
* [<span data-ttu-id="22d0f-164">Önbellek Etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="22d0f-164">Cache Tag Helper</span></span>](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)
* [<span data-ttu-id="22d0f-165">Dağıtılmış Önbellek Etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="22d0f-165">Distributed Cache Tag Helper</span></span>](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)
