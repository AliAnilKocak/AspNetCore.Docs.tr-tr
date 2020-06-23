---
title: ASP.NET Core Blazor şablonları
author: guardrex
description: ASP.NET Core Blazor uygulama şablonları ve proje yapısı hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: ce46d562285b95ff656ed43b3a63ca5e7315f4c8
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243219"
---
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="ae7bc-103">ASP.NET Core Blazor şablonları</span><span class="sxs-lookup"><span data-stu-id="ae7bc-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="ae7bc-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="ae7bc-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ae7bc-105">BlazorFramework, barındırma modellerinin her biri için uygulama geliştirmeye yönelik şablonlar sağlar Blazor :</span><span class="sxs-lookup"><span data-stu-id="ae7bc-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="ae7bc-106">WebAssembly ( `blazorwasm` )</span><span class="sxs-lookup"><span data-stu-id="ae7bc-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="ae7bc-107">Sunucu ( `blazorserver` )</span><span class="sxs-lookup"><span data-stu-id="ae7bc-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="ae7bc-108">Barındırma modelleri hakkında daha fazla bilgi için Blazor bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="ae7bc-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="ae7bc-109">Şablondan uygulama oluşturmaya yönelik adım adım yönergeler için Blazor , bkz <xref:blazor/get-started> ..</span><span class="sxs-lookup"><span data-stu-id="ae7bc-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="ae7bc-110">`--help`Seçenek CLI komutuna geçirerek şablon seçenekleri mevcuttur [`dotnet new`](/dotnet/core/tools/dotnet-new) :</span><span class="sxs-lookup"><span data-stu-id="ae7bc-110">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor<span data-ttu-id="ae7bc-111">proje yapısı</span><span class="sxs-lookup"><span data-stu-id="ae7bc-111"> project structure</span></span>

<span data-ttu-id="ae7bc-112">Aşağıdaki dosya ve klasörler Blazor bir şablondan oluşturulan bir uygulamayı yapar Blazor :</span><span class="sxs-lookup"><span data-stu-id="ae7bc-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="ae7bc-113">`Program.cs`: Uygulamanın şunları ayarlayan giriş noktası:</span><span class="sxs-lookup"><span data-stu-id="ae7bc-113">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="ae7bc-114">ASP.NET Core [ana bilgisayar](xref:fundamentals/host/generic-host) ( Blazor sunucu)</span><span class="sxs-lookup"><span data-stu-id="ae7bc-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="ae7bc-115">WebAssembly Host ( Blazor webassembly): Bu dosyadaki kod, Blazor webassembly şablonundan () oluşturulan uygulamalar için benzersizdir `blazorwasm` .</span><span class="sxs-lookup"><span data-stu-id="ae7bc-115">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="ae7bc-116">`App`Uygulamanın kök bileşeni olan bileşeni, `app` yönteminin DOM öğesi olarak belirtilir `Add` .</span><span class="sxs-lookup"><span data-stu-id="ae7bc-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="ae7bc-117">Hizmetler, `ConfigureServices` ana bilgisayar Oluşturucu 'daki yöntemiyle yapılandırılabilir (örneğin, `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).</span><span class="sxs-lookup"><span data-stu-id="ae7bc-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="ae7bc-118">Yapılandırma, ana bilgisayar Oluşturucu () yoluyla sağlanabilir `builder.Configuration` .</span><span class="sxs-lookup"><span data-stu-id="ae7bc-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="ae7bc-119">`Startup.cs`( Blazor Sunucu): uygulamanın başlangıç mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-119">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="ae7bc-120">`Startup`Sınıfı iki yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="ae7bc-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="ae7bc-121">`ConfigureServices`: Uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmetlerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-121">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="ae7bc-122">BlazorSunucu uygulamalarında, Hizmetleri çağırarak eklenir <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> ve `WeatherForecastService` örnek bileşen tarafından kullanılmak üzere hizmet kapsayıcısına eklenir `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="ae7bc-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="ae7bc-123">`Configure`: Uygulamanın istek işleme ardışık düzenini yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="ae7bc-123">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="ae7bc-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>, tarayıcıya gerçek zamanlı bağlantı için bir uç nokta ayarlamak üzere çağırılır.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="ae7bc-125">Bağlantı, [SignalR](xref:signalr/introduction) uygulamalarına gerçek zamanlı Web işlevselliği ekleme çerçevesi olan ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="ae7bc-126">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*), uygulamanın () kök sayfasını ayarlamak `Pages/_Host.cshtml` ve gezinmeyi etkinleştirmek için çağırılır.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-126">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="ae7bc-127">`wwwroot/index.html`( Blazor Webassembly): BIR HTML sayfası olarak uygulanan uygulamanın kök sayfası:</span><span class="sxs-lookup"><span data-stu-id="ae7bc-127">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="ae7bc-128">Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="ae7bc-129">Bu sayfa, kök bileşenin nerede `App` işleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="ae7bc-130">`App`Bileşen ( `App.razor` ), `app` içindeki metoduna DOM öğesi olarak belirtilir `AddComponent` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ae7bc-130">The `App` component (`App.razor`) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="ae7bc-131">`_framework/blazor.webassembly.js`JavaScript dosyası yüklenir ve şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="ae7bc-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="ae7bc-132">.NET çalışma zamanını, uygulamayı ve uygulamanın bağımlılıklarını indirir.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="ae7bc-133">Uygulamayı çalıştırmak için çalışma zamanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="ae7bc-134">`App.razor`: Bileşeni kullanarak istemci tarafı yönlendirmeyi ayarlayan uygulamanın kök bileşeni <xref:Microsoft.AspNetCore.Components.Routing.Router> .</span><span class="sxs-lookup"><span data-stu-id="ae7bc-134">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="ae7bc-135"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen tarayıcı gezintisini karşılar ve istenen adresle eşleşen sayfayı işler.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-135">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="ae7bc-136">`Pages`klasör: `.razor` Blazor Razor bir sunucu uygulamasının uygulamasını ve kök sayfasını oluşturan yönlendirilebilir bileşenleri/sayfaları () içerir Blazor .</span><span class="sxs-lookup"><span data-stu-id="ae7bc-136">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="ae7bc-137">Her sayfanın yolu, yönergesi kullanılarak belirtilir [`@page`](xref:mvc/views/razor#page) .</span><span class="sxs-lookup"><span data-stu-id="ae7bc-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="ae7bc-138">Şablon şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="ae7bc-138">The template includes the following:</span></span>
  * <span data-ttu-id="ae7bc-139">`_Host.cshtml`( Blazor Sunucu): uygulamanın kök sayfası olarak Razor uygulandı Sayfasında</span><span class="sxs-lookup"><span data-stu-id="ae7bc-139">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="ae7bc-140">Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="ae7bc-141">`_framework/blazor.server.js`Tarayıcı ve sunucu arasındaki gerçek zamanlı bağlantıyı ayarlayan JavaScript dosyası yüklenir SignalR .</span><span class="sxs-lookup"><span data-stu-id="ae7bc-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="ae7bc-142">Ana bilgisayar sayfası, kök bileşeni 'nin `App` ( `App.razor` ) nerede işleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-142">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="ae7bc-143">`Counter`( `Pages/Counter.razor` ): Sayaç sayfasını uygular.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-143">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="ae7bc-144">`Error`( `Error.razor` Blazor Yalnızca sunucu uygulaması): uygulamada işlenmeyen bir özel durum oluştuğunda işlenir.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-144">`Error` (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="ae7bc-145">`FetchData`( `Pages/FetchData.razor` ): Veri getirme sayfasını uygular.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-145">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="ae7bc-146">`Index`( `Pages/Index.razor` ): Giriş sayfasını uygular.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-146">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>

* <span data-ttu-id="ae7bc-147">`Shared`klasör: uygulama tarafından kullanılan diğer Kullanıcı Arabirimi bileşenlerini ( `.razor` ) içerir:</span><span class="sxs-lookup"><span data-stu-id="ae7bc-147">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="ae7bc-148">`MainLayout`( `MainLayout.razor` ): Uygulamanın Düzen bileşeni.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-148">`MainLayout` (`MainLayout.razor`): The app's layout component.</span></span>
  * <span data-ttu-id="ae7bc-149">`NavMenu`( `NavMenu.razor` ): Kenar çubuğu gezintisini uygular.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-149">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="ae7bc-150">[ `NavLink` ](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Diğer bileşenlere gezinti bağlantılarını işleyen bileşeni () içerir Razor .</span><span class="sxs-lookup"><span data-stu-id="ae7bc-150">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="ae7bc-151">Bileşeni, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="ae7bc-152">`_Imports.razor`: Razor `.razor` [`@using`](xref:mvc/views/razor#using) Ad alanları için yönergeler gibi uygulamanın bileşenlerine () dahil etmek için ortak yönergeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-152">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="ae7bc-153">`Data`klasör ( Blazor sunucu): `WeatherForecast` `WeatherForecastService` uygulamanın bileşenine örnek Hava durumu verileri sağlayan sınıfını ve uygulamasını içerir `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="ae7bc-153">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="ae7bc-154">`wwwroot`: Uygulamanın ortak statik varlıklarını içeren uygulamanın [Web kök](xref:fundamentals/index#web-root) klasörü.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-154">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="ae7bc-155">`appsettings.json`( Blazor Sunucu): uygulamanın yapılandırma ayarları.</span><span class="sxs-lookup"><span data-stu-id="ae7bc-155">`appsettings.json` (Blazor Server): Configuration settings for the app.</span></span>
