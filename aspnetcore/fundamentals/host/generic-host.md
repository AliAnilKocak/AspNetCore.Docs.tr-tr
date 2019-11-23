---
title: .NET genel ana bilgisayar
author: rick-anderson
description: Uygulama başlatma ve ömür yönetiminden sorumlu .NET Core genel ana bilgisayarı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: f14917ad924e2c762a14c2cb5f51391d4be06e7b
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378748"
---
# <a name="net-generic-host"></a><span data-ttu-id="6b5ec-103">.NET genel ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="6b5ec-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6b5ec-104">Bu makalede .NET Core genel ana bilgisayarı (<xref:Microsoft.Extensions.Hosting.HostBuilder>) tanıtılmakta ve nasıl kullanılacağına ilişkin yönergeler sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="6b5ec-105">Ana bilgisayar nedir?</span><span class="sxs-lookup"><span data-stu-id="6b5ec-105">What's a host?</span></span>

<span data-ttu-id="6b5ec-106">*Ana bilgisayar* , bir uygulamanın kaynaklarını kapsülleyen bir nesnedir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="6b5ec-107">Bağımlılık ekleme (dı)</span><span class="sxs-lookup"><span data-stu-id="6b5ec-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="6b5ec-108">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="6b5ec-108">Logging</span></span>
* <span data-ttu-id="6b5ec-109">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6b5ec-109">Configuration</span></span>
* <span data-ttu-id="6b5ec-110">`IHostedService` uygulamalar</span><span class="sxs-lookup"><span data-stu-id="6b5ec-110">`IHostedService` implementations</span></span>

<span data-ttu-id="6b5ec-111">Bir konak başlatıldığında, DI kapsayıcısında bulduğu <xref:Microsoft.Extensions.Hosting.IHostedService> her bir uygulamada `IHostedService.StartAsync` çağırır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="6b5ec-112">Bir Web uygulamasında, `IHostedService` uygulamalarından biri, [http sunucu uygulaması](xref:fundamentals/index#servers)Başlatan bir Web hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="6b5ec-113">Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="6b5ec-114">3,0 ' den önceki ASP.NET Core sürümlerinde, [Web ana BILGISAYARı](xref:fundamentals/host/web-host) http iş yükleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="6b5ec-115">Web ana bilgisayarı artık Web uygulamaları için önerilmez ve yalnızca geriye dönük uyumluluk için kullanılabilir durumda kalır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="6b5ec-116">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="6b5ec-116">Set up a host</span></span>

<span data-ttu-id="6b5ec-117">Konak genellikle `Program` sınıfındaki kodla yapılandırılır, oluşturulur ve çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="6b5ec-118">`Main` Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-118">The `Main` method:</span></span>

* <span data-ttu-id="6b5ec-119">Bir Oluşturucu nesnesi oluşturmak ve yapılandırmak için bir `CreateHostBuilder` yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="6b5ec-120">Oluşturucu nesnesinde `Build` ve `Run` yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="6b5ec-121">İşte, tek bir `IHostedService` uygulama olarak dı kapsayıcısına eklenen HTTP olmayan bir iş yükü için *program.cs* kodu.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="6b5ec-122">Bir HTTP iş yükü için `Main` yöntemi aynıdır ancak `CreateHostBuilder` `ConfigureWebHostDefaults`çağırır:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="6b5ec-123">Uygulama Entity Framework Core kullanıyorsa `CreateHostBuilder` yönteminin adını veya imzasını değiştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="6b5ec-124">[Entity Framework Core araçları](/ef/core/miscellaneous/cli/) , uygulamayı çalıştırmadan Konağı yapılandıran bir `CreateHostBuilder` yöntemi bulmayı bekler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="6b5ec-125">Daha fazla bilgi için bkz. [Tasarım zamanı DbContext oluşturma](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="6b5ec-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="6b5ec-126">Varsayılan Oluşturucu ayarları</span><span class="sxs-lookup"><span data-stu-id="6b5ec-126">Default builder settings</span></span>

<span data-ttu-id="6b5ec-127"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="6b5ec-128">[İçerik kökünü](xref:fundamentals/index#content-root) <xref:System.IO.Directory.GetCurrentDirectory*>tarafından döndürülen yola ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="6b5ec-129">Ana bilgisayar yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="6b5ec-130">"DOTNET_" önekli ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-130">Environment variables prefixed with "DOTNET_".</span></span>
  * <span data-ttu-id="6b5ec-131">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-131">Command-line arguments.</span></span>
* <span data-ttu-id="6b5ec-132">Uygulama yapılandırmasını şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="6b5ec-133">*appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="6b5ec-134">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="6b5ec-135">Uygulama `Development` ortamda çalıştığında [gizli dizi Yöneticisi](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="6b5ec-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="6b5ec-136">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-136">Environment variables.</span></span>
  * <span data-ttu-id="6b5ec-137">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-137">Command-line arguments.</span></span>
* <span data-ttu-id="6b5ec-138">Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="6b5ec-139">Konsol</span><span class="sxs-lookup"><span data-stu-id="6b5ec-139">Console</span></span>
  * <span data-ttu-id="6b5ec-140">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="6b5ec-140">Debug</span></span>
  * <span data-ttu-id="6b5ec-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="6b5ec-141">EventSource</span></span>
  * <span data-ttu-id="6b5ec-142">Olay günlüğü (yalnızca Windows üzerinde çalışırken)</span><span class="sxs-lookup"><span data-stu-id="6b5ec-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="6b5ec-143">Ortam geliştirme sırasında [kapsam doğrulaması](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulaması](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="6b5ec-144">`ConfigureWebHostDefaults` Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="6b5ec-145">"ASPNETCORE_" önekli ortam değişkenlerinden ana bilgisayar yapılandırmasını yükler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-145">Loads host configuration from environment variables prefixed with "ASPNETCORE_".</span></span>
* <span data-ttu-id="6b5ec-146">[Kestrel](xref:fundamentals/servers/kestrel) sunucusunu Web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="6b5ec-147">Kestrel sunucusunun varsayılan seçenekleri için bkz. <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="6b5ec-148">[Ana bilgisayar filtreleme ara yazılımı](xref:fundamentals/servers/kestrel#host-filtering)ekler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="6b5ec-149">ASPNETCORE_FORWARDEDHEADERS_ENABLED = true ise [Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if ASPNETCORE_FORWARDEDHEADERS_ENABLED=true.</span></span>
* <span data-ttu-id="6b5ec-150">IIS tümleştirmesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-150">Enables IIS integration.</span></span> <span data-ttu-id="6b5ec-151">IIS varsayılan seçenekleri için bkz. <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="6b5ec-152">Bu makalenin ilerleyen kısımlarında [Web Apps bölümlerine yönelik](#settings-for-web-apps) [tüm uygulama türleri](#settings-for-all-app-types) ve ayarlarının ayarları, varsayılan Oluşturucu ayarlarının nasıl geçersiz kılınacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="6b5ec-153">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="6b5ec-153">Framework-provided services</span></span>

<span data-ttu-id="6b5ec-154">Kayıtlı hizmetler otomatik olarak şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-154">Services that are registered automatically include the following:</span></span>

* [<span data-ttu-id="6b5ec-155">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="6b5ec-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="6b5ec-156">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="6b5ec-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="6b5ec-157">Ihostenvironment/ıwebhostenvironment</span><span class="sxs-lookup"><span data-stu-id="6b5ec-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="6b5ec-158">Framework tarafından sunulan hizmetler hakkında daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="6b5ec-159">Ihostapplicationlifetime</span><span class="sxs-lookup"><span data-stu-id="6b5ec-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="6b5ec-160">Başlatma sonrası ve düzgün kapanma görevlerini işlemek için <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (eski adıyla `IApplicationLifetime`) hizmeti herhangi bir sınıfa ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="6b5ec-161">Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="6b5ec-162">Arabirim Ayrıca bir `StopApplication` yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="6b5ec-163">Aşağıdaki örnek, `IHostApplicationLifetime` olaylarını kaydeden bir `IHostedService` uygulamasıdır:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="6b5ec-164">Ihostlifetime</span><span class="sxs-lookup"><span data-stu-id="6b5ec-164">IHostLifetime</span></span>

<span data-ttu-id="6b5ec-165"><xref:Microsoft.Extensions.Hosting.IHostLifetime> uygulama, ana bilgisayar başladığında ve durdurulduğunda kontrol eder.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="6b5ec-166">Kaydedilen son uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-166">The last implementation registered is used.</span></span>

<span data-ttu-id="6b5ec-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` varsayılan `IHostLifetime` uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="6b5ec-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="6b5ec-169">CTRL + C/SIGINT veya SIGDÖNEM için dinler ve <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>, başlatma işlemini başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-169">listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="6b5ec-170">[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="6b5ec-171">Ihostenvironment</span><span class="sxs-lookup"><span data-stu-id="6b5ec-171">IHostEnvironment</span></span>

<span data-ttu-id="6b5ec-172"><xref:Microsoft.Extensions.Hosting.IHostEnvironment> hizmetini bir sınıfa ekleyin ve aşağıdakiler hakkında bilgi alın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following:</span></span>

* [<span data-ttu-id="6b5ec-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="6b5ec-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="6b5ec-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="6b5ec-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="6b5ec-175">Contentrootyolu</span><span class="sxs-lookup"><span data-stu-id="6b5ec-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="6b5ec-176">Web uygulamaları, `IHostEnvironment` devralan ve ekleyen `IWebHostEnvironment` arabirimini uygular:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds:</span></span>

* [<span data-ttu-id="6b5ec-177">WebRootPath</span><span class="sxs-lookup"><span data-stu-id="6b5ec-177">WebRootPath</span></span>](#webroot)

## <a name="host-configuration"></a><span data-ttu-id="6b5ec-178">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="6b5ec-178">Host configuration</span></span>

<span data-ttu-id="6b5ec-179">Konak yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostEnvironment> uygulamasının özellikleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="6b5ec-180">Konak yapılandırması, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>içinde [Hostbuildercontext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) içinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="6b5ec-181">`ConfigureAppConfiguration`sonra, `HostBuilderContext.Configuration` uygulama yapılandırması ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="6b5ec-182">Konak yapılandırması eklemek için `IHostBuilder`<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> çağırın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="6b5ec-183">`ConfigureHostConfiguration`, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="6b5ec-184">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="6b5ec-185">Ön ek `DOTNET_` ve komut satırı bağımsız değişkenlerine sahip ortam değişkeni sağlayıcısı, CreateDefaultBuilder tarafından eklenir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-185">The environment variable provider with prefix `DOTNET_` and command line args are included by CreateDefaultBuilder.</span></span> <span data-ttu-id="6b5ec-186">Web Apps için `ASPNETCORE_` ön ekine sahip ortam değişkeni sağlayıcısı eklenir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="6b5ec-187">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="6b5ec-188">Örneğin, `ASPNETCORE_ENVIRONMENT` için ortam değişkeni değeri `environment` anahtar için ana bilgisayar yapılandırma değeri haline gelir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="6b5ec-189">Aşağıdaki örnek ana bilgisayar yapılandırması oluşturur:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="6b5ec-190">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="6b5ec-190">App configuration</span></span>

<span data-ttu-id="6b5ec-191">Uygulama yapılandırması, `IHostBuilder`<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> çağırarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="6b5ec-192">`ConfigureAppConfiguration`, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="6b5ec-193">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="6b5ec-194">`ConfigureAppConfiguration` tarafından oluşturulan yapılandırma, sonraki işlemler ve DI hizmeti olarak, [Hostbuildercontext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) konumunda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="6b5ec-195">Konak yapılandırması, uygulama yapılandırmasına de eklenir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="6b5ec-196">Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index#configureappconfiguration)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="6b5ec-197">Tüm uygulama türleri için ayarlar</span><span class="sxs-lookup"><span data-stu-id="6b5ec-197">Settings for all app types</span></span>

<span data-ttu-id="6b5ec-198">Bu bölüm, hem HTTP hem de HTTP olmayan iş yükleri için uygulanan konak ayarlarını listeler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="6b5ec-199">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya `ASPNETCORE_` öneki olabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="6b5ec-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="6b5ec-200">ApplicationName</span></span>

<span data-ttu-id="6b5ec-201">[Ihostenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="6b5ec-202">**Anahtar**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="6b5ec-202">**Key**: applicationName</span></span>  
<span data-ttu-id="6b5ec-203">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="6b5ec-203">**Type**: *string*</span></span>  
<span data-ttu-id="6b5ec-204">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>
<span data-ttu-id="6b5ec-205">**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="6b5ec-206">Bu değeri ayarlamak için ortam değişkenini kullanın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-206">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="6b5ec-207">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="6b5ec-207">ContentRootPath</span></span>

<span data-ttu-id="6b5ec-208">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="6b5ec-209">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="6b5ec-210">**Anahtar**: contentroot</span><span class="sxs-lookup"><span data-stu-id="6b5ec-210">**Key**: contentRoot</span></span>  
<span data-ttu-id="6b5ec-211">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="6b5ec-211">**Type**: *string*</span></span>  
<span data-ttu-id="6b5ec-212">**Varsayılan**: uygulama derlemesinin bulunduğu klasör.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="6b5ec-213">**Ortam değişkeni**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="6b5ec-214">Bu değeri ayarlamak için, ortam değişkenini kullanın veya `IHostBuilder``UseContentRoot` çağırın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="6b5ec-215">Daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-215">For more information, see:</span></span>

* [<span data-ttu-id="6b5ec-216">Temel bilgiler: Içerik kökü</span><span class="sxs-lookup"><span data-stu-id="6b5ec-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="6b5ec-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="6b5ec-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="6b5ec-218">environmentName</span><span class="sxs-lookup"><span data-stu-id="6b5ec-218">EnvironmentName</span></span>

<span data-ttu-id="6b5ec-219">[Ihostenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="6b5ec-220">Çerçeve tanımlı değerler `Development`, `Staging`ve `Production`içerir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="6b5ec-221">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-221">Values aren't case sensitive.</span></span>

<span data-ttu-id="6b5ec-222">**Anahtar**: ortam</span><span class="sxs-lookup"><span data-stu-id="6b5ec-222">**Key**: environment</span></span>  
<span data-ttu-id="6b5ec-223">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="6b5ec-223">**Type**: *string*</span></span>  
<span data-ttu-id="6b5ec-224">**Varsayılan**: üretim</span><span class="sxs-lookup"><span data-stu-id="6b5ec-224">**Default**: Production</span></span>  
<span data-ttu-id="6b5ec-225">**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="6b5ec-226">Bu değeri ayarlamak için, ortam değişkenini kullanın veya `IHostBuilder``UseEnvironment` çağırın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="6b5ec-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="6b5ec-227">ShutdownTimeout</span></span>

<span data-ttu-id="6b5ec-228">[Hostoptions. shutdowntimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>için zaman aşımını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="6b5ec-229">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-229">The default value is five seconds.</span></span>  <span data-ttu-id="6b5ec-230">Zaman aşımı süresi boyunca ana bilgisayar:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="6b5ec-231">[Ihostapplicationlifetime. Applicationdurduruluyor](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping)tetikler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="6b5ec-232">Üzerinde durmayacak hizmetler için barındırılan Hizmetleri durdurma ve hataları günlüğe kaydetme girişimleri.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="6b5ec-233">Tüm barındırılan hizmetler durmadan önce zaman aşımı süresi dolarsa, uygulama kapandığında kalan etkin hizmetler durdurulur.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="6b5ec-234">Hizmetler, işlemeyi tamamlamadıklarında bile durur.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="6b5ec-235">Hizmetlerin durdurulması için ek süre gerekiyorsa, zaman aşımını artırın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="6b5ec-236">**Anahtar**: shutdowntimeoutseconds</span><span class="sxs-lookup"><span data-stu-id="6b5ec-236">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="6b5ec-237">**Tür**: *int*</span><span class="sxs-lookup"><span data-stu-id="6b5ec-237">**Type**: *int*</span></span>  
<span data-ttu-id="6b5ec-238">**Varsayılan**: 5 saniye **ortam değişkeni**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-238">**Default**: 5 seconds **Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="6b5ec-239">Bu değeri ayarlamak için, ortam değişkenini kullanın veya `HostOptions`yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="6b5ec-240">Aşağıdaki örnek, zaman aşımını 20 saniye olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="6b5ec-241">Web Apps ayarları</span><span class="sxs-lookup"><span data-stu-id="6b5ec-241">Settings for web apps</span></span>

<span data-ttu-id="6b5ec-242">Bazı konak ayarları yalnızca HTTP iş yükleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-242">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="6b5ec-243">Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya `ASPNETCORE_` öneki olabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-243">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="6b5ec-244">`IWebHostBuilder` genişletme yöntemleri bu ayarlar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-244">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="6b5ec-245">Uzantı yöntemlerinin nasıl çağrılacağını gösteren kod örnekleri, aşağıdaki örnekte olduğu gibi `webBuilder` bir `IWebHostBuilder`örneği olduğunu varsayar:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-245">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="6b5ec-246">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="6b5ec-246">CaptureStartupErrors</span></span>

<span data-ttu-id="6b5ec-247">`false`, başlangıç sırasında hata durumunda çıkış sırasında hatalar oluştu.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-247">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="6b5ec-248">`true`, ana bilgisayar başlangıç sırasında özel durumları yakalar ve sunucuyu başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-248">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="6b5ec-249">**Anahtar**: capturestartuperrors</span><span class="sxs-lookup"><span data-stu-id="6b5ec-249">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="6b5ec-250">**Tür**: *bool* (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="6b5ec-250">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="6b5ec-251">**Varsayılan**: uygulama IIS arkasındaki Kestrel ile çalıştırılmadığı müddetçe `false` varsayılan olarak `true`.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-251">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="6b5ec-252">**Ortam değişkeni**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-252">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="6b5ec-253">Bu değeri ayarlamak için yapılandırma veya çağrı `CaptureStartupErrors`kullanın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-253">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="6b5ec-254">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="6b5ec-254">DetailedErrors</span></span>

<span data-ttu-id="6b5ec-255">Etkinleştirildiğinde veya ortam `Development`olduğunda, uygulama ayrıntılı hataları yakalar.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-255">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="6b5ec-256">**Anahtar**: detailederrors</span><span class="sxs-lookup"><span data-stu-id="6b5ec-256">**Key**: detailedErrors</span></span>  
<span data-ttu-id="6b5ec-257">**Tür**: *bool* (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="6b5ec-257">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="6b5ec-258">**Varsayılan**: false</span><span class="sxs-lookup"><span data-stu-id="6b5ec-258">**Default**: false</span></span>  
<span data-ttu-id="6b5ec-259">**Ortam değişkeni**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-259">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="6b5ec-260">Bu değeri ayarlamak için yapılandırma veya çağrı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-260">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="6b5ec-261">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="6b5ec-261">HostingStartupAssemblies</span></span>

<span data-ttu-id="6b5ec-262">Başlangıçta yüklenecek başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-262">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="6b5ec-263">Yapılandırma değeri boş bir dize olarak varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-263">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="6b5ec-264">Barındırma başlangıç derlemeleri sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-264">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="6b5ec-265">**Anahtar**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="6b5ec-265">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="6b5ec-266">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="6b5ec-266">**Type**: *string*</span></span>  
<span data-ttu-id="6b5ec-267">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="6b5ec-267">**Default**: Empty string</span></span>  
<span data-ttu-id="6b5ec-268">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-268">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="6b5ec-269">Bu değeri ayarlamak için yapılandırma veya çağrı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-269">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="6b5ec-270">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="6b5ec-270">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="6b5ec-271">Başlangıçta dışlamak üzere başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-271">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="6b5ec-272">**Anahtar**: hostingstartupexcludeassemblies</span><span class="sxs-lookup"><span data-stu-id="6b5ec-272">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="6b5ec-273">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="6b5ec-273">**Type**: *string*</span></span>  
<span data-ttu-id="6b5ec-274">**Varsayılan**: boş dize</span><span class="sxs-lookup"><span data-stu-id="6b5ec-274">**Default**: Empty string</span></span>  
<span data-ttu-id="6b5ec-275">**Ortam değişkeni**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-275">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="6b5ec-276">Bu değeri ayarlamak için yapılandırma veya çağrı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-276">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="6b5ec-277">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="6b5ec-277">HTTPS_Port</span></span>

<span data-ttu-id="6b5ec-278">HTTPS yeniden yönlendirme bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-278">The HTTPS redirect port.</span></span> <span data-ttu-id="6b5ec-279">[Https zorlama](xref:security/enforcing-ssl)bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-279">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="6b5ec-280">**Anahtar**: https_port</span><span class="sxs-lookup"><span data-stu-id="6b5ec-280">**Key**: https_port</span></span>  
<span data-ttu-id="6b5ec-281">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="6b5ec-281">**Type**: *string*</span></span>  
<span data-ttu-id="6b5ec-282">**Varsayılan**: varsayılan değer ayarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-282">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="6b5ec-283">**Ortam değişkeni**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-283">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="6b5ec-284">Bu değeri ayarlamak için yapılandırma veya çağrı `UseSetting`kullanın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-284">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="6b5ec-285">Tercih Hostingurl 'Leri</span><span class="sxs-lookup"><span data-stu-id="6b5ec-285">PreferHostingUrls</span></span>

<span data-ttu-id="6b5ec-286">Konağın `IServer` uygulamayla yapılandırılanlar yerine `IWebHostBuilder` ile yapılandırılan URL 'lerde dinleme yapıp kullanmayacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-286">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="6b5ec-287">**Anahtar**: preferhostingurl 'leri</span><span class="sxs-lookup"><span data-stu-id="6b5ec-287">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="6b5ec-288">**Tür**: *bool* (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="6b5ec-288">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="6b5ec-289">**Varsayılan**: true</span><span class="sxs-lookup"><span data-stu-id="6b5ec-289">**Default**: true</span></span>  
<span data-ttu-id="6b5ec-290">**Ortam değişkeni**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-290">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="6b5ec-291">Bu değeri ayarlamak için, ortam değişkenini kullanın veya `PreferHostingUrls`çağırın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-291">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="6b5ec-292">Koruyucu Thostınstartup</span><span class="sxs-lookup"><span data-stu-id="6b5ec-292">PreventHostingStartup</span></span>

<span data-ttu-id="6b5ec-293">Uygulamanın derlemesi tarafından yapılandırılan başlatma derlemelerinin barındırılması dahil olmak üzere, barındırma başlangıç derlemelerinin otomatik yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-293">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="6b5ec-294">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-294">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="6b5ec-295">**Anahtar**: koruyucu thostingstartup</span><span class="sxs-lookup"><span data-stu-id="6b5ec-295">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="6b5ec-296">**Tür**: *bool* (`true` veya `1`)</span><span class="sxs-lookup"><span data-stu-id="6b5ec-296">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="6b5ec-297">**Varsayılan**: false</span><span class="sxs-lookup"><span data-stu-id="6b5ec-297">**Default**: false</span></span>  
<span data-ttu-id="6b5ec-298">**Ortam değişkeni**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-298">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="6b5ec-299">Bu değeri ayarlamak için, ortam değişkenini kullanın veya `UseSetting` çağırın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-299">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="6b5ec-300">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="6b5ec-300">StartupAssembly</span></span>

<span data-ttu-id="6b5ec-301">`Startup` sınıfını aramak için bütünleştirilmiş kod.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-301">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="6b5ec-302">**Anahtar**: startupassembly</span><span class="sxs-lookup"><span data-stu-id="6b5ec-302">**Key**: startupAssembly</span></span>  
<span data-ttu-id="6b5ec-303">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="6b5ec-303">**Type**: *string*</span></span>  
<span data-ttu-id="6b5ec-304">**Varsayılan**: uygulamanın derlemesi</span><span class="sxs-lookup"><span data-stu-id="6b5ec-304">**Default**: The app's assembly</span></span>  
<span data-ttu-id="6b5ec-305">**Ortam değişkeni**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-305">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="6b5ec-306">Bu değeri ayarlamak için, ortam değişkenini kullanın veya `UseStartup`çağırın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-306">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="6b5ec-307">`UseStartup`, bir derleme adı (`string`) veya bir tür (`TStartup`) alabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-307">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="6b5ec-308">Birden çok `UseStartup` yöntemi çağrılırsa, son bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-308">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="6b5ec-309">URL'ler</span><span class="sxs-lookup"><span data-stu-id="6b5ec-309">URLs</span></span>

<span data-ttu-id="6b5ec-310">Sunucu istekleri için dinlemesi gereken bağlantı noktaları ve protokollerle, noktalı virgülle ayrılmış IP adresleri listesi veya ana bilgisayar adresleri.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-310">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="6b5ec-311">Örneğin: `http://localhost:123`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-311">For example, `http://localhost:123`.</span></span> <span data-ttu-id="6b5ec-312">Sunucunun belirtilen bağlantı noktasını ve Protokolü (örneğin, `http://*:5000`) kullanarak herhangi bir IP adresi veya ana bilgisayar için istekleri dinlemesi gerektiğini belirtmek için "\*" kullanın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-312">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="6b5ec-313">Protokol (`http://` veya `https://`) her URL 'ye dahil olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-313">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="6b5ec-314">Desteklenen biçimler sunucular arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-314">Supported formats vary among servers.</span></span>

<span data-ttu-id="6b5ec-315">**Anahtar**: URL 'ler</span><span class="sxs-lookup"><span data-stu-id="6b5ec-315">**Key**: urls</span></span>  
<span data-ttu-id="6b5ec-316">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="6b5ec-316">**Type**: *string*</span></span>  
<span data-ttu-id="6b5ec-317">**Varsayılan**: `http://localhost:5000` ve `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-317">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="6b5ec-318">**Ortam değişkeni**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-318">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="6b5ec-319">Bu değeri ayarlamak için, ortam değişkenini kullanın veya `UseUrls`çağırın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-319">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="6b5ec-320">Kestrel kendi uç nokta yapılandırması API 'sine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-320">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="6b5ec-321">Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-321">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="6b5ec-322">WebRoot</span><span class="sxs-lookup"><span data-stu-id="6b5ec-322">WebRoot</span></span>

<span data-ttu-id="6b5ec-323">Uygulamanın statik varlıklarının göreli yolu.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-323">The relative path to the app's static assets.</span></span>

<span data-ttu-id="6b5ec-324">**Anahtar**: Webroot</span><span class="sxs-lookup"><span data-stu-id="6b5ec-324">**Key**: webroot</span></span>  
<span data-ttu-id="6b5ec-325">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="6b5ec-325">**Type**: *string*</span></span>  
<span data-ttu-id="6b5ec-326">**Varsayılan**: varsayılan `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-326">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="6b5ec-327">*{Content root}/Wwwroot* yolu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-327">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="6b5ec-328">Yol yoksa, Hayır-op dosya sağlayıcısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-328">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="6b5ec-329">**Ortam değişkeni**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-329">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="6b5ec-330">Bu değeri ayarlamak için, ortam değişkenini kullanın veya `UseWebRoot`çağırın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-330">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="6b5ec-331">Daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-331">For more information, see:</span></span>

* [<span data-ttu-id="6b5ec-332">Temel bilgiler: Web kökü</span><span class="sxs-lookup"><span data-stu-id="6b5ec-332">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="6b5ec-333">Contentrootyolu</span><span class="sxs-lookup"><span data-stu-id="6b5ec-333">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="6b5ec-334">Konak ömrünü yönetme</span><span class="sxs-lookup"><span data-stu-id="6b5ec-334">Manage the host lifetime</span></span>

<span data-ttu-id="6b5ec-335">Uygulamayı başlatmak ve durdurmak için oluşturulan <xref:Microsoft.Extensions.Hosting.IHost> uygulamasındaki Yöntemleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-335">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="6b5ec-336">Bu yöntemler, hizmet kapsayıcısında kayıtlı olan tüm <xref:Microsoft.Extensions.Hosting.IHostedService> uygulamalarını etkiler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-336">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="6b5ec-337">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="6b5ec-337">Run</span></span>

<span data-ttu-id="6b5ec-338"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uygulamayı çalıştırır ve konak kapanana kadar çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-338"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="6b5ec-339">RunAsync</span><span class="sxs-lookup"><span data-stu-id="6b5ec-339">RunAsync</span></span>

<span data-ttu-id="6b5ec-340"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uygulamayı çalıştırır ve iptal belirteci veya kapanışı tetiklendiğinde tamamlayan bir <xref:System.Threading.Tasks.Task> döndürür.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-340"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="6b5ec-341">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="6b5ec-341">RunConsoleAsync</span></span>

<span data-ttu-id="6b5ec-342"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>, konsol desteği sağlar, Konağı oluşturur ve başlatır ve CTRL + C/SIGINT ya da SIGDÖNEM 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-342"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="6b5ec-343">Başlangıç</span><span class="sxs-lookup"><span data-stu-id="6b5ec-343">Start</span></span>

<span data-ttu-id="6b5ec-344"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-344"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="6b5ec-345">StartAsync</span><span class="sxs-lookup"><span data-stu-id="6b5ec-345">StartAsync</span></span>

<span data-ttu-id="6b5ec-346"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, Konağı başlatır ve iptal belirteci veya kapanışı tetiklendiğinde tamamlanmış bir <xref:System.Threading.Tasks.Task> döndürür.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-346"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="6b5ec-347"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>, `StartAsync`başlangıcında çağrılır ve bu, devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-347"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="6b5ec-348">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-348">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="6b5ec-349">StopAsync</span><span class="sxs-lookup"><span data-stu-id="6b5ec-349">StopAsync</span></span>

<span data-ttu-id="6b5ec-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>, belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="6b5ec-351">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="6b5ec-351">WaitForShutdown</span></span>

<span data-ttu-id="6b5ec-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, CTRL + C/SIGINT veya SIGTERM gibi ıhostlifetime tarafından kapanmadan, çağıran iş parçacığını engeller.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via Ctrl+C/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="6b5ec-353">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="6b5ec-353">WaitForShutdownAsync</span></span>

<span data-ttu-id="6b5ec-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>, verilen belirteç aracılığıyla kapalı tetiklendiğinde ve <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>çağıran bir <xref:System.Threading.Tasks.Task> döndürür.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="6b5ec-355">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="6b5ec-355">External control</span></span>

<span data-ttu-id="6b5ec-356">Ana bilgisayar ömrünün doğrudan denetimi dışarıdan çağrılabilen yöntemler kullanılarak sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-356">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6b5ec-357">ASP.NET Core uygulamalar bir konağı yapılandırıp başlatır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-357">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="6b5ec-358">Uygulama başlatma ve ömür yönetimi için konak sorumludur.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-358">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="6b5ec-359">Bu makalede, HTTP isteklerini işlemeyin uygulamalar için kullanılan ASP.NET Core genel ana bilgisayar (<xref:Microsoft.Extensions.Hosting.HostBuilder>) ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-359">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="6b5ec-360">Genel konağın amacı, daha geniş bir konak senaryolarını etkinleştirmek üzere Web ana bilgisayar API 'sinden HTTP işlem hattını ayırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-360">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="6b5ec-361">Yapılandırma, bağımlılık ekleme (dı) ve günlüğe kaydetme gibi çapraz kesme özelliğinden genel ana bilgisayar avantajı temel alınarak mesajlaşma, arka plan görevleri ve diğer HTTP olmayan iş yükleri.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-361">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="6b5ec-362">Genel ana bilgisayar ASP.NET Core 2,1 ' de yenidir ve Web barındırma senaryolarında uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-362">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="6b5ec-363">Web barındırma senaryolarında [Web konağını](xref:fundamentals/host/web-host)kullanın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-363">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="6b5ec-364">Genel ana bilgisayar gelecek bir sürümdeki Web konağını değiştirecek ve hem HTTP hem de HTTP olmayan senaryolarda birincil ana bilgisayar API 'SI olarak görev yapacak.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-364">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="6b5ec-365">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6b5ec-365">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6b5ec-366">Örnek uygulamayı [Visual Studio Code](https://code.visualstudio.com/)' de çalıştırırken, *dış veya tümleşik bir Terminal*kullanın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-366">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="6b5ec-367">Örneği bir `internalConsole`çalıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-367">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="6b5ec-368">Konsolu Visual Studio Code ayarlamak için:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-368">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="6b5ec-369">*. Vscode/Launch. JSON* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-369">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="6b5ec-370">**.NET Core başlatma (konsol)** yapılandırmasında **konsol** girişini bulun.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-370">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="6b5ec-371">Değeri `externalTerminal` ya da `integratedTerminal`olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-371">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="6b5ec-372">Giriş</span><span class="sxs-lookup"><span data-stu-id="6b5ec-372">Introduction</span></span>

<span data-ttu-id="6b5ec-373">Genel ana bilgisayar kitaplığı <xref:Microsoft.Extensions.Hosting> ad alanında kullanılabilir ve [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-373">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="6b5ec-374">`Microsoft.Extensions.Hosting` paketi [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e dahildir (ASP.NET Core 2,1 veya üzeri).</span><span class="sxs-lookup"><span data-stu-id="6b5ec-374">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="6b5ec-375"><xref:Microsoft.Extensions.Hosting.IHostedService>, kod yürütmeye yönelik giriş noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-375"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="6b5ec-376">Her bir <xref:Microsoft.Extensions.Hosting.IHostedService> uygulama, [ConfigureServices 'de hizmet kaydı](#configureservices)sırasında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-376">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="6b5ec-377"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>, konak başlatıldığında her <xref:Microsoft.Extensions.Hosting.IHostedService> çağrılır ve konak düzgün şekilde kapandığında <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> ters kayıt sırasında çağrılır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-377"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="6b5ec-378">Konak ayarlama</span><span class="sxs-lookup"><span data-stu-id="6b5ec-378">Set up a host</span></span>

<span data-ttu-id="6b5ec-379"><xref:Microsoft.Extensions.Hosting.IHostBuilder>, kitaplıkların ve uygulamaların Konağı başlatmak, derlemek ve çalıştırmak için kullandığı ana bileşendir:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-379"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="6b5ec-380">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="6b5ec-380">Options</span></span>

<span data-ttu-id="6b5ec-381"><xref:Microsoft.Extensions.Hosting.HostOptions> <xref:Microsoft.Extensions.Hosting.IHost>seçeneklerini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-381"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="6b5ec-382">Kapatılma zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="6b5ec-382">Shutdown timeout</span></span>

<span data-ttu-id="6b5ec-383"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>, <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman aşımını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-383"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="6b5ec-384">Varsayılan değer beş saniyedir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-384">The default value is five seconds.</span></span>

<span data-ttu-id="6b5ec-385">`Program.Main` ' deki aşağıdaki seçenek yapılandırması, varsayılan beş saniyelik kapatılma zaman aşımını 20 saniyeye yükseltir:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-385">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="6b5ec-386">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="6b5ec-386">Default services</span></span>

<span data-ttu-id="6b5ec-387">Aşağıdaki hizmetler konak başlatma sırasında kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-387">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="6b5ec-388">[Ortam](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="6b5ec-388">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="6b5ec-389">[Yapılandırma](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="6b5ec-389">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="6b5ec-390"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="6b5ec-390"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="6b5ec-391"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="6b5ec-391"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="6b5ec-392">[Seçenekler](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="6b5ec-392">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="6b5ec-393">[Günlüğe kaydetme](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="6b5ec-393">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="6b5ec-394">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="6b5ec-394">Host configuration</span></span>

<span data-ttu-id="6b5ec-395">Ana bilgisayar yapılandırması şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-395">Host configuration is created by:</span></span>

* <span data-ttu-id="6b5ec-396">[İçerik kökünü](#content-root) ve [ortamı](#environment)ayarlamak için <xref:Microsoft.Extensions.Hosting.IHostBuilder> uzantı yöntemleri çağrılıyor.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-396">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="6b5ec-397"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>içindeki yapılandırma sağlayıcılarından yapılandırma okunuyor.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-397">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="6b5ec-398">Uzantı yöntemleri</span><span class="sxs-lookup"><span data-stu-id="6b5ec-398">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="6b5ec-399">Uygulama anahtarı (ad)</span><span class="sxs-lookup"><span data-stu-id="6b5ec-399">Application key (name)</span></span>

<span data-ttu-id="6b5ec-400">[Ihostingenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-400">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="6b5ec-401">Değeri açıkça ayarlamak için, [Hostdefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)kullanın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-401">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="6b5ec-402">**Anahtar**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="6b5ec-402">**Key**: applicationName</span></span>  
<span data-ttu-id="6b5ec-403">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="6b5ec-403">**Type**: *string*</span></span>  
<span data-ttu-id="6b5ec-404">**Varsayılan**: uygulamanın giriş noktasını içeren derlemenin adı.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-404">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="6b5ec-405">Şunu **kullanarak ayarla**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-405">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="6b5ec-406">**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="6b5ec-406">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="6b5ec-407">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="6b5ec-407">Content root</span></span>

<span data-ttu-id="6b5ec-408">Bu ayar, konağın içerik dosyalarını aramaya başladığı yeri belirler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-408">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="6b5ec-409">**Anahtar**: contentroot</span><span class="sxs-lookup"><span data-stu-id="6b5ec-409">**Key**: contentRoot</span></span>  
<span data-ttu-id="6b5ec-410">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="6b5ec-410">**Type**: *string*</span></span>  
<span data-ttu-id="6b5ec-411">**Varsayılan**: uygulama derlemesinin bulunduğu klasörü varsayılan olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-411">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="6b5ec-412">Şunu **kullanarak ayarla**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-412">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="6b5ec-413">**Ortam değişkeni**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="6b5ec-413">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="6b5ec-414">Yol yoksa, ana bilgisayar başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-414">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="6b5ec-415">Daha fazla bilgi için bkz. [temel bilgiler: içerik kökü](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="6b5ec-415">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="6b5ec-416">Ortam</span><span class="sxs-lookup"><span data-stu-id="6b5ec-416">Environment</span></span>

<span data-ttu-id="6b5ec-417">Uygulamanın [ortamını](xref:fundamentals/environments)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-417">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="6b5ec-418">**Anahtar**: ortam</span><span class="sxs-lookup"><span data-stu-id="6b5ec-418">**Key**: environment</span></span>  
<span data-ttu-id="6b5ec-419">**Tür**: *dize*</span><span class="sxs-lookup"><span data-stu-id="6b5ec-419">**Type**: *string*</span></span>  
<span data-ttu-id="6b5ec-420">**Varsayılan**: üretim</span><span class="sxs-lookup"><span data-stu-id="6b5ec-420">**Default**: Production</span></span>  
<span data-ttu-id="6b5ec-421">Şunu **kullanarak ayarla**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="6b5ec-421">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="6b5ec-422">**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="6b5ec-422">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="6b5ec-423">Ortam herhangi bir değere ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-423">The environment can be set to any value.</span></span> <span data-ttu-id="6b5ec-424">Çerçeve tanımlı değerler `Development`, `Staging`ve `Production`içerir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-424">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="6b5ec-425">Değerler büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-425">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="6b5ec-426">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="6b5ec-426">ConfigureHostConfiguration</span></span>

<span data-ttu-id="6b5ec-427"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, ana bilgisayar için bir <xref:Microsoft.Extensions.Configuration.IConfiguration> oluşturmak üzere bir <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> kullanır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-427"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="6b5ec-428">Konak yapılandırması, uygulamanın derleme sürecinde kullanılmak üzere <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> başlatmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-428">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="6b5ec-429"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-429"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="6b5ec-430">Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-430">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="6b5ec-431">Varsayılan olarak hiçbir sağlayıcı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-431">No providers are included by default.</span></span> <span data-ttu-id="6b5ec-432">Uygulamanın <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>gereken her türlü yapılandırma sağlayıcısını açıkça belirtmeniz gerekir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-432">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="6b5ec-433">Dosya yapılandırması (örneğin, *HostSettings. JSON* dosyasından).</span><span class="sxs-lookup"><span data-stu-id="6b5ec-433">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="6b5ec-434">Ortam değişkeni yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-434">Environment variable configuration.</span></span>
* <span data-ttu-id="6b5ec-435">Komut satırı bağımsız değişken yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-435">Command-line argument configuration.</span></span>
* <span data-ttu-id="6b5ec-436">Diğer tüm gerekli yapılandırma sağlayıcıları.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-436">Any other required configuration providers.</span></span>

<span data-ttu-id="6b5ec-437">Konağın dosya yapılandırması, uygulamanın temel yolu `SetBasePath` ve ardından [dosya yapılandırma sağlayıcılarından](xref:fundamentals/configuration/index#file-configuration-provider)birine yapılan bir çağrı tarafından belirtilerek etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-437">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="6b5ec-438">Örnek uygulama bir JSON dosyası, *HostSettings. JSON*kullanır ve dosyanın konak yapılandırma ayarlarını kullanmak için <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> çağırır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-438">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="6b5ec-439">Konağın [ortam değişkeni yapılandırmasını](xref:fundamentals/configuration/index#environment-variables-configuration-provider) eklemek için konak oluşturucu üzerinde <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> çağırın.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-439">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="6b5ec-440">`AddEnvironmentVariables`, Kullanıcı tanımlı isteğe bağlı bir ön eki kabul eder.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-440">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="6b5ec-441">Örnek uygulama `PREFIX_`önekini kullanır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-441">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="6b5ec-442">Ortam değişkenleri okurken ön ek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-442">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="6b5ec-443">Örnek uygulamanın ana bilgisayarı yapılandırıldığında, `PREFIX_ENVIRONMENT` için ortam değişkeni değeri `environment` anahtar için ana bilgisayar yapılandırma değeri olur.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-443">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="6b5ec-444">[Visual Studio](https://visualstudio.microsoft.com) kullanırken veya `dotnet run`bir uygulama çalıştırırken geliştirme sırasında, *Özellikler/launchsettings. JSON* dosyasında ortam değişkenleri ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-444">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="6b5ec-445">[Visual Studio Code](https://code.visualstudio.com/), geliştirme sırasında *. vscode/Launch. JSON* dosyasında ortam değişkenleri ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-445">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="6b5ec-446">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-446">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="6b5ec-447">[Komut satırı yapılandırması](xref:fundamentals/configuration/index#command-line-configuration-provider) , <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>çağırarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-447">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="6b5ec-448">Komut satırı yapılandırması, önceki yapılandırma sağlayıcıları tarafından belirtilen yapılandırmayı geçersiz kılmak için komut satırı bağımsız değişkenlerine izin vermek üzere en son eklenir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-448">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="6b5ec-449">*HostSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-449">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="6b5ec-450">Ek yapılandırma [ApplicationName](#application-key-name) ve [contentroot](#content-root) anahtarlarıyla birlikte etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-450">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="6b5ec-451"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>kullanarak `HostBuilder` yapılandırma örneği:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-451">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="6b5ec-452">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="6b5ec-452">ConfigureAppConfiguration</span></span>

<span data-ttu-id="6b5ec-453">Uygulama yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostBuilder> uygulamasındaki <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> çağırarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="6b5ec-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, uygulama için bir <xref:Microsoft.Extensions.Configuration.IConfiguration> oluşturmak üzere bir <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> kullanır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="6b5ec-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="6b5ec-456">Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-456">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="6b5ec-457"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> tarafından oluşturulan yapılandırma, sonraki işlemler ve <xref:Microsoft.Extensions.Hosting.IHost.Services*>için [Hostbuildercontext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) ' da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-457">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="6b5ec-458">Uygulama yapılandırması, [Configurehostconfiguration](#configurehostconfiguration)tarafından belirtilen konak yapılandırmasını otomatik olarak alır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-458">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="6b5ec-459"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>kullanarak örnek uygulama yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-459">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="6b5ec-460">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-460">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="6b5ec-461">*appSettings. Development. JSON*:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-461">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="6b5ec-462">*appSettings. Production. JSON*:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-462">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="6b5ec-463">Ayar dosyalarını çıkış dizinine taşımak için, ayarlar dosyalarını proje dosyasında [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-463">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="6b5ec-464">Örnek uygulama, JSON uygulama ayarları dosyalarını ve *HostSettings. JSON* dosyasını şu `<Content>` öğesiyle taşıdıkça:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-464">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="6b5ec-465"><xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> ve <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> gibi yapılandırma uzantısı yöntemleri, [Microsoft. Extensions. Configuration. JSON](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) ve [Microsoft. Extensions. Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)gibi ek NuGet paketleri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-465">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="6b5ec-466">Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'i kullanmadığı takdirde, bu paketlerin temel [Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) paketine ek olarak projeye eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-466">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="6b5ec-467">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-467">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="6b5ec-468">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="6b5ec-468">ConfigureServices</span></span>

<span data-ttu-id="6b5ec-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>, uygulamanın [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6b5ec-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="6b5ec-471">Barındırılan hizmet, <xref:Microsoft.Extensions.Hosting.IHostedService> arabirimini uygulayan bir arka plan görevi mantığı içeren bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-471">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="6b5ec-472">Daha fazla bilgi için bkz. <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-472">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="6b5ec-473">[Örnek uygulama](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , yaşam süresi olayları, `LifetimeEventsHostedService`ve zaman aşımına uğramış bir arka plan görevi olan `TimedHostedService`uygulamaya bir hizmet eklemek için `AddHostedService` uzantısı yöntemini kullanır:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-473">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="6b5ec-474">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="6b5ec-474">ConfigureLogging</span></span>

<span data-ttu-id="6b5ec-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>, belirtilen <xref:Microsoft.Extensions.Logging.ILoggingBuilder>yapılandırmak için bir temsilci ekler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="6b5ec-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="6b5ec-477">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="6b5ec-477">UseConsoleLifetime</span></span>

<span data-ttu-id="6b5ec-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>, CTRL + C/SIGINT veya SIGTERIM dinler ve <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> çağırarak, bu işlemi başlatmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="6b5ec-479">[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*></span><span class="sxs-lookup"><span data-stu-id="6b5ec-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="6b5ec-480">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` varsayılan ömür uygulamasıyla önceden kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-480">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="6b5ec-481">Kaydedilen son yaşam süresi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-481">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="6b5ec-482">Kapsayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="6b5ec-482">Container configuration</span></span>

<span data-ttu-id="6b5ec-483">Diğer kapsayıcıları takmayı desteklemek için, ana bilgisayar bir <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-483">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="6b5ec-484">Fabrika sağlamak, dı kapsayıcı kaydının bir parçası değildir, ancak bunun yerine somut dı kapsayıcısını oluşturmak için kullanılan bir konak iç sıdır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-484">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="6b5ec-485">[Useserviceproviderfactory (ıvıceproviderfactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) , uygulamanın hizmet sağlayıcısını oluşturmak için kullanılan varsayılan fabrikası geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-485">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="6b5ec-486">Özel kapsayıcı yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> yöntemi tarafından yönetilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-486">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="6b5ec-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>, temel ana bilgisayar API 'sinin üstünde kapsayıcıyı yapılandırmak için kesin olarak belirlenmiş bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="6b5ec-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="6b5ec-489">Uygulama için bir hizmet kapsayıcısı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-489">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="6b5ec-490">Hizmet kapsayıcısı fabrikası sağlama:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-490">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="6b5ec-491">Fabrika 'yi kullanın ve uygulama için özel hizmet kapsayıcısını yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-491">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="6b5ec-492">Genişletilebilirlik</span><span class="sxs-lookup"><span data-stu-id="6b5ec-492">Extensibility</span></span>

<span data-ttu-id="6b5ec-493">Konak genişletilebilirliği <xref:Microsoft.Extensions.Hosting.IHostBuilder>uzantı yöntemleriyle gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-493">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="6b5ec-494">Aşağıdaki örnek, bir genişletme yönteminin <xref:fundamentals/host/hosted-services>gösterilen [Timedhostedservice](xref:fundamentals/host/hosted-services#timed-background-tasks) örneği ile bir <xref:Microsoft.Extensions.Hosting.IHostBuilder> uygulamasını nasıl genişlettiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-494">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="6b5ec-495">Uygulama, `T`geçirilen barındırılan hizmeti kaydetmek için `UseHostedService` uzantısı yöntemini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-495">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="6b5ec-496">Konağı yönetme</span><span class="sxs-lookup"><span data-stu-id="6b5ec-496">Manage the host</span></span>

<span data-ttu-id="6b5ec-497"><xref:Microsoft.Extensions.Hosting.IHost> uygulaması, hizmet kapsayıcısında kayıtlı <xref:Microsoft.Extensions.Hosting.IHostedService> uygulamalarını başlatma ve durdurma sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-497">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="6b5ec-498">Çalıştırın</span><span class="sxs-lookup"><span data-stu-id="6b5ec-498">Run</span></span>

<span data-ttu-id="6b5ec-499"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uygulamayı çalıştırır ve konak kapanana kadar çağıran iş parçacığını engeller:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-499"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="6b5ec-500">RunAsync</span><span class="sxs-lookup"><span data-stu-id="6b5ec-500">RunAsync</span></span>

<span data-ttu-id="6b5ec-501"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uygulamayı çalıştırır ve iptal belirteci veya kapanışı tetiklendiğinde tamamlayan bir <xref:System.Threading.Tasks.Task> döndürür:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-501"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="6b5ec-502">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="6b5ec-502">RunConsoleAsync</span></span>

<span data-ttu-id="6b5ec-503"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>, konsol desteği sağlar, Konağı oluşturur ve başlatır ve CTRL + C/SIGINT ya da SIGDÖNEM 'in kapatılmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-503"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="6b5ec-504">Başlangıç ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="6b5ec-504">Start and StopAsync</span></span>

<span data-ttu-id="6b5ec-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Konağı zaman uyumlu olarak başlatır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="6b5ec-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>, belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="6b5ec-507">StartAsync ve StopAsync</span><span class="sxs-lookup"><span data-stu-id="6b5ec-507">StartAsync and StopAsync</span></span>

<span data-ttu-id="6b5ec-508"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-508"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="6b5ec-509"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> uygulamayı sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-509"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="6b5ec-510">Waitforkapatması</span><span class="sxs-lookup"><span data-stu-id="6b5ec-510">WaitForShutdown</span></span>

<span data-ttu-id="6b5ec-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (CTRL + C/SIGINT veya SIGTERIM dinler) gibi <xref:Microsoft.Extensions.Hosting.IHostLifetime>aracılığıyla tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="6b5ec-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>çağırır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="6b5ec-513">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="6b5ec-513">WaitForShutdownAsync</span></span>

<span data-ttu-id="6b5ec-514"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>, verilen belirteç aracılığıyla kapalı tetiklendiğinde ve <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>çağıran bir <xref:System.Threading.Tasks.Task> döndürür.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-514"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="6b5ec-515">Dış denetim</span><span class="sxs-lookup"><span data-stu-id="6b5ec-515">External control</span></span>

<span data-ttu-id="6b5ec-516">Ana bilgisayarın dış denetimine, dışarıdan çağrılabilen yöntemler kullanılarak ulaşılabilecek:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-516">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="6b5ec-517"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>, <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>başlangıcında çağrılır ve bu, devam etmeden önce tamamlanana kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-517"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="6b5ec-518">Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-518">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="6b5ec-519">Ihostingenvironment arabirimi</span><span class="sxs-lookup"><span data-stu-id="6b5ec-519">IHostingEnvironment interface</span></span>

<span data-ttu-id="6b5ec-520"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>, uygulamanın barındırma ortamı hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-520"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="6b5ec-521">Özelliklerini ve uzantı yöntemlerini kullanmak için <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> almak üzere [Oluşturucu Ekleme](xref:fundamentals/dependency-injection) kullanın:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-521">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="6b5ec-522">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-522">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="6b5ec-523">Iapplicationlifetime arabirimi</span><span class="sxs-lookup"><span data-stu-id="6b5ec-523">IApplicationLifetime interface</span></span>

<span data-ttu-id="6b5ec-524"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>, düzgün kapanma istekleri dahil olmak üzere başlatma sonrası ve kapanma etkinliklerine izin verir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-524"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="6b5ec-525">Arabirimdeki üç özellik, başlangıç ve kapalı olayları tanımlayan <xref:System.Action> yöntemlerini kaydetmek için kullanılan iptal belirteçleridir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-525">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="6b5ec-526">İptal belirteci</span><span class="sxs-lookup"><span data-stu-id="6b5ec-526">Cancellation Token</span></span> | <span data-ttu-id="6b5ec-527">Tetiklendiği zaman&#8230;</span><span class="sxs-lookup"><span data-stu-id="6b5ec-527">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="6b5ec-528">Konak tam olarak başlatıldı.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-528">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="6b5ec-529">Ana bilgisayar düzgün kapanma işlemini tamamlıyor.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-529">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="6b5ec-530">Tüm isteklerin işlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-530">All requests should be processed.</span></span> <span data-ttu-id="6b5ec-531">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-531">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="6b5ec-532">Ana bilgisayar düzgün bir şekilde kapanma gerçekleştiriyor.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-532">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="6b5ec-533">İstekler hala işliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-533">Requests may still be processing.</span></span> <span data-ttu-id="6b5ec-534">Bu olay tamamlanana kadar kapalı bloklar.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-534">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="6b5ec-535">Constructor-<xref:Microsoft.Extensions.Hosting.IApplicationLifetime> hizmetini herhangi bir sınıfa ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-535">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="6b5ec-536">[Örnek uygulama](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , olayları kaydetmek için bir `LifetimeEventsHostedService` sınıfına (bir <xref:Microsoft.Extensions.Hosting.IHostedService> uygulaması) Oluşturucu ekleme işlemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-536">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="6b5ec-537">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-537">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="6b5ec-538"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>, uygulamanın sonlandırılmasını ister.</span><span class="sxs-lookup"><span data-stu-id="6b5ec-538"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="6b5ec-539">Aşağıdaki sınıf, sınıfın `Shutdown` yöntemi çağrıldığında bir uygulamayı düzgün bir şekilde kapatmak için <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> kullanır:</span><span class="sxs-lookup"><span data-stu-id="6b5ec-539">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6b5ec-540">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6b5ec-540">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
