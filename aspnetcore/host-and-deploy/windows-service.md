---
title: Windows hizmetinde konak ASP.NET Core
author: guardrex
description: ASP.NET Core uygulamasının bir Windows hizmetinde nasıl barındıralınacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 014585cd1e170fc94f7f577e11ec19824e54572f
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659861"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="462ec-103">Windows hizmetinde konak ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="462ec-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="462ec-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="462ec-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="462ec-105">Bir ASP.NET Core uygulaması, IIS kullanmadan Windows [hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak Windows üzerinde barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="462ec-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="462ec-106">Windows hizmeti olarak barındırıldığı zaman, uygulama otomatik olarak sunucu yeniden başlatıldıktan sonra başlatılır.</span><span class="sxs-lookup"><span data-stu-id="462ec-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="462ec-107">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="462ec-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="462ec-108">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="462ec-108">Prerequisites</span></span>

* [<span data-ttu-id="462ec-109">ASP.NET Core SDK 2,1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="462ec-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="462ec-110">PowerShell 6,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="462ec-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="462ec-111">Çalışan hizmeti şablonu</span><span class="sxs-lookup"><span data-stu-id="462ec-111">Worker Service template</span></span>

<span data-ttu-id="462ec-112">ASP.NET Core Worker hizmeti şablonu, uzun süre çalışan hizmet uygulamalarını yazmak için bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="462ec-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="462ec-113">Şablonu bir Windows Hizmeti uygulamasının temeli olarak kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="462ec-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="462ec-114">.NET Core şablonundan bir çalışan hizmeti uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="462ec-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="462ec-115">Çalışan hizmeti uygulamasını bir Windows hizmeti olarak çalışacak şekilde güncelleştirmek için [uygulama yapılandırma](#app-configuration) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="462ec-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

::: moniker-end

## <a name="app-configuration"></a><span data-ttu-id="462ec-116">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="462ec-116">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="462ec-117">Uygulama, [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)için bir paket başvurusu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="462ec-117">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="462ec-118">`IHostBuilder.UseWindowsService` ana bilgisayar oluşturulurken çağrılır.</span><span class="sxs-lookup"><span data-stu-id="462ec-118">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="462ec-119">Uygulama bir Windows hizmeti olarak çalışıyorsa, yöntemi:</span><span class="sxs-lookup"><span data-stu-id="462ec-119">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="462ec-120">Ana bilgisayar ömrünü `WindowsServiceLifetime` olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="462ec-120">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="462ec-121">[İçerik kökünü](xref:fundamentals/index#content-root)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="462ec-121">Sets the [content root](xref:fundamentals/index#content-root).</span></span>
* <span data-ttu-id="462ec-122">Varsayılan kaynak adı olarak uygulama adı ile olay günlüğüne günlük kaydını sağlar.</span><span class="sxs-lookup"><span data-stu-id="462ec-122">Enables logging to the event log with the application name as the default source name.</span></span>
  * <span data-ttu-id="462ec-123">Günlük düzeyi, appSettings 'de `Logging:LogLevel:Default` anahtarı kullanılarak yapılandırılabilir *. Production. JSON* dosyası.</span><span class="sxs-lookup"><span data-stu-id="462ec-123">The log level can be configured using the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>
  * <span data-ttu-id="462ec-124">Yeni olay kaynakları yalnızca yöneticiler tarafından oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="462ec-124">Only administrators can create new event sources.</span></span> <span data-ttu-id="462ec-125">Uygulama adı kullanılarak bir olay kaynağı oluşturuoluşturumadığında, *uygulama* kaynağına bir uyarı kaydedilir ve olay günlükleri devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="462ec-125">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="462ec-126">*Program.cs*`CreateHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="462ec-126">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="462ec-127">Aşağıdaki örnek uygulamalar bu konuya eşlik eder:</span><span class="sxs-lookup"><span data-stu-id="462ec-127">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="462ec-128">Arka plan çalışan hizmeti örneği, arka plan görevleri için [barındırılan Hizmetleri](xref:fundamentals/host/hosted-services) kullanan [çalışan hizmeti şablonunu](#worker-service-template) temel alan, Web olmayan bir uygulama örneğini &ndash;.</span><span class="sxs-lookup"><span data-stu-id="462ec-128">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="462ec-129">Web App Service örneği, arka plan görevleri için [barındırılan hizmetlerle](xref:fundamentals/host/hosted-services) Windows hizmeti olarak çalışan bir Razor pages Web uygulaması örneği &ndash;.</span><span class="sxs-lookup"><span data-stu-id="462ec-129">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="462ec-130">MVC Kılavuzu için <xref:mvc/overview> ve <xref:migration/22-to-30>altındaki makalelere bakın.</span><span class="sxs-lookup"><span data-stu-id="462ec-130">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="462ec-131">Uygulama [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) ve [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)için paket başvuruları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="462ec-131">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="462ec-132">Bir hizmetin dışında çalışırken test ve hata ayıklamak için, uygulamanın bir hizmet olarak mı yoksa bir konsol uygulaması mi çalıştığını belirleme kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="462ec-132">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="462ec-133">Hata ayıklayıcının ekli olduğunu veya bir `--console` anahtarının mevcut olup olmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="462ec-133">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="462ec-134">Her iki koşul de geçerliyse (uygulama bir hizmet olarak çalıştırılmadıysa) <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> ' ı çağırın.</span><span class="sxs-lookup"><span data-stu-id="462ec-134">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="462ec-135">Koşullar yanlışsa (uygulama bir hizmet olarak çalıştırılır):</span><span class="sxs-lookup"><span data-stu-id="462ec-135">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="462ec-136"><xref:System.IO.Directory.SetCurrentDirectory*> çağırın ve uygulamanın yayımlanan konumunun yolunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="462ec-136">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="462ec-137">Bir Windows hizmeti uygulaması, <xref:System.IO.Directory.GetCurrentDirectory*> çağrıldığında *C:\\windows\\system32* klasörünü döndürdüğünden yolu almak için <xref:System.IO.Directory.GetCurrentDirectory*> çağırmayın.</span><span class="sxs-lookup"><span data-stu-id="462ec-137">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="462ec-138">Daha fazla bilgi için [geçerli dizin ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="462ec-138">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="462ec-139">Bu adım, uygulama `CreateWebHostBuilder` ' da yapılandırılmadan önce gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="462ec-139">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="462ec-140">Uygulamayı bir hizmet olarak çalıştırmak için <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="462ec-140">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="462ec-141">Komut satırı [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#command-line-configuration-provider) komut satırı bağımsız değişkenleri için ad-değer çiftleri gerektirdiğinden, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bağımsız değişkenleri almadan önce `--console` anahtarı bağımsız değişkenlerden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="462ec-141">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="462ec-142">Windows olay günlüğü 'ne yazmak için, EventLog sağlayıcısını <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> ' a ekleyin.</span><span class="sxs-lookup"><span data-stu-id="462ec-142">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="462ec-143">Günlük kaydı düzeyini appSettings 'de `Logging:LogLevel:Default` anahtarıyla ayarlayın *. Production. JSON* dosyası.</span><span class="sxs-lookup"><span data-stu-id="462ec-143">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="462ec-144">Örnek uygulamadan aşağıdaki örnekte, uygulamadaki ömür olaylarını işlemek için <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> yerine `RunAsCustomService` çağrılır.</span><span class="sxs-lookup"><span data-stu-id="462ec-144">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="462ec-145">Daha fazla bilgi için [olayları başlatma ve durdurma olaylarını](#handle-starting-and-stopping-events) inceleyin bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="462ec-145">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="462ec-146">Dağıtım türü</span><span class="sxs-lookup"><span data-stu-id="462ec-146">Deployment type</span></span>

<span data-ttu-id="462ec-147">Dağıtım senaryoları hakkında bilgi ve öneriler için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="462ec-147">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="462ec-148">SDK</span><span class="sxs-lookup"><span data-stu-id="462ec-148">SDK</span></span>

<span data-ttu-id="462ec-149">Razor Pages veya MVC çerçevelerini kullanan bir Web uygulaması tabanlı hizmet için, proje dosyasında Web SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="462ec-149">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="462ec-150">Hizmet yalnızca arka plan görevlerini (örneğin, [barındırılan hizmetler](xref:fundamentals/host/hosted-services)) yürütülüyorsa, proje dosyasında çalışan SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="462ec-150">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="462ec-151">Çerçeveye bağımlı dağıtım (FDD)</span><span class="sxs-lookup"><span data-stu-id="462ec-151">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="462ec-152">Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core 'un paylaşılan sistem genelindeki bir sürümünün varlığına dayanır.</span><span class="sxs-lookup"><span data-stu-id="462ec-152">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="462ec-153">Bu makaledeki kılavuzdan sonra FDD senaryosu benimsendiği zaman SDK, *çerçeveye bağlı yürütülebilir dosya*olarak adlandırılan yürütülebilir bir dosya ( *. exe*) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="462ec-153">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="462ec-154">[Web SDK](#sdk)kullanıyorsanız, normalde bir ASP.NET Core uygulaması yayımlarken üretilen bir *Web. config* dosyası Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="462ec-154">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="462ec-155">*Web. config* dosyasının oluşturulmasını devre dışı bırakmak için `<IsTransformWebConfigDisabled>` özelliğini `true` olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="462ec-155">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="462ec-156">Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) ([\<runtimeıdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)), hedef Framework 'ü içerir.</span><span class="sxs-lookup"><span data-stu-id="462ec-156">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="462ec-157">Aşağıdaki örnekte, RID `win7-x64` olarak ayarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="462ec-157">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="462ec-158">`<SelfContained>` özelliği `false`olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="462ec-158">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="462ec-159">Bu özellikler SDK 'nın Windows için bir yürütülebilir ( *. exe*) dosya ve paylaşılan .NET Core çerçevesine bağlı bir uygulama oluşturmasını ister.</span><span class="sxs-lookup"><span data-stu-id="462ec-159">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="462ec-160">Bir ASP.NET Core uygulaması yayımlandığında normalde üretilen bir *Web. config* dosyası, Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="462ec-160">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="462ec-161">*Web. config* dosyasının oluşturulmasını devre dışı bırakmak için `<IsTransformWebConfigDisabled>` özelliğini `true` olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="462ec-161">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="462ec-162">Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) ([\<runtimeıdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)), hedef Framework 'ü içerir.</span><span class="sxs-lookup"><span data-stu-id="462ec-162">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="462ec-163">Aşağıdaki örnekte, RID `win7-x64` olarak ayarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="462ec-163">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="462ec-164">`<SelfContained>` özelliği `false`olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="462ec-164">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="462ec-165">Bu özellikler SDK 'nın Windows için bir yürütülebilir ( *. exe*) dosya ve paylaşılan .NET Core çerçevesine bağlı bir uygulama oluşturmasını ister.</span><span class="sxs-lookup"><span data-stu-id="462ec-165">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="462ec-166">`<UseAppHost>` özelliği `true`olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="462ec-166">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="462ec-167">Bu özellik, bir FDD için bir etkinleştirme yolu (yürütülebilir, *. exe*) ile hizmeti sağlar.</span><span class="sxs-lookup"><span data-stu-id="462ec-167">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="462ec-168">Bir ASP.NET Core uygulaması yayımlandığında normalde üretilen bir *Web. config* dosyası, Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="462ec-168">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="462ec-169">*Web. config* dosyasının oluşturulmasını devre dışı bırakmak için `<IsTransformWebConfigDisabled>` özelliğini `true` olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="462ec-169">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="462ec-170">Kendi içinde dağıtım (SCD)</span><span class="sxs-lookup"><span data-stu-id="462ec-170">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="462ec-171">Kendinden bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir Framework varlığına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="462ec-171">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="462ec-172">Çalışma zamanı ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="462ec-172">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="462ec-173">Hedef Framework 'ü içeren `<PropertyGroup>` ' de bir Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) bulunur:</span><span class="sxs-lookup"><span data-stu-id="462ec-173">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="462ec-174">Birden çok RID için yayımlamak için:</span><span class="sxs-lookup"><span data-stu-id="462ec-174">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="462ec-175">RID 'leri, noktalı virgülle ayrılmış bir liste olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="462ec-175">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="462ec-176">[\<Runtimetanýmlayýcýtanımlayıcıları >](/dotnet/core/tools/csproj#runtimeidentifiers) (plural) için özellik adını kullanın.</span><span class="sxs-lookup"><span data-stu-id="462ec-176">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="462ec-177">Daha fazla bilgi için bkz. [.NET Core RID kataloğu](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="462ec-177">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="462ec-178">`<SelfContained>` Özellik `true`olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="462ec-178">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="462ec-179">Hizmet Kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="462ec-179">Service user account</span></span>

<span data-ttu-id="462ec-180">Bir hizmet için Kullanıcı hesabı oluşturmak için, bir yönetim PowerShell 6 komut kabuğundan [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ini kullanın.</span><span class="sxs-lookup"><span data-stu-id="462ec-180">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="462ec-181">Windows 10 Ekim 2018 Güncelleştirmesi (sürüm 1809/Build 10.0.17763) veya sonraki sürümler:</span><span class="sxs-lookup"><span data-stu-id="462ec-181">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="462ec-182">Windows 10 Ekim 2018 (sürüm 1809/Build 10.0.17763) sürümünden önceki Windows IŞLETIM sistemlerinde:</span><span class="sxs-lookup"><span data-stu-id="462ec-182">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

<span data-ttu-id="462ec-183">İstendiğinde [güçlü bir parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.</span><span class="sxs-lookup"><span data-stu-id="462ec-183">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="462ec-184">`-AccountExpires` parametresi [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ine bir süre sonu <xref:System.DateTime>sağlanmamışsa hesabın süresi dolmaz.</span><span class="sxs-lookup"><span data-stu-id="462ec-184">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="462ec-185">Daha fazla bilgi için bkz. [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [hizmet Kullanıcı hesapları](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="462ec-185">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="462ec-186">Active Directory kullanırken kullanıcıları yönetmeye yönelik alternatif bir yaklaşım, yönetilen hizmet hesaplarını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="462ec-186">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="462ec-187">Daha fazla bilgi için bkz. [Grup yönetilen hizmet hesaplarına genel bakış](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="462ec-187">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="462ec-188">Hizmet hakları olarak oturum açma</span><span class="sxs-lookup"><span data-stu-id="462ec-188">Log on as a service rights</span></span>

<span data-ttu-id="462ec-189">Hizmet Kullanıcı hesabı için *hizmet hakları olarak oturum* açma oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="462ec-189">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="462ec-190">Yerel Güvenlik Ilkesi düzenleyicisini, *secpol. msc*' i çalıştırarak açın.</span><span class="sxs-lookup"><span data-stu-id="462ec-190">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="462ec-191">**Yerel ilkeler** düğümünü genişletin ve **Kullanıcı hakları ataması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="462ec-191">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="462ec-192">**Hizmet olarak oturum** açma ilkesi açın.</span><span class="sxs-lookup"><span data-stu-id="462ec-192">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="462ec-193">**Kullanıcı veya Grup Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="462ec-193">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="462ec-194">Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (Kullanıcı hesabı) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="462ec-194">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="462ec-195">Kullanıcı hesabını (`{DOMAIN OR COMPUTER NAME\USER}`) nesne adı alanına yazın ve kullanıcıyı ilkeye eklemek için **Tamam** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="462ec-195">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="462ec-196">**Gelişmiş**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="462ec-196">Select **Advanced**.</span></span> <span data-ttu-id="462ec-197">**Şimdi bul**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="462ec-197">Select **Find Now**.</span></span> <span data-ttu-id="462ec-198">Listeden Kullanıcı hesabını seçin.</span><span class="sxs-lookup"><span data-stu-id="462ec-198">Select the user account from the list.</span></span> <span data-ttu-id="462ec-199">**Tamam ' ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="462ec-199">Select **OK**.</span></span> <span data-ttu-id="462ec-200">Kullanıcıyı ilkeye eklemek için yeniden **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="462ec-200">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="462ec-201">Değişiklikleri kabul etmek için **Tamam ' ı** veya **Uygula** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="462ec-201">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="462ec-202">Windows hizmetini oluşturma ve yönetme</span><span class="sxs-lookup"><span data-stu-id="462ec-202">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="462ec-203">Hizmet oluşturma</span><span class="sxs-lookup"><span data-stu-id="462ec-203">Create a service</span></span>

<span data-ttu-id="462ec-204">Bir hizmeti kaydetmek için PowerShell komutlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="462ec-204">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="462ec-205">Bir yönetim PowerShell 6 komut kabuğundan aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="462ec-205">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="462ec-206">Ana bilgisayardaki uygulamanın klasörünün yolunu &ndash; `{EXE PATH}` (örneğin, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="462ec-206">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="462ec-207">Uygulamanın yürütülebilir dosyasını yola eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="462ec-207">Don't include the app's executable in the path.</span></span> <span data-ttu-id="462ec-208">Sondaki eğik çizgi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="462ec-208">A trailing slash isn't required.</span></span>
* <span data-ttu-id="462ec-209">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; hizmet Kullanıcı hesabı (örneğin, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="462ec-209">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="462ec-210">`{NAME}` &ndash; hizmet adı (örneğin, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="462ec-210">`{NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="462ec-211">`{EXE FILE PATH}` &ndash; uygulamanın yürütülebilir yolu (örneğin, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="462ec-211">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="462ec-212">Yürütülebilir dosyanın dosya adını uzantısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="462ec-212">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="462ec-213">`{DESCRIPTION}` &ndash; hizmet açıklaması (örneğin, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="462ec-213">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="462ec-214">`{DISPLAY NAME}` &ndash; hizmet görünen adı (örneğin, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="462ec-214">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="462ec-215">Hizmet başlatma</span><span class="sxs-lookup"><span data-stu-id="462ec-215">Start a service</span></span>

<span data-ttu-id="462ec-216">Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:</span><span class="sxs-lookup"><span data-stu-id="462ec-216">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {NAME}
```

<span data-ttu-id="462ec-217">Komutun başlatılması birkaç saniye sürer.</span><span class="sxs-lookup"><span data-stu-id="462ec-217">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="462ec-218">Hizmetin durumunu belirleme</span><span class="sxs-lookup"><span data-stu-id="462ec-218">Determine a service's status</span></span>

<span data-ttu-id="462ec-219">Bir hizmetin durumunu denetlemek için aşağıdaki PowerShell 6 komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="462ec-219">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {NAME}
```

<span data-ttu-id="462ec-220">Durum, aşağıdaki değerlerden biri olarak bildirilir:</span><span class="sxs-lookup"><span data-stu-id="462ec-220">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="462ec-221">Bir hizmeti durdur</span><span class="sxs-lookup"><span data-stu-id="462ec-221">Stop a service</span></span>

<span data-ttu-id="462ec-222">Aşağıdaki PowerShell 6 komutuyla bir hizmeti durdurun:</span><span class="sxs-lookup"><span data-stu-id="462ec-222">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="462ec-223">Hizmeti Kaldır</span><span class="sxs-lookup"><span data-stu-id="462ec-223">Remove a service</span></span>

<span data-ttu-id="462ec-224">Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki PowerShell 6 komutuyla bir hizmeti kaldırın:</span><span class="sxs-lookup"><span data-stu-id="462ec-224">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="462ec-225">Olayları başlatma ve durdurma olaylarını işleme</span><span class="sxs-lookup"><span data-stu-id="462ec-225">Handle starting and stopping events</span></span>

<span data-ttu-id="462ec-226"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>ve <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> olaylarını işlemek için:</span><span class="sxs-lookup"><span data-stu-id="462ec-226">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="462ec-227">`OnStarting`, `OnStarted`ve `OnStopping` yöntemlerle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> türetilen bir sınıf oluşturun:</span><span class="sxs-lookup"><span data-stu-id="462ec-227">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="462ec-228"><xref:System.ServiceProcess.ServiceBase.Run*>`CustomWebHostService` ileten <xref:Microsoft.AspNetCore.Hosting.IWebHost> için bir genişletme yöntemi oluşturun:</span><span class="sxs-lookup"><span data-stu-id="462ec-228">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="462ec-229">`Program.Main`' de, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>yerine `RunAsCustomService` uzantısı metodunu çağırın:</span><span class="sxs-lookup"><span data-stu-id="462ec-229">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="462ec-230">`Program.Main`<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> konumunu görmek için, [dağıtım türü](#deployment-type) bölümünde gösterilen kod örneğine bakın.</span><span class="sxs-lookup"><span data-stu-id="462ec-230">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="462ec-231">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="462ec-231">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="462ec-232">Internet 'ten veya şirket ağından gelen isteklerle etkileşime geçen ve bir ara sunucu veya yük dengeleyicinin arkasındaki Hizmetler ek yapılandırma gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="462ec-232">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="462ec-233">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="462ec-233">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="462ec-234">Uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="462ec-234">Configure endpoints</span></span>

<span data-ttu-id="462ec-235">Varsayılan olarak, ASP.NET Core `http://localhost:5000` ' a bağlanır.</span><span class="sxs-lookup"><span data-stu-id="462ec-235">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="462ec-236">`ASPNETCORE_URLS` ortam değişkenini ayarlayarak URL 'YI ve bağlantı noktasını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="462ec-236">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="462ec-237">Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="462ec-237">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="462ec-238">Yukarıdaki kılavuz, HTTPS uç noktaları için desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="462ec-238">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="462ec-239">Örneğin, bir Windows hizmeti ile kimlik doğrulaması kullanıldığında HTTPS için uygulamayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="462ec-239">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="462ec-240">Hizmet uç noktasının güvenliğini sağlamak için ASP.NET Core HTTPS geliştirme sertifikası kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="462ec-240">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="462ec-241">Geçerli dizin ve içerik kökü</span><span class="sxs-lookup"><span data-stu-id="462ec-241">Current directory and content root</span></span>

<span data-ttu-id="462ec-242">Bir Windows hizmeti için <xref:System.IO.Directory.GetCurrentDirectory*> çağırarak döndürülen geçerli çalışma dizini *C:\\windows\\system32* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="462ec-242">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="462ec-243">*System32* klasörü, bir hizmetin dosyalarını (örneğin, ayarlar dosyaları) depolamak için uygun bir konum değildir.</span><span class="sxs-lookup"><span data-stu-id="462ec-243">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="462ec-244">Bir hizmetin varlık ve ayar dosyalarını sürdürmek ve erişmek için aşağıdaki yaklaşımlardan birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="462ec-244">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="462ec-245">ContentRootPath veya ContentRootFileProvider kullanın</span><span class="sxs-lookup"><span data-stu-id="462ec-245">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="462ec-246">Bir uygulamanın kaynaklarını bulmak için [ıhostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) veya <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> kullanın.</span><span class="sxs-lookup"><span data-stu-id="462ec-246">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="462ec-247">Uygulamanın klasörü için içerik kök yolunu ayarla</span><span class="sxs-lookup"><span data-stu-id="462ec-247">Set the content root path to the app's folder</span></span>

<span data-ttu-id="462ec-248"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>, bir hizmet oluşturulduğunda `binPath` bağımsız değişkenine sunulan yoldur.</span><span class="sxs-lookup"><span data-stu-id="462ec-248">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="462ec-249">Ayar dosyalarına yollar oluşturmak için `GetCurrentDirectory` çağırmak yerine, uygulamanın [içerik kökünün](xref:fundamentals/index#content-root)yolunu kullanarak <xref:System.IO.Directory.SetCurrentDirectory*> çağırın.</span><span class="sxs-lookup"><span data-stu-id="462ec-249">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="462ec-250">`Program.Main`, hizmetin yürütülebilir dosyasının yolunu belirleyin ve uygulamanın içerik kökünü oluşturmak için yolu kullanın:</span><span class="sxs-lookup"><span data-stu-id="462ec-250">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="462ec-251">Hizmetin dosyalarını diskte uygun bir konumda depolayın</span><span class="sxs-lookup"><span data-stu-id="462ec-251">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="462ec-252">Dosyaları içeren klasöre <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> kullanırken <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> ile mutlak bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="462ec-252">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="462ec-253">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="462ec-253">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="462ec-254">[Kestrel uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (https yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="462ec-254">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="462ec-255">[Kestrel uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (https yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="462ec-255">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
