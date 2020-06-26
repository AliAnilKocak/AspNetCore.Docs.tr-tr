---
title: ASP.NET Core 'de dağıtılmış önbelleğe alma
author: rick-anderson
description: Özellikle bir bulutta veya sunucu grubu ortamında uygulama performansını ve ölçeklenebilirliğini artırmak için ASP.NET Core dağıtılmış bir önbelleğin nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/distributed
ms.openlocfilehash: 56c67178bd5c63f08a812357a4f8e672dd483994
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405399"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="67659-103">ASP.NET Core 'de dağıtılmış önbelleğe alma</span><span class="sxs-lookup"><span data-stu-id="67659-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="67659-104">Düzenleyen/ [mohsin Nasir](https://github.com/mohsinnasir) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="67659-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="67659-105">Dağıtılmış önbellek, genellikle ona erişen uygulama sunucuları için bir dış hizmet olarak tutulan birden çok uygulama sunucusu tarafından paylaşılan bir önbellektir.</span><span class="sxs-lookup"><span data-stu-id="67659-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="67659-106">Dağıtılmış önbellek, özellikle uygulama bir bulut hizmeti veya sunucu grubu tarafından barındırıldığı zaman bir ASP.NET Core uygulamasının performansını ve ölçeklenebilirliğini iyileştirebilir.</span><span class="sxs-lookup"><span data-stu-id="67659-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="67659-107">Dağıtılmış bir önbellek, önbelleğe alınan verilerin ayrı uygulama sunucularında depolandığı diğer önbelleğe alma senaryolarından daha fazla avantaj sunar.</span><span class="sxs-lookup"><span data-stu-id="67659-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="67659-108">Önbelleğe alınan veriler dağıtıldığında, veriler:</span><span class="sxs-lookup"><span data-stu-id="67659-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="67659-109">Birden çok sunucuya yönelik istekler arasında *tutarlı (tutarlı* ).</span><span class="sxs-lookup"><span data-stu-id="67659-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="67659-110">Sunucu yeniden başlatmaları ve uygulama dağıtımları.</span><span class="sxs-lookup"><span data-stu-id="67659-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="67659-111">Yerel bellek kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="67659-111">Doesn't use local memory.</span></span>

<span data-ttu-id="67659-112">Dağıtılmış önbellek yapılandırması uygulamaya özgüdür.</span><span class="sxs-lookup"><span data-stu-id="67659-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="67659-113">Bu makalede SQL Server ve Redsıs dağıtılmış önbelleklerinin nasıl yapılandırılacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="67659-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="67659-114">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub üzerindeki nCache](https://github.com/Alachisoft/NCache)) gibi üçüncü taraf uygulamalar da mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="67659-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="67659-115">Uygulama hangi uygulamanın seçildiğine bakılmaksızın, arabirimi kullanılarak önbellek ile etkileşime girer <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="67659-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="67659-116">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="67659-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="67659-117">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="67659-117">Prerequisites</span></span>

<span data-ttu-id="67659-118">SQL Server dağıtılmış önbellek kullanmak için [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="67659-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="67659-119">Redsıs dağıtılmış önbelleği kullanmak için [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="67659-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="67659-120">NCache dağıtılmış önbelleğini kullanmak için [nCache. Microsoft. Extensions. Caching. OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="67659-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="67659-121">Idistributedcache arabirimi</span><span class="sxs-lookup"><span data-stu-id="67659-121">IDistributedCache interface</span></span>

<span data-ttu-id="67659-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Arabirim, dağıtılmış önbellek uygulamasındaki öğeleri işlemek için aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="67659-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="67659-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Bir dize anahtarını kabul eder ve önbellekte bulunursa önbelleğe alınmış bir öğeyi dizi olarak alır `byte[]` .</span><span class="sxs-lookup"><span data-stu-id="67659-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="67659-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` Bir dize anahtarı kullanarak önbelleğe bir öğe (dizi olarak) ekler.</span><span class="sxs-lookup"><span data-stu-id="67659-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="67659-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Önbellekte bir öğeyi anahtarını temel alarak yeniler, Kayan süre sonu zaman aşımını (varsa) sıfırlarken.</span><span class="sxs-lookup"><span data-stu-id="67659-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="67659-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Bir önbellek öğesini dize anahtarına göre kaldırır.</span><span class="sxs-lookup"><span data-stu-id="67659-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="67659-127">Dağıtılmış önbelleğe alma hizmetleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="67659-127">Establish distributed caching services</span></span>

<span data-ttu-id="67659-128">Uygulamasına bir uygulamasını kaydetme <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="67659-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="67659-129">Bu konuda açıklanan Framework tarafından sunulan uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="67659-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="67659-130">Dağıtılmış bellek önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="67659-131">Dağıtılmış SQL Server önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="67659-132">Dağıtılmış Redsıs önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="67659-133">Dağıtılmış NCache önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="67659-134">Dağıtılmış bellek önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-134">Distributed Memory Cache</span></span>

<span data-ttu-id="67659-135">Dağıtılmış bellek önbelleği ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ), öğeleri bellekte depolayan çerçeve tarafından sağlanmış bir uygulamasıdır <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="67659-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="67659-136">Dağıtılmış bellek önbelleği gerçek bir dağıtılmış önbellek değildir.</span><span class="sxs-lookup"><span data-stu-id="67659-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="67659-137">Önbelleğe alınan öğeler, uygulamanın çalıştığı sunucuda uygulama örneği tarafından depolanır.</span><span class="sxs-lookup"><span data-stu-id="67659-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="67659-138">Dağıtılmış bellek önbelleği, yararlı bir uygulama:</span><span class="sxs-lookup"><span data-stu-id="67659-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="67659-139">Geliştirme ve test senaryolarında.</span><span class="sxs-lookup"><span data-stu-id="67659-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="67659-140">Üretimde tek bir sunucu kullanıldığında ve bellek tüketimi bir sorun değildir.</span><span class="sxs-lookup"><span data-stu-id="67659-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="67659-141">Dağıtılmış bellek önbelleğinin uygulanması, önbelleğe alınmış veri depolamayı soyutlar.</span><span class="sxs-lookup"><span data-stu-id="67659-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="67659-142">Birden çok düğüm veya hata toleransı gerekliyse, gelecekte doğru bir dağıtılmış önbelleğe alma çözümü uygulamaya olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="67659-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="67659-143">Örnek uygulama, uygulamasında geliştirme ortamında uygulama çalıştırıldığında dağıtılmış bellek önbelleğinin kullanımını sağlar `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67659-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="67659-144">Dağıtılmış SQL Server önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="67659-145">Dağıtılmış SQL Server önbellek uygulama ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) dağıtılmış önbelleğin, kendi yedekleme deposu olarak bir SQL Server veritabanı kullanmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="67659-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="67659-146">Bir SQL Server örneğinde önbelleğe alınmış SQL Server bir öğe tablosu oluşturmak için `sql-cache` aracını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="67659-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="67659-147">Araç, belirttiğiniz ad ve şemaya sahip bir tablo oluşturur.</span><span class="sxs-lookup"><span data-stu-id="67659-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="67659-148">Komutunu çalıştırarak SQL Server tablo oluşturun `sql-cache create` .</span><span class="sxs-lookup"><span data-stu-id="67659-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="67659-149">SQL Server örneği ( `Data Source` ), veritabanı (), `Initial Catalog` şema (örneğin,) `dbo` ve tablo adı (örneğin, `TestCache` ) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="67659-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="67659-150">Aracın başarılı olduğunu göstermek için bir ileti günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="67659-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="67659-151">Araç tarafından oluşturulan tablo `sql-cache` aşağıdaki şemaya sahiptir:</span><span class="sxs-lookup"><span data-stu-id="67659-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer önbellek tablosu](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="67659-153">Bir uygulama, bir örneğini kullanarak önbellek değerlerini işlemelidir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="67659-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="67659-154">Örnek uygulama, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> Içinde geliştirme dışı bir ortamda uygular `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67659-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="67659-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(Ve isteğe bağlı olarak, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> ve <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) genellikle kaynak denetimi dışında (örneğin, [gizli yönetici](xref:security/app-secrets) tarafından veya appSettings *üzerindeappsettings.js*depolanır / *. { ENVIRONMENT}. JSON* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="67659-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="67659-156">Bağlantı dizesinde kaynak denetim sistemlerinden tutulması gereken kimlik bilgileri bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="67659-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="67659-157">Dağıtılmış Redis Cache</span><span class="sxs-lookup"><span data-stu-id="67659-157">Distributed Redis Cache</span></span>

<span data-ttu-id="67659-158">[Redsıs](https://redis.io/) , genellikle dağıtılmış önbellek olarak kullanılan açık kaynaklı bir bellek içi veri deposudur.</span><span class="sxs-lookup"><span data-stu-id="67659-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="67659-159">Redsıs 'yi yerel olarak kullanabilir ve Azure 'da barındırılan ASP.NET Core uygulaması için bir [Azure Redis Cache](https://azure.microsoft.com/services/cache/) yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="67659-159">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="67659-160">Bir uygulama, <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> içinde geliştirme olmayan bir ortamda bir örnek () kullanarak önbellek uygulamasını yapılandırır `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67659-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="67659-161">Redsıs 'i yerel makinenize yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="67659-161">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="67659-162">[Chocolatey redsıs paketini](https://chocolatey.org/packages/redis-64/)yükler.</span><span class="sxs-lookup"><span data-stu-id="67659-162">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="67659-163">`redis-server`Komut isteminden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="67659-163">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="67659-164">Dağıtılmış NCache önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-164">Distributed NCache Cache</span></span>

<span data-ttu-id="67659-165">[NCache](https://github.com/Alachisoft/NCache) , .NET ve .NET Core 'da yerel olarak geliştirilen açık kaynaklı bellek içi dağıtılmış bir önbellektir.</span><span class="sxs-lookup"><span data-stu-id="67659-165">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="67659-166">NCache hem yerel olarak hem de Azure 'da veya diğer barındırma platformlarında çalışan bir ASP.NET Core uygulama için dağıtılmış önbellek kümesi olarak yapılandırılmış şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="67659-166">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="67659-167">Yerel makinenizde NCache 'i yüklemek ve yapılandırmak için bkz. [Windows Için nCache Başlangıç Kılavuzu](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="67659-167">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="67659-168">NCache 'yi yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="67659-168">To configure NCache:</span></span>

1. <span data-ttu-id="67659-169">[NCache açık kaynak NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="67659-169">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="67659-170">Cache kümesini [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)' de yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="67659-170">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="67659-171">Aşağıdaki kodu öğesine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67659-171">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="67659-172">Dağıtılmış önbelleği kullan</span><span class="sxs-lookup"><span data-stu-id="67659-172">Use the distributed cache</span></span>

<span data-ttu-id="67659-173">Arabirimini kullanmak için <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> uygulamadaki herhangi bir oluşturucudan bir örnek isteyin.</span><span class="sxs-lookup"><span data-stu-id="67659-173">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="67659-174">Örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="67659-174">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="67659-175">Örnek uygulama başlatıldığında <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> öğesine eklenir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="67659-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="67659-176">Şu anki süre kullanılarak önbelleğe alındı <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (daha fazla bilgi için bkz. [genel konak: ıhostapplicationlifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="67659-176">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="67659-177">Örnek uygulama <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , `IndexModel` Dizin sayfası tarafından kullanılmak üzere içine çıkarır.</span><span class="sxs-lookup"><span data-stu-id="67659-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="67659-178">Dizin sayfası her yüklendiğinde, önbellek, içindeki önbelleğe alınmış zamana göre denetlenir `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="67659-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="67659-179">Önbelleğe alınmış sürenin süresi dolmamışsa, zaman görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="67659-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="67659-180">Önbelleğe alınan saate son erişildiği zamandan bu yana 20 saniye geçtikten sonra (bu sayfanın son yüklenilişinde), sayfanın *önbelleğe alınma süresi doldu*.</span><span class="sxs-lookup"><span data-stu-id="67659-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="67659-181">Önbelleğe alınmış süreyi **Sıfırla** ' yı seçerek önbelleğe alınmış zamanı geçerli saate hemen güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="67659-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="67659-182">Düğme `OnPostResetCachedTime` işleyici metodunu tetikler.</span><span class="sxs-lookup"><span data-stu-id="67659-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="67659-183">Örnekler için tek veya kapsamlı bir yaşam süresi kullanmanız gerekmez <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (en azından yerleşik uygulamalar için).</span><span class="sxs-lookup"><span data-stu-id="67659-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="67659-184">Ayrıca <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , dı kullanmak yerine bir örnek oluşturabilirsiniz, ancak kodda bir örnek oluşturmak kodunuzu test etmek ve [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)ihlal etmek için daha zor hale gelebilir.</span><span class="sxs-lookup"><span data-stu-id="67659-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="67659-185">Öneriler</span><span class="sxs-lookup"><span data-stu-id="67659-185">Recommendations</span></span>

<span data-ttu-id="67659-186"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Uygulamanız için hangi uygulamanın en iyisi olduğuna karar verirken aşağıdakileri göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="67659-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="67659-187">Mevcut altyapı</span><span class="sxs-lookup"><span data-stu-id="67659-187">Existing infrastructure</span></span>
* <span data-ttu-id="67659-188">Performans gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="67659-188">Performance requirements</span></span>
* <span data-ttu-id="67659-189">Maliyet</span><span class="sxs-lookup"><span data-stu-id="67659-189">Cost</span></span>
* <span data-ttu-id="67659-190">Ekip deneyimi</span><span class="sxs-lookup"><span data-stu-id="67659-190">Team experience</span></span>

<span data-ttu-id="67659-191">Önbelleğe alma çözümleri genellikle önbelleğe alınmış verilerin hızlı bir şekilde alınmasını sağlamak için bellek içi depolamaya dayanır, ancak bellek sınırlı bir kaynaktır ve genişleyebilir.</span><span class="sxs-lookup"><span data-stu-id="67659-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="67659-192">Yalnızca yaygın olarak kullanılan verileri bir önbellekte depolayın.</span><span class="sxs-lookup"><span data-stu-id="67659-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="67659-193">Genellikle, Redsıs önbelleği daha yüksek aktarım hızı ve bir SQL Server önbelleğinden daha düşük gecikme süresi sağlar.</span><span class="sxs-lookup"><span data-stu-id="67659-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="67659-194">Bununla birlikte, genellikle önbelleğe alma stratejilerinin performans özelliklerini belirlemede bir değerlendirme gerekir.</span><span class="sxs-lookup"><span data-stu-id="67659-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="67659-195">SQL Server dağıtılmış önbellek yedekleme deposu olarak kullanıldığında, önbellek için aynı veritabanını kullanın ve uygulamanın sıradan veri depolama ve alma, her ikisinin performansını olumsuz yönde etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="67659-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="67659-196">Dağıtılmış önbellek yedekleme deposu için adanmış bir SQL Server örneği kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="67659-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="67659-197">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="67659-197">Additional resources</span></span>

* [<span data-ttu-id="67659-198">Azure üzerinde Redis Cache</span><span class="sxs-lookup"><span data-stu-id="67659-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="67659-199">Azure’da SQL Database</span><span class="sxs-lookup"><span data-stu-id="67659-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="67659-200">[Web çiftlerine nCache Için ıdistributedcache sağlayıcısını ASP.NET Core](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub 'da nCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="67659-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="67659-201">Dağıtılmış önbellek, genellikle ona erişen uygulama sunucuları için bir dış hizmet olarak tutulan birden çok uygulama sunucusu tarafından paylaşılan bir önbellektir.</span><span class="sxs-lookup"><span data-stu-id="67659-201">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="67659-202">Dağıtılmış önbellek, özellikle uygulama bir bulut hizmeti veya sunucu grubu tarafından barındırıldığı zaman bir ASP.NET Core uygulamasının performansını ve ölçeklenebilirliğini iyileştirebilir.</span><span class="sxs-lookup"><span data-stu-id="67659-202">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="67659-203">Dağıtılmış bir önbellek, önbelleğe alınan verilerin ayrı uygulama sunucularında depolandığı diğer önbelleğe alma senaryolarından daha fazla avantaj sunar.</span><span class="sxs-lookup"><span data-stu-id="67659-203">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="67659-204">Önbelleğe alınan veriler dağıtıldığında, veriler:</span><span class="sxs-lookup"><span data-stu-id="67659-204">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="67659-205">Birden çok sunucuya yönelik istekler arasında *tutarlı (tutarlı* ).</span><span class="sxs-lookup"><span data-stu-id="67659-205">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="67659-206">Sunucu yeniden başlatmaları ve uygulama dağıtımları.</span><span class="sxs-lookup"><span data-stu-id="67659-206">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="67659-207">Yerel bellek kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="67659-207">Doesn't use local memory.</span></span>

<span data-ttu-id="67659-208">Dağıtılmış önbellek yapılandırması uygulamaya özgüdür.</span><span class="sxs-lookup"><span data-stu-id="67659-208">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="67659-209">Bu makalede SQL Server ve Redsıs dağıtılmış önbelleklerinin nasıl yapılandırılacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="67659-209">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="67659-210">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub üzerindeki nCache](https://github.com/Alachisoft/NCache)) gibi üçüncü taraf uygulamalar da mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="67659-210">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="67659-211">Uygulama hangi uygulamanın seçildiğine bakılmaksızın, arabirimi kullanılarak önbellek ile etkileşime girer <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="67659-211">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="67659-212">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="67659-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="67659-213">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="67659-213">Prerequisites</span></span>

<span data-ttu-id="67659-214">SQL Server dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="67659-214">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="67659-215">Redsıs dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="67659-215">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="67659-216">Redsıs paketi pakete dahil değildir, bu `Microsoft.AspNetCore.App` nedenle Redu paketine proje dosyanızda ayrı olarak başvurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="67659-216">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="67659-217">NCache dağıtılmış önbelleğini kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [nCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="67659-217">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="67659-218">NCache paketi pakete dahil değildir, bu `Microsoft.AspNetCore.App` nedenle, proje dosyanızda ayrı olarak NCache paketine başvurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="67659-218">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="67659-219">Idistributedcache arabirimi</span><span class="sxs-lookup"><span data-stu-id="67659-219">IDistributedCache interface</span></span>

<span data-ttu-id="67659-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Arabirim, dağıtılmış önbellek uygulamasındaki öğeleri işlemek için aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="67659-220">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="67659-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Bir dize anahtarını kabul eder ve önbellekte bulunursa önbelleğe alınmış bir öğeyi dizi olarak alır `byte[]` .</span><span class="sxs-lookup"><span data-stu-id="67659-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="67659-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` Bir dize anahtarı kullanarak önbelleğe bir öğe (dizi olarak) ekler.</span><span class="sxs-lookup"><span data-stu-id="67659-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="67659-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Önbellekte bir öğeyi anahtarını temel alarak yeniler, Kayan süre sonu zaman aşımını (varsa) sıfırlarken.</span><span class="sxs-lookup"><span data-stu-id="67659-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="67659-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Bir önbellek öğesini dize anahtarına göre kaldırır.</span><span class="sxs-lookup"><span data-stu-id="67659-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="67659-225">Dağıtılmış önbelleğe alma hizmetleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="67659-225">Establish distributed caching services</span></span>

<span data-ttu-id="67659-226">Uygulamasına bir uygulamasını kaydetme <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="67659-226">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="67659-227">Bu konuda açıklanan Framework tarafından sunulan uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="67659-227">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="67659-228">Dağıtılmış bellek önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-228">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="67659-229">Dağıtılmış SQL Server önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-229">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="67659-230">Dağıtılmış Redsıs önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-230">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="67659-231">Dağıtılmış NCache önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-231">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="67659-232">Dağıtılmış bellek önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-232">Distributed Memory Cache</span></span>

<span data-ttu-id="67659-233">Dağıtılmış bellek önbelleği ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ), öğeleri bellekte depolayan çerçeve tarafından sağlanmış bir uygulamasıdır <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="67659-233">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="67659-234">Dağıtılmış bellek önbelleği gerçek bir dağıtılmış önbellek değildir.</span><span class="sxs-lookup"><span data-stu-id="67659-234">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="67659-235">Önbelleğe alınan öğeler, uygulamanın çalıştığı sunucuda uygulama örneği tarafından depolanır.</span><span class="sxs-lookup"><span data-stu-id="67659-235">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="67659-236">Dağıtılmış bellek önbelleği, yararlı bir uygulama:</span><span class="sxs-lookup"><span data-stu-id="67659-236">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="67659-237">Geliştirme ve test senaryolarında.</span><span class="sxs-lookup"><span data-stu-id="67659-237">In development and testing scenarios.</span></span>
* <span data-ttu-id="67659-238">Üretimde tek bir sunucu kullanıldığında ve bellek tüketimi bir sorun değildir.</span><span class="sxs-lookup"><span data-stu-id="67659-238">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="67659-239">Dağıtılmış bellek önbelleğinin uygulanması, önbelleğe alınmış veri depolamayı soyutlar.</span><span class="sxs-lookup"><span data-stu-id="67659-239">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="67659-240">Birden çok düğüm veya hata toleransı gerekliyse, gelecekte doğru bir dağıtılmış önbelleğe alma çözümü uygulamaya olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="67659-240">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="67659-241">Örnek uygulama, uygulamasında geliştirme ortamında uygulama çalıştırıldığında dağıtılmış bellek önbelleğinin kullanımını sağlar `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67659-241">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="67659-242">Dağıtılmış SQL Server önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-242">Distributed SQL Server Cache</span></span>

<span data-ttu-id="67659-243">Dağıtılmış SQL Server önbellek uygulama ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) dağıtılmış önbelleğin, kendi yedekleme deposu olarak bir SQL Server veritabanı kullanmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="67659-243">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="67659-244">Bir SQL Server örneğinde önbelleğe alınmış SQL Server bir öğe tablosu oluşturmak için `sql-cache` aracını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="67659-244">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="67659-245">Araç, belirttiğiniz ad ve şemaya sahip bir tablo oluşturur.</span><span class="sxs-lookup"><span data-stu-id="67659-245">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="67659-246">Komutunu çalıştırarak SQL Server tablo oluşturun `sql-cache create` .</span><span class="sxs-lookup"><span data-stu-id="67659-246">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="67659-247">SQL Server örneği ( `Data Source` ), veritabanı (), `Initial Catalog` şema (örneğin,) `dbo` ve tablo adı (örneğin, `TestCache` ) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="67659-247">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="67659-248">Aracın başarılı olduğunu göstermek için bir ileti günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="67659-248">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="67659-249">Araç tarafından oluşturulan tablo `sql-cache` aşağıdaki şemaya sahiptir:</span><span class="sxs-lookup"><span data-stu-id="67659-249">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer önbellek tablosu](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="67659-251">Bir uygulama, bir örneğini kullanarak önbellek değerlerini işlemelidir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="67659-251">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="67659-252">Örnek uygulama, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> Içinde geliştirme dışı bir ortamda uygular `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67659-252">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="67659-253"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(Ve isteğe bağlı olarak, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> ve <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) genellikle kaynak denetimi dışında (örneğin, [gizli yönetici](xref:security/app-secrets) tarafından veya appSettings *üzerindeappsettings.js*depolanır / *. { ENVIRONMENT}. JSON* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="67659-253">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="67659-254">Bağlantı dizesinde kaynak denetim sistemlerinden tutulması gereken kimlik bilgileri bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="67659-254">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="67659-255">Dağıtılmış Redis Cache</span><span class="sxs-lookup"><span data-stu-id="67659-255">Distributed Redis Cache</span></span>

<span data-ttu-id="67659-256">[Redsıs](https://redis.io/) , genellikle dağıtılmış önbellek olarak kullanılan açık kaynaklı bir bellek içi veri deposudur.</span><span class="sxs-lookup"><span data-stu-id="67659-256">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="67659-257">Redsıs 'yi yerel olarak kullanabilir ve Azure 'da barındırılan ASP.NET Core uygulaması için bir [Azure Redis Cache](https://azure.microsoft.com/services/cache/) yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="67659-257">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="67659-258">Bir uygulama, <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> içinde geliştirme olmayan bir ortamda bir örnek () kullanarak önbellek uygulamasını yapılandırır `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67659-258">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="67659-259">Redsıs 'i yerel makinenize yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="67659-259">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="67659-260">[Chocolatey redsıs paketini](https://chocolatey.org/packages/redis-64/)yükler.</span><span class="sxs-lookup"><span data-stu-id="67659-260">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="67659-261">`redis-server`Komut isteminden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="67659-261">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="67659-262">Dağıtılmış NCache önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-262">Distributed NCache Cache</span></span>

<span data-ttu-id="67659-263">[NCache](https://github.com/Alachisoft/NCache) , .NET ve .NET Core 'da yerel olarak geliştirilen açık kaynaklı bellek içi dağıtılmış bir önbellektir.</span><span class="sxs-lookup"><span data-stu-id="67659-263">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="67659-264">NCache hem yerel olarak hem de Azure 'da veya diğer barındırma platformlarında çalışan bir ASP.NET Core uygulama için dağıtılmış önbellek kümesi olarak yapılandırılmış şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="67659-264">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="67659-265">Yerel makinenizde NCache 'i yüklemek ve yapılandırmak için bkz. [Windows Için nCache Başlangıç Kılavuzu](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="67659-265">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="67659-266">NCache 'yi yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="67659-266">To configure NCache:</span></span>

1. <span data-ttu-id="67659-267">[NCache açık kaynak NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="67659-267">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="67659-268">Cache kümesini [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)' de yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="67659-268">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="67659-269">Aşağıdaki kodu öğesine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67659-269">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="67659-270">Dağıtılmış önbelleği kullan</span><span class="sxs-lookup"><span data-stu-id="67659-270">Use the distributed cache</span></span>

<span data-ttu-id="67659-271">Arabirimini kullanmak için <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> uygulamadaki herhangi bir oluşturucudan bir örnek isteyin.</span><span class="sxs-lookup"><span data-stu-id="67659-271">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="67659-272">Örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="67659-272">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="67659-273">Örnek uygulama başlatıldığında <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> öğesine eklenir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="67659-273">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="67659-274">Şu anki süre kullanılarak önbelleğe alındı <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (daha fazla bilgi için bkz [. Web Host: ıapplicationlifetime arabirimi](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="67659-274">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="67659-275">Örnek uygulama <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , `IndexModel` Dizin sayfası tarafından kullanılmak üzere içine çıkarır.</span><span class="sxs-lookup"><span data-stu-id="67659-275">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="67659-276">Dizin sayfası her yüklendiğinde, önbellek, içindeki önbelleğe alınmış zamana göre denetlenir `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="67659-276">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="67659-277">Önbelleğe alınmış sürenin süresi dolmamışsa, zaman görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="67659-277">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="67659-278">Önbelleğe alınan saate son erişildiği zamandan bu yana 20 saniye geçtikten sonra (bu sayfanın son yüklenilişinde), sayfanın *önbelleğe alınma süresi doldu*.</span><span class="sxs-lookup"><span data-stu-id="67659-278">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="67659-279">Önbelleğe alınmış süreyi **Sıfırla** ' yı seçerek önbelleğe alınmış zamanı geçerli saate hemen güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="67659-279">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="67659-280">Düğme `OnPostResetCachedTime` işleyici metodunu tetikler.</span><span class="sxs-lookup"><span data-stu-id="67659-280">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="67659-281">Örnekler için tek veya kapsamlı bir yaşam süresi kullanmanız gerekmez <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (en azından yerleşik uygulamalar için).</span><span class="sxs-lookup"><span data-stu-id="67659-281">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="67659-282">Ayrıca <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , dı kullanmak yerine bir örnek oluşturabilirsiniz, ancak kodda bir örnek oluşturmak kodunuzu test etmek ve [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)ihlal etmek için daha zor hale gelebilir.</span><span class="sxs-lookup"><span data-stu-id="67659-282">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="67659-283">Öneriler</span><span class="sxs-lookup"><span data-stu-id="67659-283">Recommendations</span></span>

<span data-ttu-id="67659-284"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Uygulamanız için hangi uygulamanın en iyisi olduğuna karar verirken aşağıdakileri göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="67659-284">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="67659-285">Mevcut altyapı</span><span class="sxs-lookup"><span data-stu-id="67659-285">Existing infrastructure</span></span>
* <span data-ttu-id="67659-286">Performans gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="67659-286">Performance requirements</span></span>
* <span data-ttu-id="67659-287">Maliyet</span><span class="sxs-lookup"><span data-stu-id="67659-287">Cost</span></span>
* <span data-ttu-id="67659-288">Ekip deneyimi</span><span class="sxs-lookup"><span data-stu-id="67659-288">Team experience</span></span>

<span data-ttu-id="67659-289">Önbelleğe alma çözümleri genellikle önbelleğe alınmış verilerin hızlı bir şekilde alınmasını sağlamak için bellek içi depolamaya dayanır, ancak bellek sınırlı bir kaynaktır ve genişleyebilir.</span><span class="sxs-lookup"><span data-stu-id="67659-289">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="67659-290">Yalnızca yaygın olarak kullanılan verileri bir önbellekte depolayın.</span><span class="sxs-lookup"><span data-stu-id="67659-290">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="67659-291">Genellikle, Redsıs önbelleği daha yüksek aktarım hızı ve bir SQL Server önbelleğinden daha düşük gecikme süresi sağlar.</span><span class="sxs-lookup"><span data-stu-id="67659-291">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="67659-292">Bununla birlikte, genellikle önbelleğe alma stratejilerinin performans özelliklerini belirlemede bir değerlendirme gerekir.</span><span class="sxs-lookup"><span data-stu-id="67659-292">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="67659-293">SQL Server dağıtılmış önbellek yedekleme deposu olarak kullanıldığında, önbellek için aynı veritabanını kullanın ve uygulamanın sıradan veri depolama ve alma, her ikisinin performansını olumsuz yönde etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="67659-293">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="67659-294">Dağıtılmış önbellek yedekleme deposu için adanmış bir SQL Server örneği kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="67659-294">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="67659-295">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="67659-295">Additional resources</span></span>

* [<span data-ttu-id="67659-296">Azure üzerinde Redis Cache</span><span class="sxs-lookup"><span data-stu-id="67659-296">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="67659-297">Azure’da SQL Database</span><span class="sxs-lookup"><span data-stu-id="67659-297">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="67659-298">[Web çiftlerine nCache Için ıdistributedcache sağlayıcısını ASP.NET Core](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub 'da nCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="67659-298">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="67659-299">Dağıtılmış önbellek, genellikle ona erişen uygulama sunucuları için bir dış hizmet olarak tutulan birden çok uygulama sunucusu tarafından paylaşılan bir önbellektir.</span><span class="sxs-lookup"><span data-stu-id="67659-299">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="67659-300">Dağıtılmış önbellek, özellikle uygulama bir bulut hizmeti veya sunucu grubu tarafından barındırıldığı zaman bir ASP.NET Core uygulamasının performansını ve ölçeklenebilirliğini iyileştirebilir.</span><span class="sxs-lookup"><span data-stu-id="67659-300">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="67659-301">Dağıtılmış bir önbellek, önbelleğe alınan verilerin ayrı uygulama sunucularında depolandığı diğer önbelleğe alma senaryolarından daha fazla avantaj sunar.</span><span class="sxs-lookup"><span data-stu-id="67659-301">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="67659-302">Önbelleğe alınan veriler dağıtıldığında, veriler:</span><span class="sxs-lookup"><span data-stu-id="67659-302">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="67659-303">Birden çok sunucuya yönelik istekler arasında *tutarlı (tutarlı* ).</span><span class="sxs-lookup"><span data-stu-id="67659-303">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="67659-304">Sunucu yeniden başlatmaları ve uygulama dağıtımları.</span><span class="sxs-lookup"><span data-stu-id="67659-304">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="67659-305">Yerel bellek kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="67659-305">Doesn't use local memory.</span></span>

<span data-ttu-id="67659-306">Dağıtılmış önbellek yapılandırması uygulamaya özgüdür.</span><span class="sxs-lookup"><span data-stu-id="67659-306">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="67659-307">Bu makalede SQL Server ve Redsıs dağıtılmış önbelleklerinin nasıl yapılandırılacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="67659-307">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="67659-308">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub üzerindeki nCache](https://github.com/Alachisoft/NCache)) gibi üçüncü taraf uygulamalar da mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="67659-308">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="67659-309">Uygulama hangi uygulamanın seçildiğine bakılmaksızın, arabirimi kullanılarak önbellek ile etkileşime girer <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="67659-309">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="67659-310">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="67659-310">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="67659-311">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="67659-311">Prerequisites</span></span>

<span data-ttu-id="67659-312">SQL Server dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="67659-312">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="67659-313">Redsıs dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [Microsoft. Extensions. Caching. redsıs](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="67659-313">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="67659-314">Redsıs paketi pakete dahil değildir, bu `Microsoft.AspNetCore.App` nedenle Redu paketine proje dosyanızda ayrı olarak başvurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="67659-314">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="67659-315">NCache dağıtılmış önbelleğini kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [nCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="67659-315">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="67659-316">NCache paketi pakete dahil değildir, bu `Microsoft.AspNetCore.App` nedenle, proje dosyanızda ayrı olarak NCache paketine başvurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="67659-316">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="67659-317">Idistributedcache arabirimi</span><span class="sxs-lookup"><span data-stu-id="67659-317">IDistributedCache interface</span></span>

<span data-ttu-id="67659-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Arabirim, dağıtılmış önbellek uygulamasındaki öğeleri işlemek için aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="67659-318">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="67659-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Bir dize anahtarını kabul eder ve önbellekte bulunursa önbelleğe alınmış bir öğeyi dizi olarak alır `byte[]` .</span><span class="sxs-lookup"><span data-stu-id="67659-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="67659-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` Bir dize anahtarı kullanarak önbelleğe bir öğe (dizi olarak) ekler.</span><span class="sxs-lookup"><span data-stu-id="67659-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="67659-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Önbellekte bir öğeyi anahtarını temel alarak yeniler, Kayan süre sonu zaman aşımını (varsa) sıfırlarken.</span><span class="sxs-lookup"><span data-stu-id="67659-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="67659-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Bir önbellek öğesini dize anahtarına göre kaldırır.</span><span class="sxs-lookup"><span data-stu-id="67659-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="67659-323">Dağıtılmış önbelleğe alma hizmetleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="67659-323">Establish distributed caching services</span></span>

<span data-ttu-id="67659-324">Uygulamasına bir uygulamasını kaydetme <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="67659-324">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="67659-325">Bu konuda açıklanan Framework tarafından sunulan uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="67659-325">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="67659-326">Dağıtılmış bellek önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-326">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="67659-327">Dağıtılmış SQL Server önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-327">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="67659-328">Dağıtılmış Redsıs önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-328">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="67659-329">Dağıtılmış NCache önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-329">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="67659-330">Dağıtılmış bellek önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-330">Distributed Memory Cache</span></span>

<span data-ttu-id="67659-331">Dağıtılmış bellek önbelleği ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ), öğeleri bellekte depolayan çerçeve tarafından sağlanmış bir uygulamasıdır <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="67659-331">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="67659-332">Dağıtılmış bellek önbelleği gerçek bir dağıtılmış önbellek değildir.</span><span class="sxs-lookup"><span data-stu-id="67659-332">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="67659-333">Önbelleğe alınan öğeler, uygulamanın çalıştığı sunucuda uygulama örneği tarafından depolanır.</span><span class="sxs-lookup"><span data-stu-id="67659-333">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="67659-334">Dağıtılmış bellek önbelleği, yararlı bir uygulama:</span><span class="sxs-lookup"><span data-stu-id="67659-334">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="67659-335">Geliştirme ve test senaryolarında.</span><span class="sxs-lookup"><span data-stu-id="67659-335">In development and testing scenarios.</span></span>
* <span data-ttu-id="67659-336">Üretimde tek bir sunucu kullanıldığında ve bellek tüketimi bir sorun değildir.</span><span class="sxs-lookup"><span data-stu-id="67659-336">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="67659-337">Dağıtılmış bellek önbelleğinin uygulanması, önbelleğe alınmış veri depolamayı soyutlar.</span><span class="sxs-lookup"><span data-stu-id="67659-337">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="67659-338">Birden çok düğüm veya hata toleransı gerekliyse, gelecekte doğru bir dağıtılmış önbelleğe alma çözümü uygulamaya olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="67659-338">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="67659-339">Örnek uygulama, uygulamasında geliştirme ortamında uygulama çalıştırıldığında dağıtılmış bellek önbelleğinin kullanımını sağlar `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67659-339">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="67659-340">Dağıtılmış SQL Server önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-340">Distributed SQL Server Cache</span></span>

<span data-ttu-id="67659-341">Dağıtılmış SQL Server önbellek uygulama ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) dağıtılmış önbelleğin, kendi yedekleme deposu olarak bir SQL Server veritabanı kullanmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="67659-341">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="67659-342">Bir SQL Server örneğinde önbelleğe alınmış SQL Server bir öğe tablosu oluşturmak için `sql-cache` aracını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="67659-342">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="67659-343">Araç, belirttiğiniz ad ve şemaya sahip bir tablo oluşturur.</span><span class="sxs-lookup"><span data-stu-id="67659-343">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="67659-344">Komutunu çalıştırarak SQL Server tablo oluşturun `sql-cache create` .</span><span class="sxs-lookup"><span data-stu-id="67659-344">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="67659-345">SQL Server örneği ( `Data Source` ), veritabanı (), `Initial Catalog` şema (örneğin,) `dbo` ve tablo adı (örneğin, `TestCache` ) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="67659-345">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="67659-346">Aracın başarılı olduğunu göstermek için bir ileti günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="67659-346">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="67659-347">Araç tarafından oluşturulan tablo `sql-cache` aşağıdaki şemaya sahiptir:</span><span class="sxs-lookup"><span data-stu-id="67659-347">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer önbellek tablosu](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="67659-349">Bir uygulama, bir örneğini kullanarak önbellek değerlerini işlemelidir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="67659-349">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="67659-350">Örnek uygulama, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> Içinde geliştirme dışı bir ortamda uygular `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67659-350">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="67659-351"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(Ve isteğe bağlı olarak, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> ve <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) genellikle kaynak denetimi dışında (örneğin, [gizli yönetici](xref:security/app-secrets) tarafından veya appSettings *üzerindeappsettings.js*depolanır / *. { ENVIRONMENT}. JSON* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="67659-351">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="67659-352">Bağlantı dizesinde kaynak denetim sistemlerinden tutulması gereken kimlik bilgileri bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="67659-352">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="67659-353">Dağıtılmış Redis Cache</span><span class="sxs-lookup"><span data-stu-id="67659-353">Distributed Redis Cache</span></span>

<span data-ttu-id="67659-354">[Redsıs](https://redis.io/) , genellikle dağıtılmış önbellek olarak kullanılan açık kaynaklı bir bellek içi veri deposudur.</span><span class="sxs-lookup"><span data-stu-id="67659-354">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="67659-355">Redsıs 'yi yerel olarak kullanabilir ve Azure 'da barındırılan ASP.NET Core uygulaması için bir [Azure Redis Cache](https://azure.microsoft.com/services/cache/) yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="67659-355">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="67659-356">Bir uygulama bir <xref:Microsoft.Extensions.Caching.Redis.RedisCache> örnek () kullanarak önbellek uygulamasını yapılandırır <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> :</span><span class="sxs-lookup"><span data-stu-id="67659-356">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="67659-357">Redsıs 'i yerel makinenize yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="67659-357">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="67659-358">[Chocolatey redsıs paketini](https://chocolatey.org/packages/redis-64/)yükler.</span><span class="sxs-lookup"><span data-stu-id="67659-358">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="67659-359">`redis-server`Komut isteminden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="67659-359">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="67659-360">Dağıtılmış NCache önbelleği</span><span class="sxs-lookup"><span data-stu-id="67659-360">Distributed NCache Cache</span></span>

<span data-ttu-id="67659-361">[NCache](https://github.com/Alachisoft/NCache) , .NET ve .NET Core 'da yerel olarak geliştirilen açık kaynaklı bellek içi dağıtılmış bir önbellektir.</span><span class="sxs-lookup"><span data-stu-id="67659-361">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="67659-362">NCache hem yerel olarak hem de Azure 'da veya diğer barındırma platformlarında çalışan bir ASP.NET Core uygulama için dağıtılmış önbellek kümesi olarak yapılandırılmış şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="67659-362">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="67659-363">Yerel makinenizde NCache 'i yüklemek ve yapılandırmak için bkz. [Windows Için nCache Başlangıç Kılavuzu](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="67659-363">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="67659-364">NCache 'yi yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="67659-364">To configure NCache:</span></span>

1. <span data-ttu-id="67659-365">[NCache açık kaynak NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="67659-365">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="67659-366">Cache kümesini [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)' de yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="67659-366">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="67659-367">Aşağıdaki kodu öğesine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67659-367">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="67659-368">Dağıtılmış önbelleği kullan</span><span class="sxs-lookup"><span data-stu-id="67659-368">Use the distributed cache</span></span>

<span data-ttu-id="67659-369">Arabirimini kullanmak için <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> uygulamadaki herhangi bir oluşturucudan bir örnek isteyin.</span><span class="sxs-lookup"><span data-stu-id="67659-369">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="67659-370">Örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="67659-370">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="67659-371">Örnek uygulama başlatıldığında <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> öğesine eklenir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="67659-371">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="67659-372">Şu anki süre kullanılarak önbelleğe alındı <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (daha fazla bilgi için bkz [. Web Host: ıapplicationlifetime arabirimi](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="67659-372">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="67659-373">Örnek uygulama <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , `IndexModel` Dizin sayfası tarafından kullanılmak üzere içine çıkarır.</span><span class="sxs-lookup"><span data-stu-id="67659-373">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="67659-374">Dizin sayfası her yüklendiğinde, önbellek, içindeki önbelleğe alınmış zamana göre denetlenir `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="67659-374">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="67659-375">Önbelleğe alınmış sürenin süresi dolmamışsa, zaman görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="67659-375">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="67659-376">Önbelleğe alınan saate son erişildiği zamandan bu yana 20 saniye geçtikten sonra (bu sayfanın son yüklenilişinde), sayfanın *önbelleğe alınma süresi doldu*.</span><span class="sxs-lookup"><span data-stu-id="67659-376">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="67659-377">Önbelleğe alınmış süreyi **Sıfırla** ' yı seçerek önbelleğe alınmış zamanı geçerli saate hemen güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="67659-377">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="67659-378">Düğme `OnPostResetCachedTime` işleyici metodunu tetikler.</span><span class="sxs-lookup"><span data-stu-id="67659-378">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="67659-379">Örnekler için tek veya kapsamlı bir yaşam süresi kullanmanız gerekmez <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (en azından yerleşik uygulamalar için).</span><span class="sxs-lookup"><span data-stu-id="67659-379">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="67659-380">Ayrıca <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , dı kullanmak yerine bir örnek oluşturabilirsiniz, ancak kodda bir örnek oluşturmak kodunuzu test etmek ve [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)ihlal etmek için daha zor hale gelebilir.</span><span class="sxs-lookup"><span data-stu-id="67659-380">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="67659-381">Öneriler</span><span class="sxs-lookup"><span data-stu-id="67659-381">Recommendations</span></span>

<span data-ttu-id="67659-382"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Uygulamanız için hangi uygulamanın en iyisi olduğuna karar verirken aşağıdakileri göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="67659-382">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="67659-383">Mevcut altyapı</span><span class="sxs-lookup"><span data-stu-id="67659-383">Existing infrastructure</span></span>
* <span data-ttu-id="67659-384">Performans gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="67659-384">Performance requirements</span></span>
* <span data-ttu-id="67659-385">Maliyet</span><span class="sxs-lookup"><span data-stu-id="67659-385">Cost</span></span>
* <span data-ttu-id="67659-386">Ekip deneyimi</span><span class="sxs-lookup"><span data-stu-id="67659-386">Team experience</span></span>

<span data-ttu-id="67659-387">Önbelleğe alma çözümleri genellikle önbelleğe alınmış verilerin hızlı bir şekilde alınmasını sağlamak için bellek içi depolamaya dayanır, ancak bellek sınırlı bir kaynaktır ve genişleyebilir.</span><span class="sxs-lookup"><span data-stu-id="67659-387">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="67659-388">Yalnızca yaygın olarak kullanılan verileri bir önbellekte depolayın.</span><span class="sxs-lookup"><span data-stu-id="67659-388">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="67659-389">Genellikle, Redsıs önbelleği daha yüksek aktarım hızı ve bir SQL Server önbelleğinden daha düşük gecikme süresi sağlar.</span><span class="sxs-lookup"><span data-stu-id="67659-389">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="67659-390">Bununla birlikte, genellikle önbelleğe alma stratejilerinin performans özelliklerini belirlemede bir değerlendirme gerekir.</span><span class="sxs-lookup"><span data-stu-id="67659-390">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="67659-391">SQL Server dağıtılmış önbellek yedekleme deposu olarak kullanıldığında, önbellek için aynı veritabanını kullanın ve uygulamanın sıradan veri depolama ve alma, her ikisinin performansını olumsuz yönde etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="67659-391">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="67659-392">Dağıtılmış önbellek yedekleme deposu için adanmış bir SQL Server örneği kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="67659-392">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="67659-393">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="67659-393">Additional resources</span></span>

* [<span data-ttu-id="67659-394">Azure üzerinde Redis Cache</span><span class="sxs-lookup"><span data-stu-id="67659-394">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="67659-395">Azure’da SQL Database</span><span class="sxs-lookup"><span data-stu-id="67659-395">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="67659-396">[Web çiftlerine nCache Için ıdistributedcache sağlayıcısını ASP.NET Core](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub 'da nCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="67659-396">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
 