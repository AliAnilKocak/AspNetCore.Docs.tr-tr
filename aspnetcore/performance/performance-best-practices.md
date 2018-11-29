---
title: ASP.NET Core performansı en iyi uygulamalar
author: mjrousos
description: Genel performans sorunlarını önleme ve ASP.NET Core uygulamaları performansını artırmak için ipuçları.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 11/29/2018
uid: performance/performance-best-practices
ms.openlocfilehash: 9f3ed97bf4d4eb371ff5ae3874234b44745cc4ca
ms.sourcegitcommit: 0fc89b80bb1952852ecbcf3c5c156459b02a6ceb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52618122"
---
# <a name="aspnet-core-performance-best-practices"></a><span data-ttu-id="3d8b2-103">ASP.NET Core performansı en iyi uygulamalar</span><span class="sxs-lookup"><span data-stu-id="3d8b2-103">ASP.NET Core Performance Best Practices</span></span>

<span data-ttu-id="3d8b2-104">Tarafından [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="3d8b2-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="3d8b2-105">Bu konu, ASP.NET Core ile en iyi performans için yönergeler sağlar.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-105">This topic provides guidelines for performance best practices with ASP.NET Core.</span></span>

<a name="hot"></a>
<span data-ttu-id="3d8b2-106"><!-- TODO review hot code paths is jargon that won't MT (machine translate) and is not well defined for native speakers. --> Bu belgede, sık erişimli kod yolu sık çağrılır ve yürütme süresi çoğunu oluştuğu bir kod yolu olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-106"><!-- TODO review hot code paths is jargon that won't MT (machine translate) and is not well defined for native speakers. --> In this document, a hot code path is defined as a code path that is frequently called and where much of the execution time occurs.</span></span> <span data-ttu-id="3d8b2-107">Sık erişimli kod yollarını genellikle uygulama ölçeklendirme ve performans sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-107">Hot code paths typically limit app scale-out and performance.</span></span>

## <a name="cache-aggressively"></a><span data-ttu-id="3d8b2-108">Agresif bir biçimde önbelleğe alma</span><span class="sxs-lookup"><span data-stu-id="3d8b2-108">Cache aggressively</span></span>

<span data-ttu-id="3d8b2-109">Önbelleğe alma, bu belgenin birden fazla bölümde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-109">Caching is discussed in several parts of this document.</span></span> <span data-ttu-id="3d8b2-110">Daha fazla bilgi için bkz. <xref:performance/caching/response>.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-110">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="avoid-blocking-calls"></a><span data-ttu-id="3d8b2-111">Çağrıları engellemekten kaçınacak</span><span class="sxs-lookup"><span data-stu-id="3d8b2-111">Avoid blocking calls</span></span>

<span data-ttu-id="3d8b2-112">ASP.NET Core uygulamaları aynı anda birçok istekleri işlemek için tasarlanmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-112">ASP.NET Core apps should be designed to process many requests simultaneously.</span></span> <span data-ttu-id="3d8b2-113">Zaman uyumsuz API'leri çağrıları engellemeyi beklenmiyor tarafından binlerce eş zamanlı istekleri işlemek için iş parçacığı oluşan küçük bir havuz sağlar.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-113">Asynchronous APIs allow a small pool of threads to handle thousands of concurrent requests by not waiting on blocking calls.</span></span> <span data-ttu-id="3d8b2-114">Tamamlanması uzun süre çalışan zaman uyumlu görevde beklemek yerine, iş parçacığı başka bir istek üzerinde çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-114">Rather than waiting on a long-running synchronous task to complete, the thread can work on another request.</span></span>

<span data-ttu-id="3d8b2-115">ASP.NET Core uygulamalarında ortak bir performans sorunu, zaman uyumsuz çağrılar engelliyor.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-115">A common performance problem in ASP.NET Core apps is blocking calls that could be asynchronous.</span></span> <span data-ttu-id="3d8b2-116">Birçok eş zamanlı engelleme çağrı müşteri adayları [iş parçacığı havuzu starvation](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) ve yanıt sürelerini önemli.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-116">Many synchronous blocking calls leads to [Thread Pool starvation](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) and degrading response times.</span></span>

<span data-ttu-id="3d8b2-117">**Sağlamadığı**:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-117">**Do not**:</span></span>

* <span data-ttu-id="3d8b2-118">Zaman uyumsuz yürütme engelleme çağırarak [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) veya [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-118">Block asynchronous execution by calling [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) or [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).</span></span>
* <span data-ttu-id="3d8b2-119">Ortak kod yollarını kilitler edinin.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-119">Acquire locks in common code paths.</span></span> <span data-ttu-id="3d8b2-120">ASP.NET Core, çoğu kod paralel olarak çalıştırmak için tasarlanmış, yüksek performanslı uygulamalardır.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-120">ASP.NET Core apps are most performant when architected to run code in parallel.</span></span>

<span data-ttu-id="3d8b2-121">**Yapmak**:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-121">**Do**:</span></span>

* <span data-ttu-id="3d8b2-122">Olun [sık erişimliye kod yollarını](#hot) zaman uyumsuz.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-122">Make [hot code paths](#hot) asynchronous.</span></span>
* <span data-ttu-id="3d8b2-123">Veri erişimi ve uzun süre çalışan işlemleri API zaman uyumsuz olarak çağırın.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-123">Call data access and long-running operations APIs asynchronously.</span></span>
* <span data-ttu-id="3d8b2-124">Denetleyici/Razor sayfa eylemleri zaman uyumsuz olarak yapın.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-124">Make controller/Razor Page actions asynchronous.</span></span> <span data-ttu-id="3d8b2-125">Bütün çağrı yığını sayesinde bir avantaj elde için zaman uyumsuz olması gereken [async/await](/dotnet/csharp/programming-guide/concepts/async/) desenleri.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-125">The entire call stack needs to be asynchronous in order to benefit from [async/await](/dotnet/csharp/programming-guide/concepts/async/) patterns.</span></span>

<span data-ttu-id="3d8b2-126">Bir profil oluşturucu ister [PerfView](https://github.com/Microsoft/perfview) sık eklenen iş parçacıkları aramak için kullanılan [iş parçacığı havuzu](/windows/desktop/procthread/thread-pool).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-126">A profiler like [PerfView](https://github.com/Microsoft/perfview) can be used to look for threads frequently being added to the [Thread Pool](/windows/desktop/procthread/thread-pool).</span></span> <span data-ttu-id="3d8b2-127">`Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` Olay iş parçacığı havuzuna eklenen bir iş parçacığı gösterir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-127">The `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` event indicates a thread being added to the thread pool.</span></span> <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc  -->

## <a name="minimize-large-object-allocations"></a><span data-ttu-id="3d8b2-128">Büyük nesne ayırma simge durumuna küçült</span><span class="sxs-lookup"><span data-stu-id="3d8b2-128">Minimize large object allocations</span></span>

<span data-ttu-id="3d8b2-129"><!-- TODO review Bill - replaced original .NET language below with .NET Core since this targets .NET Core --> [.NET Core çöp toplayıcı](/dotnet/standard/garbage-collection/) ayırma ve serbest bırakma bellek ASP.NET Core uygulamaları otomatik olarak yönetir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-129"><!-- TODO review Bill - replaced original .NET language below with .NET Core since this targets .NET Core --> The [.NET Core garbage collector](/dotnet/standard/garbage-collection/) manages allocation and release of memory automatically in ASP.NET Core apps.</span></span> <span data-ttu-id="3d8b2-130">Otomatik çöp toplama genellikle geliştiriciler nasıl veya ne zaman bellek serbest bırakılır hakkında endişelenmeniz gerekmez anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-130">Automatic garbage collection generally means that developers don't need to worry about how or when memory is freed.</span></span> <span data-ttu-id="3d8b2-131">Böylece geliştiriciler ayırma nesneler en aza indirmeniz gerekir ancak başvurulmayan nesnelerin temizlenmesi CPU süresi alan [sık erişimliye kod yollarını](#hot).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-131">However, cleaning up unreferenced objects takes CPU time, so developers should minimize allocating objects in [hot code paths](#hot).</span></span> <span data-ttu-id="3d8b2-132">Çöp toplama, özellikle büyük nesneler (> 85 K bayt) pahalıdır.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-132">Garbage collection is especially expensive on large objects (> 85 K bytes).</span></span> <span data-ttu-id="3d8b2-133">Büyük nesneler üzerinde depolanan [büyük nesne yığını](/dotnet/standard/garbage-collection/large-object-heap) ve (2. nesil) tam çöp toplama temizlemek için.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-133">Large objects are stored on the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) and require a full (generation 2) garbage collection to clean up.</span></span> <span data-ttu-id="3d8b2-134">Nesil 0 ve 1. nesil koleksiyonlar farklı olarak, uygulamanın yürütülmesini geçici olarak askıya alınması için 2. nesil koleksiyonu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-134">Unlike generation 0 and generation 1 collections, a generation 2 collection requires app execution to be temporarily suspended.</span></span> <span data-ttu-id="3d8b2-135">Sık ayırmayı ve ayırmayı kaldırma büyük nesnelerin yetersiz performansa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-135">Frequent allocation and de-allocation of large objects can cause inconsistent performance.</span></span>

<span data-ttu-id="3d8b2-136">Öneriler:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-136">Recommendations:</span></span>

* <span data-ttu-id="3d8b2-137">**Yapmak** sık kullanılan büyük nesneleri önbelleğe almayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-137">**Do** consider caching large objects that are frequently used.</span></span> <span data-ttu-id="3d8b2-138">Büyük nesnelerin önbelleğe alma, pahalı ayırmaları engeller.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-138">Caching large objects prevents expensive allocations.</span></span>
* <span data-ttu-id="3d8b2-139">**Yapmak** kullanarak arabellek havuzu bir [ `ArrayPool<T>` ](/dotnet/api/system.buffers.arraypool-1) büyük dizileri depolamak için.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-139">**Do** pool buffers by using an [`ArrayPool<T>`](/dotnet/api/system.buffers.arraypool-1) to store large arrays.</span></span>
* <span data-ttu-id="3d8b2-140">**Sağlamadığı** birçok, kısa süreli büyük nesneler şirket ayrılamadı [sık erişimliye kod yollarını](#hot).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-140">**Do not** allocate many, short-lived large objects on [hot code paths](#hot).</span></span>

<span data-ttu-id="3d8b2-141">Önceki çöp toplama (GC) istatistikleri de gözden geçirerek koydu gibi bellek sorunlarını [PerfView](https://github.com/Microsoft/perfview) inceleyerek:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-141">Memory issues like the preceding can be diagnosed by reviewing garbage collection (GC) stats in [PerfView](https://github.com/Microsoft/perfview) and examining:</span></span>

* <span data-ttu-id="3d8b2-142">Çöp toplama duraklatma süresi.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-142">Garbage collection pause time.</span></span>
* <span data-ttu-id="3d8b2-143">Yüzde işlemci zamanı, çöp toplama harcanır.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-143">What percentage of the processor time is spent in garbage collection.</span></span>
* <span data-ttu-id="3d8b2-144">Kaç çöp koleksiyonları kuşak 0, 1 ve 2 ' dir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-144">How many garbage collections are generation 0, 1, and 2.</span></span>

<span data-ttu-id="3d8b2-145">Daha fazla bilgi için [atık toplama ve performans](/dotnet/standard/garbage-collection/performance).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-145">For more information, see [Garbage Collection and Performance](/dotnet/standard/garbage-collection/performance).</span></span>

## <a name="optimize-data-access"></a><span data-ttu-id="3d8b2-146">Veri erişimini iyileştirmek</span><span class="sxs-lookup"><span data-stu-id="3d8b2-146">Optimize Data Access</span></span>

<span data-ttu-id="3d8b2-147">Bir veri deposunu veya diğer uzak Hizmetleri ile etkileşim genellikle en yavaş bir ASP.NET Core uygulaması parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-147">Interactions with a data store or other remote services are often the slowest part of an ASP.NET Core app.</span></span> <span data-ttu-id="3d8b2-148">Verimli veri yazma ve okuma için iyi bir performans önemlidir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-148">Reading and writing data efficiently is critical for good performance.</span></span>

<span data-ttu-id="3d8b2-149">Öneriler:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-149">Recommendations:</span></span>

* <span data-ttu-id="3d8b2-150">**Yapmak** tüm veri erişimi API'leri zaman uyumsuz olarak çağırın.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-150">**Do** call all data access APIs asynchronously.</span></span>
* <span data-ttu-id="3d8b2-151">**Sağlamadığı** gerekli olandan daha fazla veri alın.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-151">**Do not** retrieve more data than is necessary.</span></span> <span data-ttu-id="3d8b2-152">Geçerli HTTP isteği için gerekli olan verileri döndürmek için sorgular yazarsınız.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-152">Write queries to return just the data that is necessary for the current HTTP request.</span></span>
* <span data-ttu-id="3d8b2-153">**Yapmak** önbelleğe sık erişilen biraz güncel olmayan verileri için kabul edilebilir ise bir veritabanı veya uzak hizmetinden alınan verileri göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-153">**Do** consider caching frequently accessed data retrieved from a database or remote service if it is acceptable for the data to be slightly out-of-date.</span></span> <span data-ttu-id="3d8b2-154">Senaryoya bağlı olarak kullanabileceğinize bir [MemoryCache](xref:performance/caching/memory) veya [DistributedCache](xref:performance/caching/distributed).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-154">Depending on the scenario, you might use a [MemoryCache](xref:performance/caching/memory) or a [DistributedCache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="3d8b2-155">Daha fazla bilgi için bkz. <xref:performance/caching/response>.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-155">For more information, see <xref:performance/caching/response>.</span></span>
* <span data-ttu-id="3d8b2-156">Simge Durumuna Küçült gidiş dönüş ağ.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-156">Minimize network round trips.</span></span> <span data-ttu-id="3d8b2-157">Tek bir çağrıda gereken tüm verileri yerine çeşitli çağrılar alınacak hedeftir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-157">The goal is to retrieve all the data that will be needed in a single call rather than several calls.</span></span>
* <span data-ttu-id="3d8b2-158">**Yapmak** kullanın [Hayır izleme sorguları](/ef/core/querying/tracking#no-tracking-queries) salt okunur amacıyla verilere erişirken Entity Framework Core içinde.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-158">**Do** use [no-tracking queries](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core when accessing data for read-only purposes.</span></span> <span data-ttu-id="3d8b2-159">EF Core Hayır izleme sorguların sonuçlarını daha verimli bir şekilde döndürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-159">EF Core can return the results of no-tracking queries more efficiently.</span></span>
* <span data-ttu-id="3d8b2-160">**Yapmak** filtre ve toplama LINQ sorguları (ile `.Where`, `.Select`, veya `.Sum` deyimleri, örneğin) ve böylece filtreleme işlemi veritabanı tarafından yapılır.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-160">**Do** filter and aggregate LINQ queries (with `.Where`, `.Select`, or `.Sum` statements, for example) so that the filtering is done by the database.</span></span>
* <span data-ttu-id="3d8b2-161">**Yapmak** EF Core bazı sorgu işleçleri verimsiz sorgu yürütülmesine neden olabilir istemcide çözümler göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-161">**Do** consider that EF Core resolves some query operators on the client, which may lead to inefficient query execution.</span></span> <span data-ttu-id="3d8b2-162">Daha fazla bilgi için [istemci değerlendirme performans sorunları](/ef/core/querying/client-eval#client-evaluation-performance-issues)</span><span class="sxs-lookup"><span data-stu-id="3d8b2-162">For more information, see [Client evaluation performance issues](/ef/core/querying/client-eval#client-evaluation-performance-issues)</span></span>
* <span data-ttu-id="3d8b2-163">**Sağlamadığı** "N + 1" yürütülmesi sonucunda koleksiyonlarda yansıtma sorguları kullanmak SQL sorguları.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-163">**Do not** use projection queries on collections, which can result in executing "N + 1" SQL queries.</span></span> <span data-ttu-id="3d8b2-164">Daha fazla bilgi için [bağıntılı alt sorgularda en iyi duruma getirilmesi](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-164">For more information, see [Optimization of correlated subqueries](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).</span></span>

<span data-ttu-id="3d8b2-165">Bkz: [EF yüksek performanslı](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) büyük ölçekli uygulamalarda performansı iyileştirebilir yaklaşımlar için:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-165">See [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) for approaches that may improve performance in high-scale apps:</span></span>

* [<span data-ttu-id="3d8b2-166">DbContext havuzu</span><span class="sxs-lookup"><span data-stu-id="3d8b2-166">DbContext pooling</span></span>](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [<span data-ttu-id="3d8b2-167">Açıkça derlenmiş sorgular</span><span class="sxs-lookup"><span data-stu-id="3d8b2-167">Explicitly compiled queries</span></span>](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

<span data-ttu-id="3d8b2-168">Kod tabanınızın gerçekleştirmeden önce önceki yüksek performanslı yaklaşımları etkisini ölçmek öneririz.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-168">We recommend you measure the impact of the preceding high-performance approaches before committing to your code base.</span></span> <span data-ttu-id="3d8b2-169">Derlenmiş sorgular ek karmaşıklığını performans artışını Yasla değil.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-169">The additional complexity of compiled queries may not justify the performance improvement.</span></span>

<span data-ttu-id="3d8b2-170">Sorgu zaman inceleyerek sorunları algılanamıyor harcanan erişen verilerle [Application Insights](/azure/application-insights/app-insights-overview) veya profil oluşturma araçları ile.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-170">Query issues can be detected by reviewing time spent accessing data with [Application Insights](/azure/application-insights/app-insights-overview) or with profiling tools.</span></span> <span data-ttu-id="3d8b2-171">Çoğu veritabanı istatistikleri de kullanılabilir sık yürütülen sorgular ilgili olun.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-171">Most databases also make statistics available concerning frequently executed queries.</span></span>

## <a name="pool-http-connections-with-httpclientfactory"></a><span data-ttu-id="3d8b2-172">Havuz HTTP bağlantılarıyla HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="3d8b2-172">Pool HTTP connections with HttpClientFactory</span></span>

<span data-ttu-id="3d8b2-173">Ancak [HttpClient](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) uygulayan `IDisposable` arabirimi, amacı, yeniden kullanılabilmeleri.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-173">Although [HttpClient](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) implements the `IDisposable` interface, it's meant to be reused.</span></span> <span data-ttu-id="3d8b2-174">Kapalı `HttpClient` örnekleri yuva açık bırakın `TIME_WAIT` kısa bir süre için durum.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-174">Closed `HttpClient` instances leave sockets open in the `TIME_WAIT` state for a short period of time.</span></span> <span data-ttu-id="3d8b2-175">Sonuç olarak, bir kod yolu oluşturup, siler, `HttpClient` nesneler sık kullanılan, uygulamanın kullanılabilir yuva tüketebilir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-175">Consequently, if a code path that creates and disposes of `HttpClient` objects is frequently used, the app may exhaust available sockets.</span></span> <span data-ttu-id="3d8b2-176">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) ASP.NET Core 2.1 içinde bu soruna bir çözüm olarak sunulmuştur.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-176">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) was introduced in ASP.NET Core 2.1 as a solution to this problem.</span></span> <span data-ttu-id="3d8b2-177">Bu, performansı ve güvenilirliği iyileştirmek için havuzu HTTP bağlantılarını işler.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-177">It handles pooling HTTP connections to optimize performance and reliability.</span></span>

<span data-ttu-id="3d8b2-178">Öneriler:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-178">Recommendations:</span></span>

* <span data-ttu-id="3d8b2-179">**Sağlamadığı** oluşturun ve elden `HttpClient` doğrudan örnekler.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-179">**Do not** create and dispose of `HttpClient` instances directly.</span></span>
* <span data-ttu-id="3d8b2-180">**Yapmak** kullanın [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) alınacak `HttpClient` örnekleri.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-180">**Do** use [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) to retrieve `HttpClient` instances.</span></span> <span data-ttu-id="3d8b2-181">Daha fazla bilgi için [dayanıklı HTTP isteklerini uygulamak için kullanım HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-181">For more information, see [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).</span></span>

## <a name="keep-common-code-paths-fast"></a><span data-ttu-id="3d8b2-182">Ortak kod yollarını hızlı tutun</span><span class="sxs-lookup"><span data-stu-id="3d8b2-182">Keep common code paths fast</span></span>

<span data-ttu-id="3d8b2-183">Tüm kodunuzu hızlı olmasını istediğiniz, ancak sık çağrılan kod yollarının en iyi duruma getirmek için en önemli olan:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-183">You want all of your code to be fast, but frequently called code paths are the most critical to optimize:</span></span>

* <span data-ttu-id="3d8b2-184">Ara yazılım bileşenleri uygulamanın istek işleme ardışık düzeninde özellikle ara yazılımı erken işlem hattında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-184">Middleware components in the app's request processing pipeline, especially middleware run early in the pipeline.</span></span> <span data-ttu-id="3d8b2-185">Bu bileşenlerin performans üzerinde büyük etkiye sahip.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-185">These components have a large impact on performance.</span></span>
* <span data-ttu-id="3d8b2-186">Her istek için veya birden çok kez istek başına yürütülen kod.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-186">Code that is executed for every request or multiple times per request.</span></span> <span data-ttu-id="3d8b2-187">Örneğin, özel günlük kaydı, yetkilendirme işleyicileri veya geçici Hizmetleri başlatma.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-187">For example, custom logging, authorization handlers, or initialization of transient services.</span></span>

<span data-ttu-id="3d8b2-188">Öneriler:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-188">Recommendations:</span></span>

* <span data-ttu-id="3d8b2-189">**Sağlamadığı** özel bir ara yazılım bileşenleri ile uzun süre çalışan görevleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-189">**Do not** use custom middleware components with long-running tasks.</span></span>
* <span data-ttu-id="3d8b2-190">**Yapmak** performans profil oluşturma araçlarını kullanın (gibi [Visual Studio tanılama araçları](/visualstudio/profiling/profiling-feature-tour) veya [PerfView](https://github.com/Microsoft/perfview)) tanımlamak için [sık erişimliye kod yollarını](#hot).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-190">**Do** use performance profiling tools (like [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) or [PerfView](https://github.com/Microsoft/perfview)) to identify [hot code paths](#hot).</span></span>

## <a name="complete-long-running-tasks-outside-of-http-requests"></a><span data-ttu-id="3d8b2-191">HTTP istekleri dışında görevler uzun süreli tamamlayın</span><span class="sxs-lookup"><span data-stu-id="3d8b2-191">Complete long-running Tasks outside of HTTP requests</span></span>

<span data-ttu-id="3d8b2-192">ASP.NET Core uygulaması için en çok istekte bir denetleyici veya gerekli hizmetleri çağırmak ve bir HTTP yanıtı döndüren sayfa modeli tarafından işlenebilir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-192">Most requests to an ASP.NET Core app can be handled by a controller or page model calling necessary services and returning an HTTP response.</span></span> <span data-ttu-id="3d8b2-193">Uzun süre çalışan görevleri içeren bazı istekler için tüm istek-yanıt işlemini zaman uyumsuz kolaylaştırmak iyidir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-193">For some requests that involve long-running tasks, it's better to make the entire request-response process asynchronous.</span></span>

<span data-ttu-id="3d8b2-194">Öneriler:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-194">Recommendations:</span></span>

* <span data-ttu-id="3d8b2-195">**Sağlamadığı** sıradan HTTP istek işlemenin bir parçası olarak tamamlanması uzun süre çalışan görevler için bekleyin.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-195">**Do not** wait for long-running tasks to complete as part of ordinary HTTP request processing.</span></span>
* <span data-ttu-id="3d8b2-196">**Yapmak** uzun süren istekleri işleme göz önünde bulundurun [arka plan Hizmetleri](/aspnet/core/fundamentals/host/hosted-services) veya işlem dışında bir [Azure işlevi](/azure/azure-functions/).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-196">**Do** consider handling long-running requests with [background services](/aspnet/core/fundamentals/host/hosted-services) or out of process with an [Azure Function](/azure/azure-functions/).</span></span> <span data-ttu-id="3d8b2-197">İş dışı işlem Tamamlanıyor, CPU yoğunluklu görevler için özellikle yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-197">Completing work out-of-process is especially beneficial for CPU-intensive tasks.</span></span>
* <span data-ttu-id="3d8b2-198">**Yapmak** gibi gerçek zamanlı iletişim seçenekleri kullanmak [SignalR](xref:signalr/introduction) istemcilerle zaman uyumsuz olarak iletişim kurmak için.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-198">**Do** use real-time communication options like [SignalR](xref:signalr/introduction) to communicate with clients asynchronously.</span></span>

## <a name="minify-client-assets"></a><span data-ttu-id="3d8b2-199">İstemci varlıklar küçültün</span><span class="sxs-lookup"><span data-stu-id="3d8b2-199">Minify client assets</span></span>

<span data-ttu-id="3d8b2-200">ASP.NET Core uygulamaları karmaşık ön uç ile sık birçok JavaScript, CSS veya görüntü dosyaları işlevi görür.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-200">ASP.NET Core apps with complex front-ends frequently serve many JavaScript, CSS, or image files.</span></span> <span data-ttu-id="3d8b2-201">İlk yükleme istekleri performansını tarafından geliştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-201">Performance of initial load requests can be improved by:</span></span>

* <span data-ttu-id="3d8b2-202">Paketleme, birden çok dosyayı tek bir araya getiren.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-202">Bundling, which combines multiple files into one.</span></span>
* <span data-ttu-id="3d8b2-203">Küçültme, dosyaları tarafından boyutunu azaltır.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-203">Minifying, which reduces the size of files by.</span></span>

<span data-ttu-id="3d8b2-204">Öneriler:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-204">Recommendations:</span></span>

* <span data-ttu-id="3d8b2-205">**Yapmak** kullanan ASP.NET Core'nın [yerleşik destek](xref:client-side/bundling-and-minification) paketleme ve küçültme istemci varlıklar için.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-205">**Do** use ASP.NET Core's [built-in support](xref:client-side/bundling-and-minification) for bundling and minifying client assets.</span></span>
* <span data-ttu-id="3d8b2-206">**Yapmak** gibi diğer üçüncü taraf araçları göz önünde bulundurun [Gulp](uid:client-side/bundling-and-minification#consume-bundleconfigjson-from-gulp) veya [Web](https://webpack.js.org/) daha karmaşık istemci varlık yönetimi.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-206">**Do** consider other third-party tools like [Gulp](uid:client-side/bundling-and-minification#consume-bundleconfigjson-from-gulp) or [Webpack](https://webpack.js.org/) for more complex client asset management.</span></span>

## <a name="use-the-latest-aspnet-core-release"></a><span data-ttu-id="3d8b2-207">ASP.NET Core en son sürümü kullan</span><span class="sxs-lookup"><span data-stu-id="3d8b2-207">Use the latest ASP.NET Core release</span></span>

<span data-ttu-id="3d8b2-208">ASP.NET her yeni sürümü, performans iyileştirmeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-208">Each new release of ASP.NET includes performance improvements.</span></span> <span data-ttu-id="3d8b2-209">.NET Core ve ASP.NET Core iyileştirmeler, daha yeni sürümleri eski sürümleri aşar anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-209">Optimizations in .NET Core and ASP.NET Core mean that newer versions will outperform older versions.</span></span> <span data-ttu-id="3d8b2-210">Örneğin, .NET Core 2.1 gelen benefitted ve derlenmiş normal ifadeler için destek eklendi [ `Span<T>` ](https://msdn.microsoft.com/magazine/mt814808.aspx).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-210">For example, .NET Core 2.1 added support for compiled regular expressions and benefitted from [`Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx).</span></span> <span data-ttu-id="3d8b2-211">HTTP/2 desteği ASP.NET Core 2.2 eklendi.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-211">ASP.NET Core 2.2 added support for HTTP/2.</span></span> <span data-ttu-id="3d8b2-212">Bir öncelik performans ise ASP.NET Core en güncel sürümüne yükseltmeyi göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-212">If performance is a priority, consider upgrading to the most current version of ASP.NET Core.</span></span>

<!-- TODO review link and taking advantage of new [performance features](#TBD)
Maybe skip this TBD link as each version will have perf improvements -->

## <a name="minimize-exceptions"></a><span data-ttu-id="3d8b2-213">Özel durumları en aza indirin</span><span class="sxs-lookup"><span data-stu-id="3d8b2-213">Minimize exceptions</span></span>

<span data-ttu-id="3d8b2-214">Özel durumlar seyrek olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-214">Exceptions should be rare.</span></span> <span data-ttu-id="3d8b2-215">Oluşturmak ve özel durumları yakalamak, diğer kod akış desenlerini göre yavaştır.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-215">Throwing and catching exceptions is slow relative to other code flow patterns.</span></span> <span data-ttu-id="3d8b2-216">Bu nedenle, özel durumlar programın normal akışını denetlemek için kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-216">Because of this, exceptions should not be used to control normal program flow.</span></span>

<span data-ttu-id="3d8b2-217">Öneriler:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-217">Recommendations:</span></span>

* <span data-ttu-id="3d8b2-218">**Sağlamadığı** atma veya özel durumları yakalama, özellikle de sık erişimli kod yollarını normal program akışı yöntemi olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-218">**Do not** use throwing or catching exceptions as a means of normal program flow, especially in hot code paths.</span></span>
* <span data-ttu-id="3d8b2-219">**Yapmak** algılar ve bir özel durum neden olan koşulları işlemek için uygulamada mantığı içerir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-219">**Do** include logic in the app to detect and handle conditions that would cause an exception.</span></span>
* <span data-ttu-id="3d8b2-220">**Yapmak** throw veya catch özel durumları için olağan dışı ya da beklenmeyen koşulları.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-220">**Do** throw or catch exceptions for unusual or unexpected conditions.</span></span>

<span data-ttu-id="3d8b2-221">Uygulama Tanılama Araçları (örneğin, Application Insights) performansını etkileyebilecek bir uygulama yaygın özel durumları belirlemeye yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-221">App diagnostic tools (like Application Insights) can help to identify common exceptions in an application which may affect performance.</span></span>