---
title: ASP.NET Core içinde barındırılan hizmetlerle arka plan görevleri
author: guardrex
description: ASP.NET Core barındırılan hizmetler ile arka plan görevleri uygulamak öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/18/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 8df86b10d7ba853edb3265df0e02eabbf8a2c058
ms.sourcegitcommit: fa61d882be9d0c48bd681f2efcb97e05522051d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71205705"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="a3b60-103">ASP.NET Core içinde barındırılan hizmetlerle arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="a3b60-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="a3b60-104">Tarafından [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a3b60-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a3b60-105">ASP.NET Core, arka plan görevleri *barındırılan hizmetler*olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="a3b60-106">Barındırılan hizmet, <xref:Microsoft.Extensions.Hosting.IHostedService> arabirimi uygulayan bir arka plan görevi mantığı olan bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a3b60-107">Bu konuda üç barındırılan hizmet örneği sunulmaktadır:</span><span class="sxs-lookup"><span data-stu-id="a3b60-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="a3b60-108">Bir Zamanlayıcı üzerinde çalışan arka plan görevi.</span><span class="sxs-lookup"><span data-stu-id="a3b60-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="a3b60-109">[Kapsamlı bir hizmeti](xref:fundamentals/dependency-injection#service-lifetimes)etkinleştiren barındırılan hizmet.</span><span class="sxs-lookup"><span data-stu-id="a3b60-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="a3b60-110">Kapsamlı hizmet [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="a3b60-111">Sıralı olarak çalışan sıraya alınmış arka plan görevleri.</span><span class="sxs-lookup"><span data-stu-id="a3b60-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="a3b60-112">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a3b60-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a3b60-113">Örnek uygulama iki sürümde sunulmaktadır:</span><span class="sxs-lookup"><span data-stu-id="a3b60-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="a3b60-114">Web ana &ndash; bilgisayarı Web ana bilgisayarı Web uygulamalarını barındırmak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="a3b60-115">Bu konu başlığında gösterilen örnek kod, örneğin web ana bilgisayar sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="a3b60-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="a3b60-116">Daha fazla bilgi için bkz. [Web ana bilgisayarı](xref:fundamentals/host/web-host) konusu.</span><span class="sxs-lookup"><span data-stu-id="a3b60-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="a3b60-117">Genel ana &ndash; bilgisayar genel ana bilgisayarı ASP.NET Core 2,1 ' de yenidir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="a3b60-118">Daha fazla bilgi için bkz. [genel ana bilgisayar](xref:fundamentals/host/generic-host) konusu.</span><span class="sxs-lookup"><span data-stu-id="a3b60-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="a3b60-119">Çalışan hizmeti şablonu</span><span class="sxs-lookup"><span data-stu-id="a3b60-119">Worker Service template</span></span>

<span data-ttu-id="a3b60-120">ASP.NET Core Worker hizmeti şablonu, uzun süre çalışan hizmet uygulamalarını yazmak için bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="a3b60-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="a3b60-121">Şablonu bir barındırılan hizmetler uygulamasının temeli olarak kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="a3b60-121">To use the template as a basis for a hosted services app:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a3b60-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3b60-122">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a3b60-123">Yeni bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a3b60-123">Create a new project.</span></span>
1. <span data-ttu-id="a3b60-124">Seçin **ASP.NET Core Web uygulaması**.</span><span class="sxs-lookup"><span data-stu-id="a3b60-124">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="a3b60-125">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a3b60-125">Select **Next**.</span></span>
1. <span data-ttu-id="a3b60-126">**Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="a3b60-126">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a3b60-127">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="a3b60-127">Select **Create**.</span></span>
1. <span data-ttu-id="a3b60-128">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,0** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="a3b60-128">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="a3b60-129">**Çalışan hizmeti** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="a3b60-129">Select the **Worker Service** template.</span></span> <span data-ttu-id="a3b60-130">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="a3b60-130">Select **Create**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a3b60-131">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3b60-131">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a3b60-132">Yeni bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a3b60-132">Create a new project.</span></span>
1. <span data-ttu-id="a3b60-133">Kenar çubuğunda **.NET Core** altında **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="a3b60-133">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="a3b60-134">**ASP.NET Core**altında **çalışan** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="a3b60-134">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="a3b60-135">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a3b60-135">Select **Next**.</span></span>
1. <span data-ttu-id="a3b60-136">**Hedef çerçeve**Için **.NET Core 3,0** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="a3b60-136">Select **.NET Core 3.0** for the **Target Framework**.</span></span> <span data-ttu-id="a3b60-137">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a3b60-137">Select **Next**.</span></span>
1. <span data-ttu-id="a3b60-138">**Proje adı** alanına bir ad girin.</span><span class="sxs-lookup"><span data-stu-id="a3b60-138">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="a3b60-139">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="a3b60-139">Select **Create**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a3b60-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a3b60-140">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a3b60-141">Çalışan hizmeti (`worker`) şablonunu bir komut kabuğundan [DotNet New](/dotnet/core/tools/dotnet-new) komutuyla birlikte kullanın.</span><span class="sxs-lookup"><span data-stu-id="a3b60-141">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="a3b60-142">Aşağıdaki örnekte, adlı `ContosoWorker`bir çalışan hizmeti uygulaması oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a3b60-142">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="a3b60-143">Komut yürütüldüğünde, `ContosoWorker` uygulamanın bir klasörü otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a3b60-143">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---

## <a name="package"></a><span data-ttu-id="a3b60-144">Paket</span><span class="sxs-lookup"><span data-stu-id="a3b60-144">Package</span></span>

<span data-ttu-id="a3b60-145">[Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketine yönelik bir paket başvurusu, ASP.NET Core uygulamalar için örtük olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-145">A package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is added implicitly for ASP.NET Core apps.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="a3b60-146">Ihostedservice arabirimi</span><span class="sxs-lookup"><span data-stu-id="a3b60-146">IHostedService interface</span></span>

<span data-ttu-id="a3b60-147"><xref:Microsoft.Extensions.Hosting.IHostedService> Arabirim, konak tarafından yönetilen nesneler için iki yöntem tanımlar:</span><span class="sxs-lookup"><span data-stu-id="a3b60-147">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="a3b60-148">[Startasync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; arkaplan`StartAsync` görevinin başlatılacağı mantığı içerir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-148">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="a3b60-149">`StartAsync`Şu kadar *çağrılır:*</span><span class="sxs-lookup"><span data-stu-id="a3b60-149">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="a3b60-150">Uygulamanın istek işleme işlem hattı yapılandırıldı (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="a3b60-150">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="a3b60-151">Sunucu başlatıldı ve [ıapplicationlifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-151">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="a3b60-152">Varsayılan davranış, barındırılan hizmetin `StartAsync` , uygulamanın işlem hattı yapılandırıldıktan ve `ApplicationStarted` çağrıldıktan sonra çalışması için değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-152">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="a3b60-153">Varsayılan davranışı değiştirmek için, öğesini çağırdıktan`VideosWatcher` `ConfigureWebHostDefaults`sonra barındırılan hizmeti (aşağıdaki örnekte) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a3b60-153">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* <span data-ttu-id="a3b60-154">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Ana bilgisayar düzgün bir şekilde kapanma gerçekleştirirken tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-154">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="a3b60-155">`StopAsync`arka plan görevinin bitiş mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-155">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="a3b60-156">Yönetilmeyen <xref:System.IDisposable> kaynakların atılmaya yönelik uygulama ve [sonlandırıcılar (Yıkıcılar)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) .</span><span class="sxs-lookup"><span data-stu-id="a3b60-156">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="a3b60-157">İptal belirtecinin, kapanma işleminin artık düzgün şekilde olmaması gerektiğini göstermek için varsayılan beş saniyelik bir zaman aşımı vardır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-157">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="a3b60-158">Belirteç üzerinde iptal istendiğinde:</span><span class="sxs-lookup"><span data-stu-id="a3b60-158">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="a3b60-159">Uygulamanın gerçekleştirdiği diğer arka plan işlemleri iptal edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-159">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="a3b60-160">İçinde `StopAsync` çağrılan tüm yöntemler hemen döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-160">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="a3b60-161">Ancak, bir iptal işlemi istendikten&mdash;sonra görevler terk edilmez ve bu, çağıran tüm görevlerin tamamlanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="a3b60-161">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="a3b60-162">Uygulama beklenmedik bir şekilde kapanırsa (örneğin, uygulamanın işlemi başarısız olur), `StopAsync` çağrılmayabilir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-162">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="a3b60-163">Bu nedenle, veya üzerinde `StopAsync` gerçekleştirilen işlemleri çağıran Yöntemler gerçekleşmeyebilir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-163">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="a3b60-164">Varsayılan beş saniyelik kapatılma zaman aşımını uzatmak için, şunu ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="a3b60-164">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="a3b60-165"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Genel ana bilgisayar kullanırken.</span><span class="sxs-lookup"><span data-stu-id="a3b60-165"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="a3b60-166">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a3b60-166">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="a3b60-167">Web ana bilgisayarı kullanılırken, kapatılma zaman aşımı konak yapılandırma ayarı.</span><span class="sxs-lookup"><span data-stu-id="a3b60-167">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="a3b60-168">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a3b60-168">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="a3b60-169">Barındırılan hizmet, uygulama başlangıcında bir kez etkinleştirilir ve uygulama kapatılırken düzgün şekilde kapanır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-169">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="a3b60-170">Arka plan görevinin yürütülmesi sırasında bir hata oluşursa, `Dispose` çağrılmadıysa `StopAsync` bile çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-170">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice"></a><span data-ttu-id="a3b60-171">BackgroundService</span><span class="sxs-lookup"><span data-stu-id="a3b60-171">BackgroundService</span></span>

<span data-ttu-id="a3b60-172">`BackgroundService`, uzun süre çalışan <xref:Microsoft.Extensions.Hosting.IHostedService>uygulamaya yönelik bir temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-172">`BackgroundService` is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="a3b60-173">`BackgroundService`arka plan işlemleri için iki yöntem tanımlar:</span><span class="sxs-lookup"><span data-stu-id="a3b60-173">`BackgroundService` defines two methods for background operations:</span></span>

* <span data-ttu-id="a3b60-174">`ExecuteAsync(CancellationToken stoppingToken)`Başladığındaçağırılır&ndash;. `ExecuteAsync` <xref:Microsoft.Extensions.Hosting.IHostedService></span><span class="sxs-lookup"><span data-stu-id="a3b60-174">`ExecuteAsync(CancellationToken stoppingToken)` &ndash; `ExecuteAsync` Called when the <xref:Microsoft.Extensions.Hosting.IHostedService> starts.</span></span> <span data-ttu-id="a3b60-175">Uygulama, gerçekleştirilen uzun süren `Task` işlemlerin ömrünü temsil eden bir döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-175">The implementation should return a `Task` that represents the lifetime of the long running operations performed.</span></span> <span data-ttu-id="a3b60-176">[Ihostedservice. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) çağrıldığında tetiklenen.`stoppingToken`</span><span class="sxs-lookup"><span data-stu-id="a3b60-176">The `stoppingToken` Triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span>
* <span data-ttu-id="a3b60-177">`StopAsync(CancellationToken stoppingToken)`&ndash; UygulamaKonağı`StopAsync` düzgün kapanma yaparken tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-177">`StopAsync(CancellationToken stoppingToken)` &ndash; `StopAsync` is triggered when the application host is performing a graceful shutdown.</span></span> <span data-ttu-id="a3b60-178">, `stoppingToken` Kapanma işleminin artık düzgün şekilde olmaması gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-178">The `stoppingToken` indicates that the shutdown process should no longer be graceful.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="a3b60-179">Zamanlanmış arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="a3b60-179">Timed background tasks</span></span>

<span data-ttu-id="a3b60-180">Zamanlanmış bir arka plan görevi, [System. Threading. Timer](xref:System.Threading.Timer) sınıfından kullanımını sağlar.</span><span class="sxs-lookup"><span data-stu-id="a3b60-180">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="a3b60-181">Zamanlayıcı, görevin `DoWork` metodunu tetikler.</span><span class="sxs-lookup"><span data-stu-id="a3b60-181">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="a3b60-182">Zamanlayıcı devre dışı bırakılır `StopAsync` ve hizmet kapsayıcısı `Dispose`bırakıldığında bırakıldı:</span><span class="sxs-lookup"><span data-stu-id="a3b60-182">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="a3b60-183">Hizmet, `IHostBuilder.ConfigureServices` (*program.cs*) `AddHostedService` öğesine uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="a3b60-183">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="a3b60-184">Bir arka plan görevinde kapsamlı bir hizmeti kullanma</span><span class="sxs-lookup"><span data-stu-id="a3b60-184">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="a3b60-185">İçindeki`BackgroundService` [kapsamlı hizmetleri](xref:fundamentals/dependency-injection#service-lifetimes) kullanmak için bir kapsam oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a3b60-185">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a `BackgroundService`, create a scope.</span></span> <span data-ttu-id="a3b60-186">Barındırılan hizmet için varsayılan olarak kapsam oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="a3b60-186">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="a3b60-187">Kapsamlı arka plan görev hizmeti, arka plan görevinin mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-187">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="a3b60-188">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="a3b60-188">In the following example:</span></span>

* <span data-ttu-id="a3b60-189">Hizmet zaman uyumsuz.</span><span class="sxs-lookup"><span data-stu-id="a3b60-189">The service is asynchronous.</span></span> <span data-ttu-id="a3b60-190">`DoWork` Yöntemi bir`Task`döndürür.</span><span class="sxs-lookup"><span data-stu-id="a3b60-190">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="a3b60-191">Tanıtım amacıyla, `DoWork` yöntemde on saniyelik bir gecikme beklenir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-191">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="a3b60-192">Hizmete <xref:Microsoft.Extensions.Logging.ILogger> eklenen bir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-192">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="a3b60-193">Barındırılan hizmet, `DoWork` yöntemini çağırmak için kapsamlı arka plan görev hizmetini çözümlemek üzere bir kapsam oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a3b60-193">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="a3b60-194">`DoWork``Task` şunu`ExecuteAsync`döndürür:</span><span class="sxs-lookup"><span data-stu-id="a3b60-194">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="a3b60-195">Hizmetler ' de `IHostBuilder.ConfigureServices` kaydedilir (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="a3b60-195">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="a3b60-196">Barındırılan hizmet, `AddHostedService` uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="a3b60-196">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="a3b60-197">Sıraya alınan arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="a3b60-197">Queued background tasks</span></span>

<span data-ttu-id="a3b60-198">Arka plan görev kuyruğu, .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> 'i temel alır ([ASP.NET Core için isteğe](https://github.com/aspnet/Hosting/issues/1280)bağlı olarak zamanlanmış olarak zamanlandı):</span><span class="sxs-lookup"><span data-stu-id="a3b60-198">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="a3b60-199">Aşağıdaki `QueueHostedService` örnekte:</span><span class="sxs-lookup"><span data-stu-id="a3b60-199">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="a3b60-200">Yöntemi, ' de `Task` `ExecuteAsync`beklemiş bir döndürür. `BackgroundProcessing`</span><span class="sxs-lookup"><span data-stu-id="a3b60-200">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="a3b60-201">Kuyruktaki arka plan görevleri, içinde `BackgroundProcessing`kuyruklanmış ve yürütülür.</span><span class="sxs-lookup"><span data-stu-id="a3b60-201">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28,39-40,44)]

<span data-ttu-id="a3b60-202">Bir `MonitorLoop` hizmet, `w` giriş cihazında anahtar her seçildiğinde barındırılan hizmet için görevleri sıraya alır:</span><span class="sxs-lookup"><span data-stu-id="a3b60-202">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="a3b60-203">, `IBackgroundTaskQueue` `MonitorLoop` Hizmete eklenir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-203">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="a3b60-204">`IBackgroundTaskQueue.QueueBackgroundWorkItem`bir iş öğesini kuyruğa almak için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-204">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="a3b60-205">Hizmetler ' de `IHostBuilder.ConfigureServices` kaydedilir (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="a3b60-205">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="a3b60-206">Barındırılan hizmet, `AddHostedService` uzantı yöntemiyle kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="a3b60-206">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a3b60-207">ASP.NET Core, arka plan görevleri *barındırılan hizmetler*olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-207">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="a3b60-208">Barındırılan hizmet, <xref:Microsoft.Extensions.Hosting.IHostedService> arabirimi uygulayan bir arka plan görevi mantığı olan bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-208">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a3b60-209">Bu konuda üç barındırılan hizmet örneği sunulmaktadır:</span><span class="sxs-lookup"><span data-stu-id="a3b60-209">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="a3b60-210">Bir Zamanlayıcı üzerinde çalışan arka plan görevi.</span><span class="sxs-lookup"><span data-stu-id="a3b60-210">Background task that runs on a timer.</span></span>
* <span data-ttu-id="a3b60-211">[Kapsamlı bir hizmeti](xref:fundamentals/dependency-injection#service-lifetimes)etkinleştiren barındırılan hizmet.</span><span class="sxs-lookup"><span data-stu-id="a3b60-211">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="a3b60-212">Kapsamlı hizmet [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kullanabilir</span><span class="sxs-lookup"><span data-stu-id="a3b60-212">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="a3b60-213">Sıralı olarak çalışan sıraya alınmış arka plan görevleri.</span><span class="sxs-lookup"><span data-stu-id="a3b60-213">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="a3b60-214">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a3b60-214">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a3b60-215">Örnek uygulama iki sürümde sunulmaktadır:</span><span class="sxs-lookup"><span data-stu-id="a3b60-215">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="a3b60-216">Web ana &ndash; bilgisayarı Web ana bilgisayarı Web uygulamalarını barındırmak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-216">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="a3b60-217">Bu konu başlığında gösterilen örnek kod, örneğin web ana bilgisayar sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="a3b60-217">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="a3b60-218">Daha fazla bilgi için bkz. [Web ana bilgisayarı](xref:fundamentals/host/web-host) konusu.</span><span class="sxs-lookup"><span data-stu-id="a3b60-218">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="a3b60-219">Genel ana &ndash; bilgisayar genel ana bilgisayarı ASP.NET Core 2,1 ' de yenidir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-219">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="a3b60-220">Daha fazla bilgi için bkz. [genel ana bilgisayar](xref:fundamentals/host/generic-host) konusu.</span><span class="sxs-lookup"><span data-stu-id="a3b60-220">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="package"></a><span data-ttu-id="a3b60-221">Paket</span><span class="sxs-lookup"><span data-stu-id="a3b60-221">Package</span></span>

<span data-ttu-id="a3b60-222">Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a3b60-222">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="a3b60-223">Ihostedservice arabirimi</span><span class="sxs-lookup"><span data-stu-id="a3b60-223">IHostedService interface</span></span>

<span data-ttu-id="a3b60-224">Barındırılan hizmetler, <xref:Microsoft.Extensions.Hosting.IHostedService> arabirimini uygular.</span><span class="sxs-lookup"><span data-stu-id="a3b60-224">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a3b60-225">Arabirim, konak tarafından yönetilen nesneler için iki yöntem tanımlar:</span><span class="sxs-lookup"><span data-stu-id="a3b60-225">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="a3b60-226">[Startasync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; arkaplan`StartAsync` görevinin başlatılacağı mantığı içerir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-226">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="a3b60-227">[Web konağını](xref:fundamentals/host/web-host) `StartAsync` kullanırken sunucu başlatıldıktan ve [ıapplicationlifetime değerinden sonra çağrılır. applicationstarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-227">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="a3b60-228">[Genel ana bilgisayar](xref:fundamentals/host/generic-host)kullanılırken, `StartAsync` tetiklendikten önce `ApplicationStarted` çağrılır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-228">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="a3b60-229">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Ana bilgisayar düzgün bir şekilde kapanma gerçekleştirirken tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-229">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="a3b60-230">`StopAsync`arka plan görevinin bitiş mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-230">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="a3b60-231">Yönetilmeyen <xref:System.IDisposable> kaynakların atılmaya yönelik uygulama ve [sonlandırıcılar (Yıkıcılar)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) .</span><span class="sxs-lookup"><span data-stu-id="a3b60-231">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="a3b60-232">İptal belirtecinin, kapanma işleminin artık düzgün şekilde olmaması gerektiğini göstermek için varsayılan beş saniyelik bir zaman aşımı vardır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-232">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="a3b60-233">Belirteç üzerinde iptal istendiğinde:</span><span class="sxs-lookup"><span data-stu-id="a3b60-233">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="a3b60-234">Uygulamanın gerçekleştirdiği diğer arka plan işlemleri iptal edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-234">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="a3b60-235">İçinde `StopAsync` çağrılan tüm yöntemler hemen döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-235">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="a3b60-236">Ancak, bir iptal işlemi istendikten&mdash;sonra görevler terk edilmez ve bu, çağıran tüm görevlerin tamamlanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="a3b60-236">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="a3b60-237">Uygulama beklenmedik bir şekilde kapanırsa (örneğin, uygulamanın işlemi başarısız olur), `StopAsync` çağrılmayabilir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-237">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="a3b60-238">Bu nedenle, veya üzerinde `StopAsync` gerçekleştirilen işlemleri çağıran Yöntemler gerçekleşmeyebilir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-238">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="a3b60-239">Varsayılan beş saniyelik kapatılma zaman aşımını uzatmak için, şunu ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="a3b60-239">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="a3b60-240"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Genel ana bilgisayar kullanırken.</span><span class="sxs-lookup"><span data-stu-id="a3b60-240"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="a3b60-241">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a3b60-241">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="a3b60-242">Web ana bilgisayarı kullanılırken, kapatılma zaman aşımı konak yapılandırma ayarı.</span><span class="sxs-lookup"><span data-stu-id="a3b60-242">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="a3b60-243">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a3b60-243">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="a3b60-244">Barındırılan hizmet, uygulama başlangıcında bir kez etkinleştirilir ve uygulama kapatılırken düzgün şekilde kapanır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-244">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="a3b60-245">Arka plan görevinin yürütülmesi sırasında bir hata oluşursa, `Dispose` çağrılmadıysa `StopAsync` bile çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-245">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="a3b60-246">Zamanlanmış arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="a3b60-246">Timed background tasks</span></span>

<span data-ttu-id="a3b60-247">Zamanlanmış bir arka plan görevi, [System. Threading. Timer](xref:System.Threading.Timer) sınıfından kullanımını sağlar.</span><span class="sxs-lookup"><span data-stu-id="a3b60-247">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="a3b60-248">Zamanlayıcı, görevin `DoWork` metodunu tetikler.</span><span class="sxs-lookup"><span data-stu-id="a3b60-248">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="a3b60-249">Zamanlayıcı devre dışı bırakılır `StopAsync` ve hizmet kapsayıcısı `Dispose`bırakıldığında bırakıldı:</span><span class="sxs-lookup"><span data-stu-id="a3b60-249">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="a3b60-250">Hizmet, `AddHostedService` uzantı yöntemiyle kaydedilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a3b60-250">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="a3b60-251">Bir arka plan görevinde kapsamlı bir hizmeti kullanma</span><span class="sxs-lookup"><span data-stu-id="a3b60-251">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="a3b60-252">[Kapsamlı hizmetleri](xref:fundamentals/dependency-injection#service-lifetimes) bir `IHostedService`içinde kullanmak için bir kapsam oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a3b60-252">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="a3b60-253">Barındırılan hizmet için varsayılan olarak kapsam oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="a3b60-253">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="a3b60-254">Kapsamlı arka plan görev hizmeti, arka plan görevinin mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-254">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="a3b60-255">Aşağıdaki örnekte, hizmetine bir <xref:Microsoft.Extensions.Logging.ILogger> hizmet eklenmiş olur:</span><span class="sxs-lookup"><span data-stu-id="a3b60-255">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="a3b60-256">Barındırılan hizmet, kendi `DoWork` metodunu çağırmak için kapsamlı arka plan görev hizmetini çözümlemek üzere bir kapsam oluşturur:</span><span class="sxs-lookup"><span data-stu-id="a3b60-256">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="a3b60-257">Hizmetler ' de `Startup.ConfigureServices`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-257">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a3b60-258">Uygulama, `AddHostedService` uzantı yöntemiyle kaydedilir: `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="a3b60-258">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="a3b60-259">Sıraya alınan arka plan görevleri</span><span class="sxs-lookup"><span data-stu-id="a3b60-259">Queued background tasks</span></span>

<span data-ttu-id="a3b60-260">Arka plan görev kuyruğu, .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> 'i temel alır ([ASP.NET Core için isteğe](https://github.com/aspnet/Hosting/issues/1280)bağlı olarak zamanlanmış olarak zamanlandı):</span><span class="sxs-lookup"><span data-stu-id="a3b60-260">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="a3b60-261">' `QueueHostedService`De, kuyruktaki arka plan görevleri <xref:Microsoft.Extensions.Hosting.BackgroundService>, uzun süre çalışan `IHostedService`uygulama için temel bir sınıf olan olarak kuyruğa alınır ve yürütülür.</span><span class="sxs-lookup"><span data-stu-id="a3b60-261">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="a3b60-262">Hizmetler ' de `Startup.ConfigureServices`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="a3b60-262">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a3b60-263">Uygulama, `AddHostedService` uzantı yöntemiyle kaydedilir: `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="a3b60-263">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="a3b60-264">Dizin sayfası model sınıfında:</span><span class="sxs-lookup"><span data-stu-id="a3b60-264">In the Index page model class:</span></span>

* <span data-ttu-id="a3b60-265">Oluşturucuya eklenir ve öğesine `Queue`atanır. `IBackgroundTaskQueue`</span><span class="sxs-lookup"><span data-stu-id="a3b60-265">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="a3b60-266">Eklenen ve öğesine `_serviceScopeFactory`atandı. <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory></span><span class="sxs-lookup"><span data-stu-id="a3b60-266">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="a3b60-267">Fabrika, bir kapsam içinde hizmet oluşturmak için <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>kullanılan örnekleri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a3b60-267">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="a3b60-268">Uygulama `AppDbContext` (bir tekil hizmet) içinde `IBackgroundTaskQueue` veritabanı kayıtları yazmak için uygulamanın ( [kapsamlı bir hizmet](xref:fundamentals/dependency-injection#service-lifetimes)) kullanılabilmesi için bir kapsam oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a3b60-268">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="a3b60-269">Dizin `OnPostAddTask` sayfasında **Görev Ekle** düğmesi seçildiğinde Yöntem yürütülür.</span><span class="sxs-lookup"><span data-stu-id="a3b60-269">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="a3b60-270">`QueueBackgroundWorkItem`bir iş öğesini kuyruğa almak için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="a3b60-270">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a3b60-271">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a3b60-271">Additional resources</span></span>

* [<span data-ttu-id="a3b60-272">Ihostedservice ve BackgroundService sınıfıyla mikro hizmetlerde arka plan görevleri uygulama</span><span class="sxs-lookup"><span data-stu-id="a3b60-272">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
