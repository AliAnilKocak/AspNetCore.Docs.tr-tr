---
<span data-ttu-id="4972d-101">Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: guardrex açıklaması: ' Blazor uygulamaların bileşenleri bileşenlere nasıl ekleyebilmesi için bkz..</span><span class="sxs-lookup"><span data-stu-id="4972d-101">title: 'ASP.NET Core Blazor dependency injection' author: guardrex description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="4972d-102">monikerRange: ' >= aspnetcore-3,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 05/19/2020 No-loc:</span><span class="sxs-lookup"><span data-stu-id="4972d-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/19/2020 no-loc:</span></span>
- <span data-ttu-id="4972d-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4972d-103">'Blazor'</span></span>
- <span data-ttu-id="4972d-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4972d-104">'Identity'</span></span>
- <span data-ttu-id="4972d-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4972d-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="4972d-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4972d-106">'Razor'</span></span>
- <span data-ttu-id="4972d-107">' SignalR ' uid: blazor/Dependency-Injection</span><span class="sxs-lookup"><span data-stu-id="4972d-107">'SignalR' uid: blazor/dependency-injection</span></span>

---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="4972d-108">ASP.NET Core Blazor bağımlılığı ekleme</span><span class="sxs-lookup"><span data-stu-id="4972d-108">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="4972d-109">Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Mike rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="4972d-109">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="4972d-110">[bağımlılık ekleme işlemini (dı)](xref:fundamentals/dependency-injection)destekler.</span><span class="sxs-lookup"><span data-stu-id="4972d-110"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4972d-111">Uygulamalar, yerleşik hizmetleri ekleme tarafından bileşenlere kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-111">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="4972d-112">Uygulamalar Ayrıca özel hizmetleri tanımlayabilir ve kaydedebilir ve bu Hizmetleri uygulama genelinde DI aracılığıyla kullanılabilir hale getirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4972d-112">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="4972d-113">DI, merkezi bir konumda yapılandırılmış hizmetlere erişmek için bir tekniktir.</span><span class="sxs-lookup"><span data-stu-id="4972d-113">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="4972d-114">Bu, Blazor uygulamalarda şunları yapmak için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="4972d-114">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="4972d-115">Hizmet sınıfının tek bir *örneğini tek bir hizmet olarak* bilinen birçok bileşen arasında paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4972d-115">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="4972d-116">Başvuru soyutlamalarını kullanarak somut hizmet sınıflarından bileşenleri ayırın.</span><span class="sxs-lookup"><span data-stu-id="4972d-116">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="4972d-117">Örneğin, `IDataAccess` uygulamadaki verilere erişim için bir arabirim düşünün.</span><span class="sxs-lookup"><span data-stu-id="4972d-117">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="4972d-118">Arabirim somut bir sınıf tarafından uygulanır `DataAccess` ve uygulamanın hizmet kapsayıcısında bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-118">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="4972d-119">Bir bileşen bir uygulama almak için DI kullandığında `IDataAccess` , bileşen somut tür ile eşleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="4972d-119">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="4972d-120">Uygulama, büyük olasılıkla birim testlerinde bir sahte uygulama için değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-120">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="4972d-121">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="4972d-121">Default services</span></span>

<span data-ttu-id="4972d-122">Varsayılan hizmetler, uygulamanın hizmet koleksiyonuna otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="4972d-122">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="4972d-123">Hizmet</span><span class="sxs-lookup"><span data-stu-id="4972d-123">Service</span></span> | <span data-ttu-id="4972d-124">Ömür</span><span class="sxs-lookup"><span data-stu-id="4972d-124">Lifetime</span></span> | <span data-ttu-id="4972d-125">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4972d-125">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="4972d-126">Larsa</span><span class="sxs-lookup"><span data-stu-id="4972d-126">Transient</span></span> | <span data-ttu-id="4972d-127">HTTP istekleri göndermek ve bir URI tarafından tanımlanan bir kaynaktan HTTP yanıtlarını almak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="4972d-127">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="4972d-128"><xref:System.Net.Http.HttpClient> Blazor Webassembly uygulamasındaki örneği, arka planda HTTP trafiğini işlemek için tarayıcıyı kullanır.</span><span class="sxs-lookup"><span data-stu-id="4972d-128">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor<span data-ttu-id="4972d-129">Sunucu uygulamaları <xref:System.Net.Http.HttpClient> Varsayılan olarak yapılandırılmış bir hizmet olarak yapılandırma içermez.</span><span class="sxs-lookup"><span data-stu-id="4972d-129"> Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="4972d-130">Bir <xref:System.Net.Http.HttpClient> Blazor sunucu uygulamasına bir sağlar.</span><span class="sxs-lookup"><span data-stu-id="4972d-130">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="4972d-131">Daha fazla bilgi için bkz. <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="4972d-131">For more information, see <xref:blazor/call-web-api>.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="4972d-132">Singleton ( Blazor webassembly)</span><span class="sxs-lookup"><span data-stu-id="4972d-132">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="4972d-133">Kapsamlı ( Blazor sunucu)</span><span class="sxs-lookup"><span data-stu-id="4972d-133">Scoped (Blazor Server)</span></span> | <span data-ttu-id="4972d-134">JavaScript çağrılarının dağıtıldığı bir JavaScript çalışma zamanının örneğini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="4972d-134">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="4972d-135">Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="4972d-135">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="4972d-136">Singleton ( Blazor webassembly)</span><span class="sxs-lookup"><span data-stu-id="4972d-136">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="4972d-137">Kapsamlı ( Blazor sunucu)</span><span class="sxs-lookup"><span data-stu-id="4972d-137">Scoped (Blazor Server)</span></span> | <span data-ttu-id="4972d-138">URI 'Ler ve gezinme durumu ile çalışmaya yönelik yardımcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="4972d-138">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="4972d-139">Daha fazla bilgi için bkz. [URI ve gezinti durumu yardımcıları](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="4972d-139">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="4972d-140">Özel bir hizmet sağlayıcı, tabloda listelenen varsayılan Hizmetleri otomatik olarak sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="4972d-140">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="4972d-141">Özel bir hizmet sağlayıcısı kullanır ve tabloda gösterilen hizmetlerden herhangi birini gerekliyse, gerekli hizmetleri yeni hizmet sağlayıcısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4972d-141">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="4972d-142">Uygulamaya hizmet ekleme</span><span class="sxs-lookup"><span data-stu-id="4972d-142">Add services to an app</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="4972d-143">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="4972d-143"> WebAssembly</span></span>

<span data-ttu-id="4972d-144">Program.cs yönteminde uygulamanın hizmet koleksiyonu için Hizmetleri yapılandırın `Main` . *Program.cs*</span><span class="sxs-lookup"><span data-stu-id="4972d-144">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="4972d-145">Aşağıdaki örnekte, `MyDependency` uygulama için kaydedilir `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="4972d-145">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="4972d-146">Konak oluşturulduktan sonra, herhangi bir bileşen işlenmeden önce kök dı kapsamından hizmetlere erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-146">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="4972d-147">Bu, içerik işlemeden önce başlatma mantığını çalıştırmak için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="4972d-147">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="4972d-148">Konak, uygulama için bir merkezi yapılandırma örneği de sağlar.</span><span class="sxs-lookup"><span data-stu-id="4972d-148">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="4972d-149">Yukarıdaki örnekte derleme yaparken, hava durumu hizmetinin URL 'SI varsayılan bir yapılandırma kaynağından (örneğin, *appSettings. JSON*) geçirilir `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="4972d-149">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a>Blazor<span data-ttu-id="4972d-150">Server</span><span class="sxs-lookup"><span data-stu-id="4972d-150"> Server</span></span>

<span data-ttu-id="4972d-151">Yeni bir uygulama oluşturduktan sonra, yöntemini inceleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4972d-151">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="4972d-152"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Yöntemi <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , hizmet açıklayıcı nesnelerinin bir listesi olan bir ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ) iletilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-152">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="4972d-153">Hizmetler, hizmet koleksiyonuna hizmet tanımlayıcıları sağlayarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="4972d-153">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="4972d-154">Aşağıdaki örnek, `IDataAccess` arabirimini ve somut uygulamasını içeren kavramı gösterir `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="4972d-154">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="4972d-155">Hizmet ömrü</span><span class="sxs-lookup"><span data-stu-id="4972d-155">Service lifetime</span></span>

<span data-ttu-id="4972d-156">Hizmetler, aşağıdaki tabloda gösterilen ömürlerle yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-156">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="4972d-157">Ömür</span><span class="sxs-lookup"><span data-stu-id="4972d-157">Lifetime</span></span> | <span data-ttu-id="4972d-158">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4972d-158">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor<span data-ttu-id="4972d-159">WebAssembly uygulamalarında Şu anda bir dı kapsamları kavramı yoktur.</span><span class="sxs-lookup"><span data-stu-id="4972d-159"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="4972d-160">`Scoped`-kayıtlı hizmetler hizmetler gibi davranır `Singleton` .</span><span class="sxs-lookup"><span data-stu-id="4972d-160">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="4972d-161">Ancak, Blazor sunucu barındırma modeli `Scoped` yaşam süresini destekler.</span><span class="sxs-lookup"><span data-stu-id="4972d-161">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="4972d-162">BlazorSunucu uygulamalarında, kapsamlı bir hizmet kaydı *bağlantının*kapsamına alınır.</span><span class="sxs-lookup"><span data-stu-id="4972d-162">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="4972d-163">Bu nedenle, geçerli amaç tarayıcıda istemci tarafı çalıştırmak olsa bile, kapsama alınmış hizmetlerin kullanılması geçerli kullanıcı kapsamında olması gereken hizmetler için tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-163">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="4972d-164">Dı, hizmetin *tek bir örneğini* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4972d-164">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="4972d-165">Hizmet gerektiren tüm bileşenler `Singleton` aynı hizmetin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="4972d-165">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="4972d-166">Bir bileşen hizmet kapsayıcısından bir hizmetin örneğini edindiğinde `Transient` , hizmetin *Yeni bir örneğini* alır.</span><span class="sxs-lookup"><span data-stu-id="4972d-166">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="4972d-167">Dı sistemi ASP.NET Core içindeki DI sistemini temel alır.</span><span class="sxs-lookup"><span data-stu-id="4972d-167">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="4972d-168">Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="4972d-168">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="4972d-169">Bir bileşende hizmet isteme</span><span class="sxs-lookup"><span data-stu-id="4972d-169">Request a service in a component</span></span>

<span data-ttu-id="4972d-170">Hizmetler hizmet koleksiyonuna eklendikten sonra, [ \@ ekleme](xref:mvc/views/razor#inject) yönergesini kullanarak hizmetleri bileşenlere ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="4972d-170">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="4972d-171">[`@inject`](xref:mvc/views/razor#inject)iki parametreye sahiptir:</span><span class="sxs-lookup"><span data-stu-id="4972d-171">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="4972d-172">Tür: eklenecek hizmetin türü.</span><span class="sxs-lookup"><span data-stu-id="4972d-172">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="4972d-173">Özellik: eklenen App Service 'i alan özelliğin adı.</span><span class="sxs-lookup"><span data-stu-id="4972d-173">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="4972d-174">Özelliği el ile oluşturma gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="4972d-174">The property doesn't require manual creation.</span></span> <span data-ttu-id="4972d-175">Derleyici özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4972d-175">The compiler creates the property.</span></span>

<span data-ttu-id="4972d-176">Daha fazla bilgi için bkz. <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="4972d-176">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="4972d-177">[`@inject`](xref:mvc/views/razor#inject)Farklı hizmetler eklemek için birden çok deyim kullanın.</span><span class="sxs-lookup"><span data-stu-id="4972d-177">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="4972d-178">Aşağıdaki örnek nasıl kullanılacağını göstermektedir [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="4972d-178">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="4972d-179">Uygulayan hizmet `Services.IDataAccess` bileşenin özelliğine eklenir `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="4972d-179">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="4972d-180">Kodun yalnızca soyutlamayı nasıl kullandığını aklınızda yapın `IDataAccess` :</span><span class="sxs-lookup"><span data-stu-id="4972d-180">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="4972d-181">Dahili olarak, oluşturulan Özellik ( `DataRepository` ) özniteliğini kullanır [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) .</span><span class="sxs-lookup"><span data-stu-id="4972d-181">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="4972d-182">Genellikle, bu öznitelik doğrudan kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="4972d-182">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="4972d-183">Bileşenler için bir temel sınıf gerekliyse ve temel sınıf için eklenen özellikler de gerekliyse, özniteliği el ile ekleyin [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) :</span><span class="sxs-lookup"><span data-stu-id="4972d-183">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="4972d-184">Temel sınıftan türetilmiş bileşenlerde, [`@inject`](xref:mvc/views/razor#inject) yönergesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="4972d-184">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="4972d-185"><xref:Microsoft.AspNetCore.Components.InjectAttribute>Taban sınıfının yeterli olması yeterlidir:</span><span class="sxs-lookup"><span data-stu-id="4972d-185">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="4972d-186">Hizmetler 'de dı kullanma</span><span class="sxs-lookup"><span data-stu-id="4972d-186">Use DI in services</span></span>

<span data-ttu-id="4972d-187">Karmaşık hizmetler için ek hizmetler gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-187">Complex services might require additional services.</span></span> <span data-ttu-id="4972d-188">Önceki örnekte, `DataAccess` <xref:System.Net.Http.HttpClient> varsayılan hizmet gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-188">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="4972d-189">[`@inject`](xref:mvc/views/razor#inject)(veya [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) özniteliği) hizmetlerde kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="4972d-189">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="4972d-190">Bunun yerine *Oluşturucu Ekleme* kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4972d-190">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="4972d-191">Gerekli hizmetler, hizmetin oluşturucusuna parametreler eklenerek eklenir.</span><span class="sxs-lookup"><span data-stu-id="4972d-191">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="4972d-192">Dı hizmeti oluşturduğunda, oluşturucuda gereken hizmetleri algılar ve bunlara göre sağlar.</span><span class="sxs-lookup"><span data-stu-id="4972d-192">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="4972d-193">Oluşturucu Ekleme önkoşulları:</span><span class="sxs-lookup"><span data-stu-id="4972d-193">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="4972d-194">Bağımsız değişkenlerinin tümü DI tarafından yerine getirilme için tek bir Oluşturucu bulunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4972d-194">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="4972d-195">Varsayılan değerleri belirttiklerinde, DI tarafından kapsanmayan ek parametrelere izin verilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-195">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="4972d-196">Uygulanabilir Oluşturucu *ortak*olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4972d-196">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="4972d-197">Uygulanabilir bir Oluşturucu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4972d-197">One applicable constructor must exist.</span></span> <span data-ttu-id="4972d-198">Belirsizlik söz konusu olduğunda, bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4972d-198">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="4972d-199">Bir dı kapsamını yönetmek için yardımcı program temel bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="4972d-199">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="4972d-200">ASP.NET Core uygulamalarda, kapsamlı hizmetler genellikle geçerli isteğin kapsamlandırılır.</span><span class="sxs-lookup"><span data-stu-id="4972d-200">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="4972d-201">İstek tamamlandıktan sonra, tüm kapsamlı veya geçici hizmetler dı sistemi tarafından silinir.</span><span class="sxs-lookup"><span data-stu-id="4972d-201">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="4972d-202">BlazorSunucu uygulamalarında istek kapsamı, istemci bağlantısı süresince sürer. Bu, geçici ve kapsamlı hizmetlerin beklenenden çok daha uzun süreli olarak oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-202">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="4972d-203">BlazorWebassembly uygulamalarında, kapsamlı bir ömürle kaydedilen hizmetler tekton olarak değerlendirilir, bu nedenle tipik ASP.NET Core uygulamalarında kapsamlı hizmetlerden daha uzun bir süre yaşarlar.</span><span class="sxs-lookup"><span data-stu-id="4972d-203">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="4972d-204">Uygulamalarda bir hizmet ömrünü sınırlayan bir yaklaşım Blazor , <xref:Microsoft.AspNetCore.Components.OwningComponentBase> türü kullanır.</span><span class="sxs-lookup"><span data-stu-id="4972d-204">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="4972d-205"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>, öğesinden türetilmiş soyut bir türdür <xref:Microsoft.AspNetCore.Components.ComponentBase> ve bileşenin ömrüne karşılık gelen BIR dı kapsamı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4972d-205"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="4972d-206">Bu kapsamı kullanarak, dı hizmetlerini kapsamlı bir ömür ile kullanmak mümkündür ve bileşen olarak bu uygulamaları canlı hale gelir.</span><span class="sxs-lookup"><span data-stu-id="4972d-206">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="4972d-207">Bileşen yok edildiğinde, bileşenin kapsamlı hizmet sağlayıcısından gelen hizmetler de silinir.</span><span class="sxs-lookup"><span data-stu-id="4972d-207">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="4972d-208">Bu, şu hizmetler için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="4972d-208">This can be useful for services that:</span></span>

* <span data-ttu-id="4972d-209">Geçici ömür uygun olmadığından, bir bileşen içinde yeniden kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4972d-209">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="4972d-210">Tek yaşam süresi uygun olmadığından, bileşenler arasında paylaşılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="4972d-210">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="4972d-211">Türün iki sürümü <xref:Microsoft.AspNetCore.Components.OwningComponentBase> kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="4972d-211">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="4972d-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>, türünde <xref:Microsoft.AspNetCore.Components.ComponentBase> Protected özelliği olan bir abstract, atılabilir alt öğesidir <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="4972d-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="4972d-213">Bu sağlayıcı, bileşenin kullanım ömrü kapsamındaki Hizmetleri çözümlemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-213">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="4972d-214">Ya da özniteliği kullanılarak bileşene eklenen dı Hizmetleri, [`@inject`](xref:mvc/views/razor#inject) [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) bileşen kapsamında oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="4972d-214">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="4972d-215">Bileşenin kapsamını kullanmak için, hizmetler veya kullanılarak çözümlenmelidir <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="4972d-215">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="4972d-216">Sağlayıcı kullanılarak çözümlenen hizmetlerin, <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> aynı kapsamdaki bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="4972d-216">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="4972d-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>öğesinden türetilir <xref:Microsoft.AspNetCore.Components.OwningComponentBase> ve KAPSAMDAKI <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> dı sağlayıcısından bir örneğini döndüren bir özellik ekler `T` .</span><span class="sxs-lookup"><span data-stu-id="4972d-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="4972d-218">Bu tür, <xref:System.IServiceProvider> uygulamanın, bileşenin kapsamını kullanarak dı kapsayıcısından gerektirdiği bir birincil hizmet olduğunda bir örneği kullanmadan kapsamlı hizmetlere erişmenin kolay bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="4972d-218">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="4972d-219"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>Özelliği kullanılabilir, bu sayede uygulama, gerekirse diğer tür hizmetleri alabilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-219">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="4972d-220">Dı Entity Framework DbContext kullanımı</span><span class="sxs-lookup"><span data-stu-id="4972d-220">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="4972d-221">Web Apps 'in kaynağından alınacak bir ortak hizmet türü Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> nesneleridir.</span><span class="sxs-lookup"><span data-stu-id="4972d-221">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span> <span data-ttu-id="4972d-222">Kullanarak EF Services <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 'in kaydı, <xref:Microsoft.EntityFrameworkCore.DbContext> Varsayılan olarak kapsamlı bir hizmet olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="4972d-222">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span> <span data-ttu-id="4972d-223">Kapsamlı bir hizmet olarak kaydetme Blazor <xref:Microsoft.EntityFrameworkCore.DbContext> , örneklerin uygulama genelinde uzun süreli ve paylaşılan olmasına neden olduğu için uygulamalardaki sorunlara yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-223">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="4972d-224"><xref:Microsoft.EntityFrameworkCore.DbContext>iş parçacığı açısından güvenli değildir ve aynı anda kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="4972d-224"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="4972d-225">Uygulamaya bağlı olarak, <xref:Microsoft.AspNetCore.Components.OwningComponentBase> bir öğesinin kapsamını tek bir bileşen ile sınırlamak için kullanılması <xref:Microsoft.EntityFrameworkCore.DbContext> sorunu çözebilir *may* .</span><span class="sxs-lookup"><span data-stu-id="4972d-225">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="4972d-226">Bir bileşen <xref:Microsoft.EntityFrameworkCore.DbContext> paralel olarak kullanmıyorsa, bileşeni öğesinden kaynağından türeterek <xref:Microsoft.AspNetCore.Components.OwningComponentBase> ve <xref:Microsoft.EntityFrameworkCore.DbContext> kaynağından alma <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> yeterlidir çünkü şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="4972d-226">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="4972d-227">Ayrı bileşenler bir paylaşmaz <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="4972d-227">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
* <span data-ttu-id="4972d-228"><xref:Microsoft.EntityFrameworkCore.DbContext>Yalnızca bileşen bu bileşene bağlı olarak sürer.</span><span class="sxs-lookup"><span data-stu-id="4972d-228">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>

<span data-ttu-id="4972d-229">Tek bir bileşen eşzamanlı olarak kullanılabilir <xref:Microsoft.EntityFrameworkCore.DbContext> (örneğin, Kullanıcı her bir düğme seçtiğinde), kullanılması de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> eşzamanlı EF işlemlerinde sorunları önetmez.</span><span class="sxs-lookup"><span data-stu-id="4972d-229">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="4972d-230">Bu durumda, <xref:Microsoft.EntityFrameworkCore.DbContext> her MANTıKSAL EF işlemi için farklı bir kullanın.</span><span class="sxs-lookup"><span data-stu-id="4972d-230">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="4972d-231">Aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="4972d-231">Use either of the following approaches:</span></span>

* <span data-ttu-id="4972d-232"><xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> Bir bağımsız değişken olarak kullanarak doğrudan oluşturun, bu, dı 'den alınabilir ve iş parçacığı güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="4972d-232">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="4972d-233"><xref:Microsoft.EntityFrameworkCore.DbContext>Hizmeti kapsayıcısına geçici bir yaşam süresi ile kaydedin:</span><span class="sxs-lookup"><span data-stu-id="4972d-233">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="4972d-234">Bağlamı kaydederken kullanın <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="4972d-234">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="4972d-235"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>Genişletme yöntemi, türünde iki isteğe bağlı parametre alır <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> .</span><span class="sxs-lookup"><span data-stu-id="4972d-235">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span> <span data-ttu-id="4972d-236">Bu yaklaşımı kullanmak için yalnızca `contextLifetime` parametresinin olması gerekir <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="4972d-236">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="4972d-237">`optionsLifetime`varsayılan değerini tutabilir <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="4972d-237">`optionsLifetime` can keep its default value of <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="4972d-238">Geçici, <xref:Microsoft.EntityFrameworkCore.DbContext> [`@inject`](xref:mvc/views/razor#inject) paralel olarak bırden çok EF işlemi yürütemeyecek bileşenlere normal (kullanılarak) eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="4972d-238">The transient <xref:Microsoft.EntityFrameworkCore.DbContext> can be injected as normal (using [`@inject`](xref:mvc/views/razor#inject)) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="4972d-239">Aynı anda birden çok EF işlemi gerçekleştirebilecek olanlar, <xref:Microsoft.EntityFrameworkCore.DbContext> kullanarak her paralel işlem için ayrı nesneler isteyebilir <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .</span><span class="sxs-lookup"><span data-stu-id="4972d-239">Those that may perform multiple EF operations simultaneously can request separate <xref:Microsoft.EntityFrameworkCore.DbContext> objects for each parallel operation using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="4972d-240">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4972d-240">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="4972d-241">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="4972d-241">IDisposable guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
