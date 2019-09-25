---
title: ASP.NET Core bağımlılık ekleme
author: guardrex
description: ASP.NET Core bağımlılık ekleme ve nasıl kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/24/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: fefd0b9df71d5b0e7c30a31620292fd37eeecfa4
ms.sourcegitcommit: e54672f5c493258dc449fac5b98faf47eb123b28
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71248271"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="df073-103">ASP.NET Core bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="df073-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="df073-104">[Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="df073-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="df073-105">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="df073-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="df073-106">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="df073-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="df073-107">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="df073-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="df073-108">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="df073-108">Overview of dependency injection</span></span>

<span data-ttu-id="df073-109">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="df073-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="df073-110">Aşağıdaki `MyDependency` sınıfı, bir uygulamadaki diğer `WriteMessage` sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="df073-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="df073-111">Yöntemi bir sınıf için `MyDependency` kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir. `WriteMessage`</span><span class="sxs-lookup"><span data-stu-id="df073-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="df073-112">Sınıfı, `IndexModel` sınıfının bir bağımlılığı olur: `MyDependency`</span><span class="sxs-lookup"><span data-stu-id="df073-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="df073-113">Sınıf oluşturur ve `MyDependency` örneğe doğrudan bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="df073-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="df073-114">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="df073-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="df073-115">Farklı bir `MyDependency` uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="df073-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="df073-116">`MyDependency` Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="df073-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="df073-117">Uygulamasına bağlı olarak `MyDependency`, birden çok sınıfı olan büyük bir projede yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="df073-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="df073-118">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="df073-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="df073-119">Uygulamanın bu yaklaşım ile mümkün olmayan bir sahte `MyDependency` veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="df073-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="df073-120">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="df073-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="df073-121">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="df073-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="df073-122">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="df073-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="df073-123">ASP.NET Core, <xref:System.IServiceProvider>yerleşik bir hizmet kapsayıcısı sağlar.</span><span class="sxs-lookup"><span data-stu-id="df073-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="df073-124">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="df073-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="df073-125">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="df073-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="df073-126">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="df073-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="df073-127">[Örnek uygulamada](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="df073-127">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="df073-128">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="df073-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="df073-129">`MyDependency`kendi oluşturucusunda <xref:Microsoft.Extensions.Logging.ILogger`1> bir ister.</span><span class="sxs-lookup"><span data-stu-id="df073-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="df073-130">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="df073-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="df073-131">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="df073-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="df073-132">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="df073-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="df073-133">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="df073-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="df073-134">`IMyDependency`ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="df073-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="df073-135">`IMyDependency``Startup.ConfigureServices`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="df073-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="df073-136">`ILogger<TCategoryName>`günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .</span><span class="sxs-lookup"><span data-stu-id="df073-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="df073-137">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="df073-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

<span data-ttu-id="df073-138">Örnek uygulamada, `IMyDependency` hizmet somut tür `MyDependency`ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="df073-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="df073-139">Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar.</span><span class="sxs-lookup"><span data-stu-id="df073-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="df073-140">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="df073-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="df073-141">Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="df073-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="df073-142">Örneğin, `services.AddMvc()` Razor Pages ve MVC 'nin gerektirdiği Hizmetleri ekler.</span><span class="sxs-lookup"><span data-stu-id="df073-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="df073-143">Uygulamaların bu kuralı izlemesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="df073-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="df073-144">Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="df073-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="df073-145">Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table) `string`gerektiriyorsa, tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="df073-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="df073-146">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="df073-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="df073-147">Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="df073-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="df073-148">Örnek uygulamada, `IMyDependency` örnek istenir ve `WriteMessage` hizmetin yöntemini çağırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="df073-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

## <a name="services-injected-into-startup"></a><span data-ttu-id="df073-149">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="df073-149">Services injected into Startup</span></span>

<span data-ttu-id="df073-150">Genel ana bilgisayar ( `Startup` <xref:Microsoft.Extensions.Hosting.IHostBuilder>) kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="df073-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="df073-151">Hizmetler şu şekilde eklenebilir `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="df073-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="df073-152">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="df073-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="df073-153">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="df073-153">Framework-provided services</span></span>

<span data-ttu-id="df073-154">`Startup.ConfigureServices` Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="df073-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="df073-155">Başlangıçta, için `IServiceCollection` `ConfigureServices` belirtilen, [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="df073-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="df073-156">Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="df073-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="df073-157">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="df073-157">A small sample of framework-registered services is listed in the following table.</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="df073-158">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="df073-158">Service Type</span></span> | <span data-ttu-id="df073-159">Ömür</span><span class="sxs-lookup"><span data-stu-id="df073-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="df073-160">Geçici</span><span class="sxs-lookup"><span data-stu-id="df073-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="df073-161">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="df073-162">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="df073-163">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="df073-164">Geçici</span><span class="sxs-lookup"><span data-stu-id="df073-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="df073-165">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="df073-166">Geçici</span><span class="sxs-lookup"><span data-stu-id="df073-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="df073-167">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="df073-168">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="df073-169">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="df073-170">Geçici</span><span class="sxs-lookup"><span data-stu-id="df073-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="df073-171">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="df073-172">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="df073-173">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-173">Singleton</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="df073-174">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="df073-174">Service Type</span></span> | <span data-ttu-id="df073-175">Ömür</span><span class="sxs-lookup"><span data-stu-id="df073-175">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="df073-176">Geçici</span><span class="sxs-lookup"><span data-stu-id="df073-176">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="df073-177">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-177">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="df073-178">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-178">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="df073-179">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-179">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="df073-180">Geçici</span><span class="sxs-lookup"><span data-stu-id="df073-180">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="df073-181">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-181">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="df073-182">Geçici</span><span class="sxs-lookup"><span data-stu-id="df073-182">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="df073-183">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-183">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="df073-184">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-184">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="df073-185">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-185">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="df073-186">Geçici</span><span class="sxs-lookup"><span data-stu-id="df073-186">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="df073-187">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-187">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="df073-188">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-188">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="df073-189">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-189">Singleton</span></span> |

::: moniker-end

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="df073-190">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="df073-190">Register additional services with extension methods</span></span>

<span data-ttu-id="df073-191">Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek `Add{SERVICE_NAME}` bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="df073-191">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="df073-192">Aşağıdaki kod, <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> [adddbcontext\<tcontext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir:</span><span class="sxs-lookup"><span data-stu-id="df073-192">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="df073-193">Daha fazla bilgi için API belgelerindeki <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> sınıfına bakın.</span><span class="sxs-lookup"><span data-stu-id="df073-193">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="df073-194">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="df073-194">Service lifetimes</span></span>

<span data-ttu-id="df073-195">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="df073-195">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="df073-196">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="df073-196">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="df073-197">Geçici</span><span class="sxs-lookup"><span data-stu-id="df073-197">Transient</span></span>

<span data-ttu-id="df073-198">Geçici ömür Hizmetleri (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="df073-198">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="df073-199">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="df073-199">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="df073-200">Yayıl</span><span class="sxs-lookup"><span data-stu-id="df073-200">Scoped</span></span>

<span data-ttu-id="df073-201">Kapsamlı ömür Hizmetleri (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="df073-201">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="df073-202">Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="df073-202">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="df073-203">Oluşturucu ekleme yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor.</span><span class="sxs-lookup"><span data-stu-id="df073-203">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="df073-204">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="df073-204">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="df073-205">Adet</span><span class="sxs-lookup"><span data-stu-id="df073-205">Singleton</span></span>

<span data-ttu-id="df073-206">Tek yaşam süresi Hizmetleri<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>() her istendiğinde oluşturulur ( `Startup.ConfigureServices` veya çalıştırıldığında ve hizmet kaydıyla bir örnek belirtildiğinde).</span><span class="sxs-lookup"><span data-stu-id="df073-206">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="df073-207">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="df073-207">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="df073-208">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir.</span><span class="sxs-lookup"><span data-stu-id="df073-208">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="df073-209">Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="df073-209">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="df073-210">Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="df073-210">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="df073-211">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="df073-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="df073-212">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="df073-212">Service registration methods</span></span>

<span data-ttu-id="df073-213">Her hizmet kayıt uzantısı yöntemi, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="df073-213">Each service registration extension method offers overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="df073-214">Yöntem</span><span class="sxs-lookup"><span data-stu-id="df073-214">Method</span></span> | <span data-ttu-id="df073-215">Otomatik</span><span class="sxs-lookup"><span data-stu-id="df073-215">Automatic</span></span><br><span data-ttu-id="df073-216">nesne</span><span class="sxs-lookup"><span data-stu-id="df073-216">object</span></span><br><span data-ttu-id="df073-217">elden</span><span class="sxs-lookup"><span data-stu-id="df073-217">disposal</span></span> | <span data-ttu-id="df073-218">Birden Çok</span><span class="sxs-lookup"><span data-stu-id="df073-218">Multiple</span></span><br><span data-ttu-id="df073-219">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="df073-219">implementations</span></span> | <span data-ttu-id="df073-220">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="df073-220">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="df073-221">Örnek:</span><span class="sxs-lookup"><span data-stu-id="df073-221">Example:</span></span><br>`services.AddScoped<IMyDep, MyDep>();` | <span data-ttu-id="df073-222">Evet</span><span class="sxs-lookup"><span data-stu-id="df073-222">Yes</span></span> | <span data-ttu-id="df073-223">Evet</span><span class="sxs-lookup"><span data-stu-id="df073-223">Yes</span></span> | <span data-ttu-id="df073-224">Hayır</span><span class="sxs-lookup"><span data-stu-id="df073-224">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="df073-225">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="df073-225">Examples:</span></span><br>`services.AddScoped<IMyDep>(sp => new MyDep());`<br>`services.AddScoped<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="df073-226">Evet</span><span class="sxs-lookup"><span data-stu-id="df073-226">Yes</span></span> | <span data-ttu-id="df073-227">Evet</span><span class="sxs-lookup"><span data-stu-id="df073-227">Yes</span></span> | <span data-ttu-id="df073-228">Evet</span><span class="sxs-lookup"><span data-stu-id="df073-228">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="df073-229">Örnek:</span><span class="sxs-lookup"><span data-stu-id="df073-229">Example:</span></span><br>`services.AddScoped<MyDep>();` | <span data-ttu-id="df073-230">Evet</span><span class="sxs-lookup"><span data-stu-id="df073-230">Yes</span></span> | <span data-ttu-id="df073-231">Hayır</span><span class="sxs-lookup"><span data-stu-id="df073-231">No</span></span> | <span data-ttu-id="df073-232">Hayır</span><span class="sxs-lookup"><span data-stu-id="df073-232">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="df073-233">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="df073-233">Examples:</span></span><br>`services.AddScoped<IMyDep>(new MyDep());`<br>`services.AddScoped<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="df073-234">Hayır</span><span class="sxs-lookup"><span data-stu-id="df073-234">No</span></span> | <span data-ttu-id="df073-235">Evet</span><span class="sxs-lookup"><span data-stu-id="df073-235">Yes</span></span> | <span data-ttu-id="df073-236">Evet</span><span class="sxs-lookup"><span data-stu-id="df073-236">Yes</span></span> |
| `Add{LIFETIME}(new {IMPLEMENTATION})`<br><span data-ttu-id="df073-237">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="df073-237">Examples:</span></span><br>`services.AddScoped(new MyDep());`<br>`services.AddScoped(new MyDep("A string!"));` | <span data-ttu-id="df073-238">Hayır</span><span class="sxs-lookup"><span data-stu-id="df073-238">No</span></span> | <span data-ttu-id="df073-239">Hayır</span><span class="sxs-lookup"><span data-stu-id="df073-239">No</span></span> | <span data-ttu-id="df073-240">Evet</span><span class="sxs-lookup"><span data-stu-id="df073-240">Yes</span></span> |

<span data-ttu-id="df073-241">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="df073-241">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="df073-242">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="df073-242">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="df073-243">`TryAdd{LIFETIME}`Yöntemler, zaten kayıtlı bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="df073-243">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="df073-244">Aşağıdaki örnekte, ilk satır için `MyDependency` `IMyDependency`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="df073-244">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="df073-245">Zaten kayıtlı bir uygulamaya sahip olduğundan `IMyDependency` ikinci satır etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="df073-245">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="df073-246">Daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="df073-246">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="df073-247">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="df073-247">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="df073-248">Aracılığıyla `IEnumerable<{SERVICE}>`birden çok hizmet çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="df073-248">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="df073-249">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir.</span><span class="sxs-lookup"><span data-stu-id="df073-249">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="df073-250">Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="df073-250">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="df073-251">Aşağıdaki örnekte, ilk satır için `MyDep` `IMyDep1`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="df073-251">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="df073-252">İkinci satır için `IMyDep2`kaydedilir `MyDep` .</span><span class="sxs-lookup"><span data-stu-id="df073-252">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="df073-253">Zaten kayıtlı bir `IMyDep1` `MyDep`uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="df073-253">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="df073-254">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="df073-254">Constructor injection behavior</span></span>

<span data-ttu-id="df073-255">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="df073-255">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="df073-256"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="df073-256"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="df073-257">`ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="df073-257">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="df073-258">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="df073-258">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="df073-259">Hizmetler veya `IServiceProvider` `ActivatorUtilities`tarafından çözümlendiğinde, Oluşturucu Ekleme *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="df073-259">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="df073-260">Hizmetler tarafından `ActivatorUtilities`çözümlendiğinde, Oluşturucu ekleme yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="df073-260">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="df073-261">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="df073-261">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="df073-262">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="df073-262">Entity Framework contexts</span></span>

<span data-ttu-id="df073-263">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="df073-263">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="df073-264">Veritabanı bağlamı kaydedilirken aşırı yükleme [> bir\<adddbcontext tcontext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) tarafından belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="df073-264">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="df073-265">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="df073-265">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="df073-266">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="df073-266">Lifetime and registration options</span></span>

<span data-ttu-id="df073-267">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya `OperationId`sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="df073-267">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="df073-268">Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="df073-268">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="df073-269">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="df073-269">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="df073-270">Bir tane sağlanmazsa Oluşturucu bir GUID oluşturur: `Operation`</span><span class="sxs-lookup"><span data-stu-id="df073-270">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="df073-271">`OperationService` , Diğer`Operation` türlerin her birine bağlı olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="df073-271">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="df073-272">`OperationService` Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="df073-272">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="df073-273">Kapsayıcıda istendiğinde `OperationId` geçici hizmetler oluşturulduğunda, `IOperationTransient` `OperationId` hizmet öğesinden `OperationService`farklı olur.</span><span class="sxs-lookup"><span data-stu-id="df073-273">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="df073-274">`OperationService``IOperationTransient` sınıfının yeni bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="df073-274">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="df073-275">Yeni örnek farklı `OperationId`bir şekilde oluşturur.</span><span class="sxs-lookup"><span data-stu-id="df073-275">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="df073-276">İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği `OperationService` içindeki ile aynı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="df073-276">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="df073-277">İstemci istekleri arasında her iki hizmet de farklı `OperationId` bir değer paylaşır.</span><span class="sxs-lookup"><span data-stu-id="df073-277">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="df073-278">Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde `OperationId` kullanıldığında, tüm hizmet istekleri genelinde sabittir.</span><span class="sxs-lookup"><span data-stu-id="df073-278">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="df073-279">' `Startup.ConfigureServices`De, her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="df073-279">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

<span data-ttu-id="df073-280">Hizmet, bilinen `Guid.Empty`kimliği olan belirli bir örnek kullanıyor. `IOperationSingletonInstance`</span><span class="sxs-lookup"><span data-stu-id="df073-280">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="df073-281">Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.</span><span class="sxs-lookup"><span data-stu-id="df073-281">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="df073-282">Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="df073-282">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="df073-283">Örnek uygulama `IndexModel` , her `IOperation` tür ve ' i `OperationService`ister.</span><span class="sxs-lookup"><span data-stu-id="df073-283">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="df073-284">Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="df073-284">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

<span data-ttu-id="df073-285">Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="df073-285">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="df073-286">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="df073-286">**First request:**</span></span>

<span data-ttu-id="df073-287">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="df073-287">Controller operations:</span></span>

<span data-ttu-id="df073-288">Geçici: d233e165-f417-469B-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="df073-288">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="df073-289">Yayıl 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="df073-289">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="df073-290">Adet 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="df073-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="df073-291">Instance 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="df073-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="df073-292">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="df073-292">`OperationService` operations:</span></span>

<span data-ttu-id="df073-293">Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="df073-293">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="df073-294">Yayıl 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="df073-294">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="df073-295">Adet 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="df073-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="df073-296">Instance 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="df073-296">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="df073-297">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="df073-297">**Second request:**</span></span>

<span data-ttu-id="df073-298">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="df073-298">Controller operations:</span></span>

<span data-ttu-id="df073-299">Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="df073-299">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="df073-300">Yayıl 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="df073-300">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="df073-301">Adet 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="df073-301">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="df073-302">Instance 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="df073-302">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="df073-303">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="df073-303">`OperationService` operations:</span></span>

<span data-ttu-id="df073-304">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="df073-304">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="df073-305">Yayıl 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="df073-305">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="df073-306">Adet 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="df073-306">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="df073-307">Instance 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="df073-307">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="df073-308">`OperationId` Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="df073-308">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="df073-309">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="df073-309">*Transient* objects are always different.</span></span> <span data-ttu-id="df073-310">Hem birinci `OperationId` hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="df073-310">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="df073-311">Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="df073-311">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="df073-312">*Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="df073-312">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="df073-313">*Tek* nesneler, ' de `Operation` `Startup.ConfigureServices`bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır.</span><span class="sxs-lookup"><span data-stu-id="df073-313">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="df073-314">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="df073-314">Call services from main</span></span>

<span data-ttu-id="df073-315">Uygulamanın kapsamındaki <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> bir kapsamlı hizmeti çözümlemek için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="df073-315">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="df073-316">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="df073-316">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="df073-317">Aşağıdaki örnek, `MyScopedService` içinde `Program.Main`için nasıl bağlam alınacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="df073-317">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

::: moniker-end

## <a name="scope-validation"></a><span data-ttu-id="df073-318">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="df073-318">Scope validation</span></span>

<span data-ttu-id="df073-319">Uygulama geliştirme ortamında çalışırken, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="df073-319">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="df073-320">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="df073-320">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="df073-321">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="df073-321">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="df073-322">Kök hizmet sağlayıcısı çağrıldığında oluşturulur <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> .</span><span class="sxs-lookup"><span data-stu-id="df073-322">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="df073-323">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="df073-323">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="df073-324">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="df073-324">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="df073-325">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="df073-325">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="df073-326">Hizmet kapsamlarını doğrulamak `BuildServiceProvider` , çağrıldığında bu durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="df073-326">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="df073-327">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="df073-327">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="df073-328">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="df073-328">Request Services</span></span>

<span data-ttu-id="df073-329">ASP.NET Core isteği `HttpContext` içinde bulunan hizmetler, [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="df073-329">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="df073-330">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="df073-330">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="df073-331">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de `RequestServices` `ApplicationServices`bulunan türler tarafından karşılanır.</span><span class="sxs-lookup"><span data-stu-id="df073-331">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="df073-332">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="df073-332">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="df073-333">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="df073-333">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="df073-334">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="df073-334">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="df073-335">`RequestServices` Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin.</span><span class="sxs-lookup"><span data-stu-id="df073-335">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="df073-336">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="df073-336">Design services for dependency injection</span></span>

<span data-ttu-id="df073-337">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="df073-337">Best practices are to:</span></span>

* <span data-ttu-id="df073-338">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="df073-338">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="df073-339">Durum bilgisi olan statik yöntem çağrılarından kaçının.</span><span class="sxs-lookup"><span data-stu-id="df073-339">Avoid stateful, static method calls.</span></span>
* <span data-ttu-id="df073-340">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="df073-340">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="df073-341">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="df073-341">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="df073-342">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="df073-342">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="df073-343">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır.</span><span class="sxs-lookup"><span data-stu-id="df073-343">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="df073-344">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="df073-344">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="df073-345">Razor Pages sayfa modeli sınıfları ve MVC denetleyici sınıflarının UI kaygılarıyla odaklanıp ilgilenmeyeceğini aklınızda bulundurun.</span><span class="sxs-lookup"><span data-stu-id="df073-345">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="df073-346">İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="df073-346">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="df073-347">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="df073-347">Disposal of services</span></span>

<span data-ttu-id="df073-348">Kapsayıcı, oluşturduğu <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="df073-348">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="df073-349">Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.</span><span class="sxs-lookup"><span data-stu-id="df073-349">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

```csharp
// Services that implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public interface ISomeService {}
public class SomeServiceImplementation : ISomeService, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // The container creates the following instances and disposes them automatically:
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<ISomeService>(sp => new SomeServiceImplementation());

    // The container doesn't create the following instances, so it doesn't dispose of
    // the instances automatically:
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="df073-350">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="df073-350">Default service container replacement</span></span>

<span data-ttu-id="df073-351">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="df073-351">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="df073-352">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="df073-352">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="df073-353">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="df073-353">Property injection</span></span>
* <span data-ttu-id="df073-354">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="df073-354">Injection based on name</span></span>
* <span data-ttu-id="df073-355">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="df073-355">Child containers</span></span>
* <span data-ttu-id="df073-356">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="df073-356">Custom lifetime management</span></span>
* <span data-ttu-id="df073-357">`Func<T>`yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="df073-357">`Func<T>` support for lazy initialization</span></span>

<span data-ttu-id="df073-358">Aşağıdaki 3. taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="df073-358">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="df073-359">Autofac</span><span class="sxs-lookup"><span data-stu-id="df073-359">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="df073-360">Drıioc</span><span class="sxs-lookup"><span data-stu-id="df073-360">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="df073-361">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="df073-361">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="df073-362">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="df073-362">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="df073-363">E</span><span class="sxs-lookup"><span data-stu-id="df073-363">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="df073-364">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="df073-364">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="df073-365">Unity</span><span class="sxs-lookup"><span data-stu-id="df073-365">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="df073-366">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="df073-366">Thread safety</span></span>

<span data-ttu-id="df073-367">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="df073-367">Create thread-safe singleton services.</span></span> <span data-ttu-id="df073-368">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="df073-368">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="df073-369">Tek bir hizmetin fabrika yöntemi (örneğin, [AddSingleton\<TService > için ikinci bağımsız değişken) (ıseviecollection,\<Func IServiceProvider, TService >)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="df073-369">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="df073-370">Bir Type (`static`) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="df073-370">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="df073-371">Öneriler</span><span class="sxs-lookup"><span data-stu-id="df073-371">Recommendations</span></span>

* <span data-ttu-id="df073-372">`async/await`ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="df073-372">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="df073-373">C#zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="df073-373">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="df073-374">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="df073-374">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="df073-375">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="df073-375">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="df073-376">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="df073-376">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="df073-377">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="df073-377">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="df073-378">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="df073-378">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="df073-379">Hizmetlere statik erişimi önleyin (örneğin, başka bir yerde kullanmak üzere, statik olarak yazılan [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ).</span><span class="sxs-lookup"><span data-stu-id="df073-379">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="df073-380">*Hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="df073-380">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="df073-381">Örneğin, yerine şunu kullandığınızda <xref:System.IServiceProvider.GetService*> bir hizmet örneği elde etme çağrısı yapmayın:</span><span class="sxs-lookup"><span data-stu-id="df073-381">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="df073-382">**Olmayan**</span><span class="sxs-lookup"><span data-stu-id="df073-382">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="df073-383">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="df073-383">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* <span data-ttu-id="df073-384">Önlemek için başka bir hizmet bulucu çeşitlemesi, çalışma zamanında bağımlılıkları çözümleyen bir ekleme.</span><span class="sxs-lookup"><span data-stu-id="df073-384">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="df073-385">Bu uygulamalardan her ikisi de [Denetim stratejilerini geçersiz kılar](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="df073-385">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="df073-386">Uygulamasına `HttpContext` statik erişimi önleyin (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="df073-386">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="df073-387">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="df073-387">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="df073-388">Özel durumlar&mdash;genellikle çerçevenin kendisi içinde özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="df073-388">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="df073-389">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="df073-389">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="df073-390">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="df073-390">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="df073-391">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="df073-391">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="df073-392">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="df073-392">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="df073-393">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="df073-393">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="df073-394">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="df073-394">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="df073-395">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="df073-395">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
