---
title: .NET Core ve ASP.NET Core oturum açma
author: rick-anderson
description: Microsoft. Extensions. Logging NuGet paketi tarafından sunulan günlüğe kaydetme çerçevesini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/19/2019
uid: fundamentals/logging/index
ms.openlocfilehash: b23e64077290f0f613e904651e4bb640fcbba95d
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289085"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="f3094-103">.NET Core ve ASP.NET Core oturum açma</span><span class="sxs-lookup"><span data-stu-id="f3094-103">Logging in .NET Core and ASP.NET Core</span></span>

<span data-ttu-id="f3094-104">[Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="f3094-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f3094-105">.NET Core, çeşitli yerleşik ve üçüncü taraf oturum açma sağlayıcılarıyla birlikte çalışarak bir günlüğe kaydetme API 'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="f3094-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="f3094-106">Bu makalede, yerleşik sağlayıcılarla günlüğe kaydetme API 'sinin nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f3094-106">This article shows how to use the logging API with built-in providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3094-107">Bu makalede gösterilen kod örneklerinin çoğu ASP.NET Core uygulamalardan oluşur.</span><span class="sxs-lookup"><span data-stu-id="f3094-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="f3094-108">Bu kod parçacıklarının günlüğe kaydetmeye özgü bölümleri, [genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullanan tüm .NET Core uygulamaları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f3094-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="f3094-109">Genel konağın Web 'de olmayan uygulamalarda kullanımı hakkında bilgi için bkz. [barındırılan hizmetler](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="f3094-109">For information about how to use the Generic Host in non-web console apps, see [Hosted services](xref:fundamentals/host/hosted-services).</span></span>

<span data-ttu-id="f3094-110">Genel ana bilgisayarı olmayan uygulamalar için günlük kodu, [sağlayıcıların Eklenme](#add-providers) ve [günlükçülerin oluşturulma](#create-logs)biçiminde farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="f3094-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="f3094-111">Ana bilgisayar olmayan kod örnekleri, makalenin bu bölümlerinde gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="f3094-111">Non-host code examples are shown in those sections of the article.</span></span>

::: moniker-end

<span data-ttu-id="f3094-112">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f3094-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="f3094-113">Sağlayıcı Ekle</span><span class="sxs-lookup"><span data-stu-id="f3094-113">Add providers</span></span>

<span data-ttu-id="f3094-114">Günlük sağlayıcısı günlükleri görüntüler veya depolar.</span><span class="sxs-lookup"><span data-stu-id="f3094-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="f3094-115">Örneğin, konsol sağlayıcısı günlükleri konsolunda görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Application Insights depolar.</span><span class="sxs-lookup"><span data-stu-id="f3094-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="f3094-116">Birden çok sağlayıcı eklenerek Günlükler birden çok hedefe gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3094-117">Genel ana bilgisayar kullanan bir uygulamaya sağlayıcı eklemek için, *program.cs*'de sağlayıcının `Add{provider name}` uzantısı metodunu çağırın:</span><span class="sxs-lookup"><span data-stu-id="f3094-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="f3094-118">Konak olmayan bir konsol uygulamasında, bir `LoggerFactory`oluştururken sağlayıcının `Add{provider name}` uzantısı metodunu çağırın:</span><span class="sxs-lookup"><span data-stu-id="f3094-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="f3094-119">`LoggerFactory` ve `AddConsole`, `Microsoft.Extensions.Logging`için `using` bir ifade gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f3094-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="f3094-120">Varsayılan ASP.NET Core proje şablonları, aşağıdaki günlük sağlayıcılarını ekleyen <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>çağırır:</span><span class="sxs-lookup"><span data-stu-id="f3094-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="f3094-121">Konsol</span><span class="sxs-lookup"><span data-stu-id="f3094-121">Console</span></span>
* <span data-ttu-id="f3094-122">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="f3094-122">Debug</span></span>
* <span data-ttu-id="f3094-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="f3094-123">EventSource</span></span>
* <span data-ttu-id="f3094-124">Olay günlüğü (yalnızca Windows üzerinde çalışırken)</span><span class="sxs-lookup"><span data-stu-id="f3094-124">EventLog (only when running on Windows)</span></span>

<span data-ttu-id="f3094-125">Varsayılan sağlayıcıları kendi seçimlerinizle değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f3094-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="f3094-126"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>çağırın ve istediğiniz sağlayıcıları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f3094-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

<span data-ttu-id="f3094-127">Bir sağlayıcı eklemek için sağlayıcının `Add{provider name}` uzantısı yöntemini *program.cs*içinde çağırın:</span><span class="sxs-lookup"><span data-stu-id="f3094-127">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="f3094-128">Yukarıdaki kod `Microsoft.Extensions.Logging` ve `Microsoft.Extensions.Configuration`başvuruları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f3094-128">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="f3094-129">Varsayılan proje şablonu, aşağıdaki günlük sağlayıcılarını ekleyen <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>çağırır:</span><span class="sxs-lookup"><span data-stu-id="f3094-129">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="f3094-130">Konsol</span><span class="sxs-lookup"><span data-stu-id="f3094-130">Console</span></span>
* <span data-ttu-id="f3094-131">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="f3094-131">Debug</span></span>
* <span data-ttu-id="f3094-132">EventSource (ASP.NET Core 2,2 ' den başlayarak)</span><span class="sxs-lookup"><span data-stu-id="f3094-132">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="f3094-133">`CreateDefaultBuilder`kullanıyorsanız, varsayılan sağlayıcıları kendi seçimlerinizle değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f3094-133">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="f3094-134"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>çağırın ve istediğiniz sağlayıcıları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f3094-134">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

<span data-ttu-id="f3094-135">Makalenin ilerleyen kısımlarında [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve [üçüncü taraf günlüğü sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="f3094-135">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="f3094-136">Günlükleri oluştur</span><span class="sxs-lookup"><span data-stu-id="f3094-136">Create logs</span></span>

<span data-ttu-id="f3094-137">Günlükler oluşturmak için bir <xref:Microsoft.Extensions.Logging.ILogger%601> nesnesi kullanın.</span><span class="sxs-lookup"><span data-stu-id="f3094-137">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="f3094-138">Bir Web uygulamasında veya barındırılan hizmette, bağımlılık ekleme (DI) `ILogger` alın.</span><span class="sxs-lookup"><span data-stu-id="f3094-138">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="f3094-139">Konak dışı konsol uygulamalarında, bir `ILogger`oluşturmak için `LoggerFactory` kullanın.</span><span class="sxs-lookup"><span data-stu-id="f3094-139">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="f3094-140">Aşağıdaki ASP.NET Core örnek kategori olarak `TodoApiSample.Pages.AboutModel` içeren bir günlükçü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f3094-140">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="f3094-141">Günlük *kategorisi* , her günlük ile ilişkili bir dizedir.</span><span class="sxs-lookup"><span data-stu-id="f3094-141">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="f3094-142">Dı tarafından belirtilen `ILogger<T>` örneği, kategori olarak `T` tam adı olan Günlükler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f3094-142">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="f3094-143">Aşağıdaki konak olmayan konsol uygulaması örneği, kategori olarak `LoggingConsoleApp.Program` olan bir günlükçü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f3094-143">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

<span data-ttu-id="f3094-144">Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, günlükçü, düzeyi `Information` olan günlükleri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f3094-144">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="f3094-145">Günlük *düzeyi* günlüğe kaydedilen etkinliğin önem derecesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="f3094-145">The Log *level* indicates the severity of the logged event.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

<span data-ttu-id="f3094-146">[Düzeyler](#log-level) ve [Kategoriler](#log-category) Bu makalenin ilerleyen kısımlarında daha ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="f3094-146">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="f3094-147">Program sınıfında Günlükler oluşturma</span><span class="sxs-lookup"><span data-stu-id="f3094-147">Create logs in the Program class</span></span>

<span data-ttu-id="f3094-148">ASP.NET Core uygulamasının `Program` sınıfında Günlükler yazmak için, konak oluşturulduktan sonra bir `ILogger` örneği alın:</span><span class="sxs-lookup"><span data-stu-id="f3094-148">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="f3094-149">Ana bilgisayar oluşturma sırasında günlüğe kaydetme doğrudan desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="f3094-149">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="f3094-150">Ancak, ayrı bir günlükçü kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-150">However, a separate logger can be used.</span></span> <span data-ttu-id="f3094-151">Aşağıdaki örnekte, `CreateHostBuilder`oturum açmak için bir [Serilog](https://serilog.net/) günlükçü kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f3094-151">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="f3094-152">`AddSerilog`, `Log.Logger`belirtilen statik yapılandırmayı kullanır:</span><span class="sxs-lookup"><span data-stu-id="f3094-152">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="f3094-153">Başlangıç sınıfında Günlükler oluşturma</span><span class="sxs-lookup"><span data-stu-id="f3094-153">Create logs in the Startup class</span></span>

<span data-ttu-id="f3094-154">Bir ASP.NET Core uygulamasının `Startup.Configure` yönteminde Günlükler yazmak için, yöntem imzasına bir `ILogger` parametresi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f3094-154">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="f3094-155">`Startup.ConfigureServices` yönteminde DI kapsayıcısı kurulumu tamamlanmadan önce Günlüklerin yazılması desteklenmez:</span><span class="sxs-lookup"><span data-stu-id="f3094-155">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="f3094-156">`Startup` oluşturucusuna günlükçü ekleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="f3094-156">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="f3094-157">`Startup.ConfigureServices` yöntemi imzasına günlükçü ekleme desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="f3094-157">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="f3094-158">Bu kısıtlamanın nedeni, günlük kaydının dı ve yapılandırmaya göre değişir ve bu da, ara ' ya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f3094-158">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="f3094-159">DI kapsayıcısı `ConfigureServices` bitene kadar ayarlanamaz.</span><span class="sxs-lookup"><span data-stu-id="f3094-159">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="f3094-160">Web ana bilgisayarı için ayrı bir dı kapsayıcısı oluşturulduğundan, bir günlükçü `Startup` ' ye Oluşturucu Ekleme ASP.NET Core önceki sürümlerinde çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f3094-160">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="f3094-161">Genel ana bilgisayar için yalnızca bir kapsayıcı oluşturma hakkında daha fazla bilgi için bkz. [Son değişiklik duyurusu](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="f3094-161">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="f3094-162">`ILogger<T>`bağımlı bir hizmet yapılandırmanız gerekiyorsa, bunu Oluşturucu ekleme veya bir fabrika yöntemi sağlayarak de yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f3094-162">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="f3094-163">Fabrika yöntemi yaklaşımı yalnızca başka bir seçenek yoksa önerilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-163">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="f3094-164">Örneğin, bir özelliği kaynağından bir hizmete doldurmanız gerektiğini varsayalım:</span><span class="sxs-lookup"><span data-stu-id="f3094-164">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="f3094-165">Önceki vurgulanan kod, DI kapsayıcısının bir `MyService`örneği oluşturmak için ilk kez çalışan bir `Func`.</span><span class="sxs-lookup"><span data-stu-id="f3094-165">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="f3094-166">Kayıtlı hizmetlerden herhangi birine bu şekilde erişebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f3094-166">You can access any of the registered services in this way.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="f3094-167">Başlangıçta günlük oluşturma</span><span class="sxs-lookup"><span data-stu-id="f3094-167">Create logs in Startup</span></span>

<span data-ttu-id="f3094-168">`Startup` sınıfında Günlükler yazmak için, Oluşturucu imzasına bir `ILogger` parametresi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f3094-168">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="f3094-169">Program sınıfında Günlükler oluşturma</span><span class="sxs-lookup"><span data-stu-id="f3094-169">Create logs in the Program class</span></span>

<span data-ttu-id="f3094-170">`Program` sınıfında Günlükler yazmak için, DI 'den bir `ILogger` örneği alın:</span><span class="sxs-lookup"><span data-stu-id="f3094-170">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="f3094-171">Ana bilgisayar oluşturma sırasında günlüğe kaydetme doğrudan desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="f3094-171">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="f3094-172">Ancak, ayrı bir günlükçü kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-172">However, a separate logger can be used.</span></span> <span data-ttu-id="f3094-173">Aşağıdaki örnekte, `CreateWebHostBuilder`oturum açmak için bir [Serilog](https://serilog.net/) günlükçü kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f3094-173">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="f3094-174">`AddSerilog`, `Log.Logger`belirtilen statik yapılandırmayı kullanır:</span><span class="sxs-lookup"><span data-stu-id="f3094-174">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="f3094-175">Zaman uyumsuz günlükçü yöntemi yok</span><span class="sxs-lookup"><span data-stu-id="f3094-175">No asynchronous logger methods</span></span>

<span data-ttu-id="f3094-176">Günlüğe kaydetme, zaman uyumsuz kodun performans maliyetine değer olmaması kadar hızlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f3094-176">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="f3094-177">Günlüğe kaydetme veri depoluizin yavaşsa, doğrudan buna yazmayın.</span><span class="sxs-lookup"><span data-stu-id="f3094-177">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="f3094-178">Başlangıç olarak günlük iletilerini hızlı bir mağazaya yazmayı ve sonra yavaş depoya daha sonra taşımayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="f3094-178">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="f3094-179">Örneğin, SQL Server için günlük kaydı yapıyorsanız, `Log` Yöntemler zaman uyumlu olduğundan bunu doğrudan bir `Log` yönteminde yapmak istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="f3094-179">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="f3094-180">Bunun yerine, günlük iletilerini bir bellek içi kuyruğa eşzamanlı olarak ekleyin ve bir arka plan çalışanı, SQL Server veri gönderme zaman uyumsuz çalışmasını sağlamak için iletileri kuyruktan çekin.</span><span class="sxs-lookup"><span data-stu-id="f3094-180">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span>

## <a name="configuration"></a><span data-ttu-id="f3094-181">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f3094-181">Configuration</span></span>

<span data-ttu-id="f3094-182">Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="f3094-182">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="f3094-183">Dosya biçimleri (ıNı, JSON ve XML).</span><span class="sxs-lookup"><span data-stu-id="f3094-183">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="f3094-184">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="f3094-184">Command-line arguments.</span></span>
* <span data-ttu-id="f3094-185">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="f3094-185">Environment variables.</span></span>
* <span data-ttu-id="f3094-186">Bellek içi .NET nesneleri.</span><span class="sxs-lookup"><span data-stu-id="f3094-186">In-memory .NET objects.</span></span>
* <span data-ttu-id="f3094-187">Şifrelenmemiş [gizli dizi Yöneticisi](xref:security/app-secrets) depolaması.</span><span class="sxs-lookup"><span data-stu-id="f3094-187">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="f3094-188">[Azure Key Vault](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.</span><span class="sxs-lookup"><span data-stu-id="f3094-188">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="f3094-189">Özel sağlayıcılar (yüklü veya oluşturulmuş).</span><span class="sxs-lookup"><span data-stu-id="f3094-189">Custom providers (installed or created).</span></span>

<span data-ttu-id="f3094-190">Örneğin, günlük yapılandırma genellikle uygulama ayarları dosyalarının `Logging` bölümü tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f3094-190">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="f3094-191">Aşağıdaki örnek tipik bir appSettings 'in içeriğini gösterir *. Development. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="f3094-191">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="f3094-192">`Logging` özelliği `LogLevel` ve günlük sağlayıcısı özelliklerine sahip olabilir (konsol gösterilir).</span><span class="sxs-lookup"><span data-stu-id="f3094-192">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="f3094-193">`Logging` altındaki `LogLevel` özelliği, Seçili kategoriler için günlüğe kaydedilecek minimum [düzeyi](#log-level) belirtir.</span><span class="sxs-lookup"><span data-stu-id="f3094-193">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="f3094-194">Örnekte, `System` ve `Microsoft` kategorileri `Information` düzeyinde günlüğe kaydedilir ve diğerleri `Debug` düzeyinde günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-194">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="f3094-195">`Logging` altındaki diğer özellikler günlük sağlayıcılarını belirtir.</span><span class="sxs-lookup"><span data-stu-id="f3094-195">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="f3094-196">Örnek, konsol sağlayıcısına yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="f3094-196">The example is for the Console provider.</span></span> <span data-ttu-id="f3094-197">Bir sağlayıcı, [günlük kapsamlarını](#log-scopes)destekliyorsa `IncludeScopes` etkinleştirilip etkinleştirilmeyeceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="f3094-197">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="f3094-198">Bir sağlayıcı özelliği (örneğin `Console` gibi), bir `LogLevel` özelliği de belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-198">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="f3094-199">sağlayıcı altında `LogLevel`, bu sağlayıcının günlüğe kaydedilecek düzeyleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="f3094-199">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="f3094-200">`Logging.{providername}.LogLevel`düzeyler belirtilirse, `Logging.LogLevel`ayarlanan her şeyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="f3094-200">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="f3094-201">Günlüğe kaydetme API 'SI, bir uygulama çalışırken günlük düzeylerini değiştirme senaryosu içermez.</span><span class="sxs-lookup"><span data-stu-id="f3094-201">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="f3094-202">Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu, günlüğe kaydetme yapılandırması üzerinde etkili bir şekilde gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="f3094-202">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="f3094-203">Örneğin, ayar dosyalarını okumak için `CreateDefaultBuilder` tarafından eklenen [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider), varsayılan olarak günlük yapılandırmasını yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="f3094-203">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="f3094-204">Uygulama çalışırken kodda yapılandırma değiştirilirse uygulama, uygulamanın günlük yapılandırmasını güncelleştirmek için [IController. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) ' i çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-204">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="f3094-205">Yapılandırma sağlayıcılarını uygulama hakkında daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f3094-205">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="f3094-206">Örnek günlüğe kaydetme çıkışı</span><span class="sxs-lookup"><span data-stu-id="f3094-206">Sample logging output</span></span>

<span data-ttu-id="f3094-207">Yukarıdaki bölümde gösterilen örnek kodla, uygulama komut satırından çalıştırıldığında Günlükler konsolunda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f3094-207">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="f3094-208">Konsol çıkışının bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f3094-208">Here's an example of console output:</span></span>

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

::: moniker-end

<span data-ttu-id="f3094-209">Yukarıdaki Günlükler, `http://localhost:5000/api/todo/0`konumundaki örnek uygulamaya HTTP GET isteği yapılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="f3094-209">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="f3094-210">Visual Studio 'da örnek uygulamayı çalıştırdığınızda hata ayıklama penceresinde göründükleri günlüklere yönelik bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f3094-210">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

::: moniker range=">= aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="f3094-211">Yukarıdaki bölümde gösterilen `ILogger` çağrıları tarafından oluşturulan Günlükler "TodoApiSample" ile başlar.</span><span class="sxs-lookup"><span data-stu-id="f3094-211">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="f3094-212">"Microsoft" kategorileri ile başlayan Günlükler ASP.NET Core Framework kodundan alınır.</span><span class="sxs-lookup"><span data-stu-id="f3094-212">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="f3094-213">ASP.NET Core ve uygulama kodu aynı günlük API 'sini ve sağlayıcılarını kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="f3094-213">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="f3094-214">Yukarıdaki bölümde gösterilen `ILogger` çağrıları tarafından oluşturulan Günlükler "TodoApi" ile başlar.</span><span class="sxs-lookup"><span data-stu-id="f3094-214">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="f3094-215">"Microsoft" kategorileri ile başlayan Günlükler ASP.NET Core Framework kodundan alınır.</span><span class="sxs-lookup"><span data-stu-id="f3094-215">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="f3094-216">ASP.NET Core ve uygulama kodu aynı günlük API 'sini ve sağlayıcılarını kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="f3094-216">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

<span data-ttu-id="f3094-217">Bu makalenin geri kalanında günlüğe kaydetme için bazı ayrıntılar ve seçenekler açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f3094-217">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="f3094-218">NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="f3094-218">NuGet packages</span></span>

<span data-ttu-id="f3094-219">`ILogger` ve `ILoggerFactory` arabirimleri [Microsoft. Extensions. Logging. soyutlamalar](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)ve bunların varsayılan uygulamaları [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)' dir.</span><span class="sxs-lookup"><span data-stu-id="f3094-219">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="f3094-220">Günlük kategorisi</span><span class="sxs-lookup"><span data-stu-id="f3094-220">Log category</span></span>

<span data-ttu-id="f3094-221">`ILogger` bir nesne oluşturulduğunda, için bir *Kategori* belirtilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-221">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="f3094-222">Bu kategori, bu `ILogger`örneği tarafından oluşturulan her günlük iletisine dahildir.</span><span class="sxs-lookup"><span data-stu-id="f3094-222">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="f3094-223">Kategori herhangi bir dize olabilir, ancak kural, "TodoApi. Controllers. TodoController" gibi sınıf adını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="f3094-223">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="f3094-224">Kategori olarak `T` tam nitelikli tür adını kullanan bir `ILogger` örneğini almak için `ILogger<T>` kullanın:</span><span class="sxs-lookup"><span data-stu-id="f3094-224">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="f3094-225">Kategoriyi açıkça belirtmek için `ILoggerFactory.CreateLogger`çağırın:</span><span class="sxs-lookup"><span data-stu-id="f3094-225">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="f3094-226">`ILogger<T>`, `T`tam nitelikli tür adıyla `CreateLogger` çağırma ile eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="f3094-226">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="f3094-227">Günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="f3094-227">Log level</span></span>

<span data-ttu-id="f3094-228">Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="f3094-228">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="f3094-229">Günlük düzeyi önem derecesini veya önemini gösterir.</span><span class="sxs-lookup"><span data-stu-id="f3094-229">The log level indicates the severity or importance.</span></span> <span data-ttu-id="f3094-230">Örneğin, bir yöntem normal olarak sona erdiğinde bir `Information` günlüğü ve bir yöntem *404* bulunmayan bir durum kodu döndürdüğünde bir `Warning` günlüğü yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f3094-230">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="f3094-231">Aşağıdaki kod `Information` ve `Warning` günlüklerini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f3094-231">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="f3094-232">Önceki kodda, ilk parametredir [oturum öğesini belirten Olay No.](#log-event-id)</span><span class="sxs-lookup"><span data-stu-id="f3094-232">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="f3094-233">İkinci parametre, kalan Yöntem parametreleri tarafından belirtilen bağımsız değişken değerleri için yer tutucuları olan bir ileti şablonudur.</span><span class="sxs-lookup"><span data-stu-id="f3094-233">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="f3094-234">Yöntem parametreleri bu makalenin ilerleyen kısımlarında bulunan [ileti şablonu bölümünde](#log-message-template) açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f3094-234">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="f3094-235">Yöntem adındaki düzeyi (örneğin, `LogInformation` ve `LogWarning`) içeren günlük yöntemleri, [ILogger için uzantı yöntemleridir](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="f3094-235">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="f3094-236">Bu yöntemler bir `LogLevel` parametresi alan `Log` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="f3094-236">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="f3094-237">Bu uzantı yöntemlerinden biri yerine doğrudan `Log` yöntemi çağırabilirsiniz, ancak söz dizimi görece karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="f3094-237">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="f3094-238">Daha fazla bilgi için bkz. <xref:Microsoft.Extensions.Logging.ILogger> ve [günlükçü uzantıları kaynak kodu](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="f3094-238">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="f3094-239">ASP.NET Core, en küçükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="f3094-239">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="f3094-240">İzleme = 0</span><span class="sxs-lookup"><span data-stu-id="f3094-240">Trace = 0</span></span>

  <span data-ttu-id="f3094-241">Genellikle yalnızca hata ayıklama için değerli bilgiler için.</span><span class="sxs-lookup"><span data-stu-id="f3094-241">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="f3094-242">Bu iletiler hassas uygulama verileri içerebilir, bu nedenle bir üretim ortamında etkinleştirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="f3094-242">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="f3094-243">*Varsayılan olarak devre dışıdır.*</span><span class="sxs-lookup"><span data-stu-id="f3094-243">*Disabled by default.*</span></span>

* <span data-ttu-id="f3094-244">Hata Ayıkla = 1</span><span class="sxs-lookup"><span data-stu-id="f3094-244">Debug = 1</span></span>

  <span data-ttu-id="f3094-245">Geliştirme ve hata ayıklama konusunda yararlı olabilecek bilgiler için.</span><span class="sxs-lookup"><span data-stu-id="f3094-245">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="f3094-246">Örnek: `Entering method Configure with flag set to true.` en yüksek günlük hacimden dolayı yalnızca sorun giderirken `Debug` düzeyi günlüklerini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f3094-246">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="f3094-247">Bilgi = 2</span><span class="sxs-lookup"><span data-stu-id="f3094-247">Information = 2</span></span>

  <span data-ttu-id="f3094-248">Uygulamanın genel akışını izlemek için.</span><span class="sxs-lookup"><span data-stu-id="f3094-248">For tracking the general flow of the app.</span></span> <span data-ttu-id="f3094-249">Bu günlüklerde genellikle uzun süreli bir değer vardır.</span><span class="sxs-lookup"><span data-stu-id="f3094-249">These logs typically have some long-term value.</span></span> <span data-ttu-id="f3094-250">Örnek: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="f3094-250">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="f3094-251">Uyarı = 3</span><span class="sxs-lookup"><span data-stu-id="f3094-251">Warning = 3</span></span>

  <span data-ttu-id="f3094-252">Uygulama akışında anormal veya beklenmedik olaylar için.</span><span class="sxs-lookup"><span data-stu-id="f3094-252">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="f3094-253">Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gerekebilecek hataları veya diğer koşulları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-253">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="f3094-254">İşlenmiş özel durumlar `Warning` günlük düzeyini kullanmak için yaygın bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="f3094-254">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="f3094-255">Örnek: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="f3094-255">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="f3094-256">Hata = 4</span><span class="sxs-lookup"><span data-stu-id="f3094-256">Error = 4</span></span>

  <span data-ttu-id="f3094-257">İşlenemeyen hatalar ve özel durumlar için.</span><span class="sxs-lookup"><span data-stu-id="f3094-257">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="f3094-258">Bu iletiler, uygulama genelinde bir hata değil geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="f3094-258">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="f3094-259">Örnek günlük iletisi: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="f3094-259">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="f3094-260">Kritik = 5</span><span class="sxs-lookup"><span data-stu-id="f3094-260">Critical = 5</span></span>

  <span data-ttu-id="f3094-261">Anında ilgilenilmesi gereken hatalarda.</span><span class="sxs-lookup"><span data-stu-id="f3094-261">For failures that require immediate attention.</span></span> <span data-ttu-id="f3094-262">Örnekler: veri kaybı senaryoları, disk alanı yetersiz.</span><span class="sxs-lookup"><span data-stu-id="f3094-262">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="f3094-263">Belirli bir depolama ortamında veya görüntüleme penceresinde ne kadar günlük çıkışının yazıldığını denetlemek için günlük düzeyini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f3094-263">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="f3094-264">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f3094-264">For example:</span></span>

* <span data-ttu-id="f3094-265">Üretimde:</span><span class="sxs-lookup"><span data-stu-id="f3094-265">In production:</span></span>
  * <span data-ttu-id="f3094-266">`Trace` `Information` düzeylerinde günlüğe kaydetme, yüksek hacimli ayrıntılı günlük iletileri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f3094-266">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="f3094-267">Maliyetleri denetlemek ve veri depolama sınırlarını aşmamak için, `Information` düzey iletileri kullanarak `Trace` yüksek hacimli, düşük maliyetli bir veri deposuna günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f3094-267">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="f3094-268">`Warning` `Critical` düzeyler aracılığıyla günlüğe kaydetme işlemi genellikle daha az, daha küçük günlük iletileri üretir.</span><span class="sxs-lookup"><span data-stu-id="f3094-268">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="f3094-269">Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir ve bu da veri deposu seçiminden daha fazla esneklik elde etmez.</span><span class="sxs-lookup"><span data-stu-id="f3094-269">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="f3094-270">Geliştirme sırasında:</span><span class="sxs-lookup"><span data-stu-id="f3094-270">During development:</span></span>
  * <span data-ttu-id="f3094-271">Konsola `Critical` iletileri aracılığıyla `Warning`.</span><span class="sxs-lookup"><span data-stu-id="f3094-271">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="f3094-272">Sorun giderirken `Information` iletileri aracılığıyla `Trace` ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f3094-272">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="f3094-273">Bu makalede daha sonra bulunan [günlük filtreleme](#log-filtering) bölümünde, bir sağlayıcının hangi günlük düzeylerinin işlediğini nasıl denetleneceği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f3094-273">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="f3094-274">ASP.NET Core çerçeve olayları için günlükleri yazar.</span><span class="sxs-lookup"><span data-stu-id="f3094-274">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="f3094-275">Bu makalede daha önce gelen günlük örnekleri `Information` düzeyin altında tutulur, dolayısıyla hiçbir `Debug` veya `Trace` düzeyi günlüğü oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="f3094-275">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="f3094-276">Aşağıda, `Debug` günlüklerini göstermek için yapılandırılmış örnek uygulama çalıştırılarak oluşturulan konsol günlüklerinin bir örneği verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f3094-276">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

::: moniker-end

## <a name="log-event-id"></a><span data-ttu-id="f3094-277">Günlüğe olay KIMLIĞI</span><span class="sxs-lookup"><span data-stu-id="f3094-277">Log event ID</span></span>

<span data-ttu-id="f3094-278">Her günlük belirtebilirsiniz bir *öğesini belirten Olay No*.</span><span class="sxs-lookup"><span data-stu-id="f3094-278">Each log can specify an *event ID*.</span></span> <span data-ttu-id="f3094-279">Örnek uygulama bunu yerel olarak tanımlanmış bir `LoggingEvents` sınıfını kullanarak yapar:</span><span class="sxs-lookup"><span data-stu-id="f3094-279">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="f3094-280">Olay KIMLIĞI bir olay kümesini ilişkilendirir.</span><span class="sxs-lookup"><span data-stu-id="f3094-280">An event ID associates a set of events.</span></span> <span data-ttu-id="f3094-281">Örneğin, bir sayfadaki öğelerin listesini görüntülemek için ilgili tüm Günlükler 1001 olabilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-281">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="f3094-282">Günlüğe kaydetme sağlayıcısı, olay KIMLIĞINI günlüğe kaydetme iletisindeki kimlik alanında veya hiç değil, bir kimlik alanında saklayabilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-282">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="f3094-283">Hata ayıklama sağlayıcısı olay kimliklerini göstermiyor.</span><span class="sxs-lookup"><span data-stu-id="f3094-283">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="f3094-284">Konsol sağlayıcısı, etkinlik kimliklerini kategoriden sonra parantez içinde gösterir:</span><span class="sxs-lookup"><span data-stu-id="f3094-284">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="f3094-285">Günlük iletisi şablonu</span><span class="sxs-lookup"><span data-stu-id="f3094-285">Log message template</span></span>

<span data-ttu-id="f3094-286">Her günlük bir ileti şablonunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="f3094-286">Each log specifies a message template.</span></span> <span data-ttu-id="f3094-287">İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucuları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-287">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="f3094-288">Sayılar değil, yer tutucular için adları kullanın.</span><span class="sxs-lookup"><span data-stu-id="f3094-288">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="f3094-289">Adlarının, değerlerinin sağlanması için hangi parametrelerin kullanılacağını belirleyen yer tutucular sırası.</span><span class="sxs-lookup"><span data-stu-id="f3094-289">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="f3094-290">Aşağıdaki kodda, parametre adlarının ileti şablonunda sıra dışı olduğuna dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="f3094-290">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="f3094-291">Bu kod, sırasıyla parametre değerleriyle bir günlük iletisi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f3094-291">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="f3094-292">Günlüğe kaydetme altyapısı bu şekilde çalışarak, günlük sağlayıcılarının [yapılandırılmış günlüğe yazma olarak da bilinen anlamsal günlüğü](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uygulayabilmesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-292">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="f3094-293">Bağımsız değişkenler yalnızca biçimli ileti şablonuna değil, günlük sistemine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-293">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="f3094-294">Bu bilgiler, günlük sağlayıcılarının parametre değerlerini alan olarak depolamasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="f3094-294">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="f3094-295">Örneğin, günlükçü yönteminin şuna benzer şekilde göründüğünü varsayın:</span><span class="sxs-lookup"><span data-stu-id="f3094-295">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="f3094-296">Günlükleri Azure Tablo depolama alanına gönderiyorsanız, her bir Azure Tablo varlığı `ID` ve `RequestTime` özelliklerine sahip olabilir. Bu, günlük verilerinde sorguları basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="f3094-296">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="f3094-297">Bir sorgu, belirli bir `RequestTime` aralığındaki tüm günlükleri, metin iletisinden zaman aşımına uğratmadan bulabilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-297">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="f3094-298">Günlüğe kaydetme özel durumları</span><span class="sxs-lookup"><span data-stu-id="f3094-298">Logging exceptions</span></span>

<span data-ttu-id="f3094-299">Günlükçü yöntemlerinin, aşağıdaki örnekte olduğu gibi bir özel durum iletmenizi sağlayan aşırı yüklemeleri vardır:</span><span class="sxs-lookup"><span data-stu-id="f3094-299">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="f3094-300">Özel durum bilgileri farklı yollarla farklı sağlayıcılarda işler.</span><span class="sxs-lookup"><span data-stu-id="f3094-300">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="f3094-301">Yukarıda gösterilen koddan hata ayıklama sağlayıcısı çıktısına bir örnek aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="f3094-301">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="f3094-302">Günlük filtreleme</span><span class="sxs-lookup"><span data-stu-id="f3094-302">Log filtering</span></span>

<span data-ttu-id="f3094-303">Belirli bir sağlayıcı ve kategori için en az bir günlük düzeyi veya tüm sağlayıcılar ya da tüm kategoriler için belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f3094-303">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="f3094-304">Minimum düzeyin altındaki tüm Günlükler bu sağlayıcıya aktarılmaz, bu nedenle görüntülenmez veya depolanmaz.</span><span class="sxs-lookup"><span data-stu-id="f3094-304">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="f3094-305">Tüm günlükleri gizlemek için en düşük günlük düzeyi olarak `LogLevel.None` belirtin.</span><span class="sxs-lookup"><span data-stu-id="f3094-305">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="f3094-306">`LogLevel.None` tamsayı değeri 6 ' dır ve `LogLevel.Critical` (5) daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="f3094-306">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="f3094-307">Yapılandırmada filtre kuralları oluşturma</span><span class="sxs-lookup"><span data-stu-id="f3094-307">Create filter rules in configuration</span></span>

<span data-ttu-id="f3094-308">Proje şablonu kodu, konsol, hata ayıklama ve EventSource (ASP.NET Core 2,2 veya üzeri) sağlayıcılar için günlük kaydı ayarlamak üzere `CreateDefaultBuilder` çağırır.</span><span class="sxs-lookup"><span data-stu-id="f3094-308">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="f3094-309">`CreateDefaultBuilder` yöntemi, [Bu makalenin önceki kısımlarında](#configuration)açıklandığı gibi, `Logging` bir bölümünde yapılandırma aramak için günlüğe kaydetmeyi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f3094-309">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="f3094-310">Yapılandırma verileri aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en düşük günlük düzeylerini belirtir:</span><span class="sxs-lookup"><span data-stu-id="f3094-310">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

<span data-ttu-id="f3094-311">Bu JSON altı filtre kuralı oluşturur: biri hata ayıklama sağlayıcısı, konsol sağlayıcısı için dört ve diğeri tüm sağlayıcılar için.</span><span class="sxs-lookup"><span data-stu-id="f3094-311">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="f3094-312">`ILogger` bir nesne oluşturulduğunda her sağlayıcı için tek bir kural seçilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-312">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="f3094-313">Koddaki filtre kuralları</span><span class="sxs-lookup"><span data-stu-id="f3094-313">Filter rules in code</span></span>

<span data-ttu-id="f3094-314">Aşağıdaki örnek, koddaki filtre kurallarının nasıl kaydedileceği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f3094-314">The following example shows how to register filter rules in code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

<span data-ttu-id="f3094-315">İkinci `AddFilter`, tür adını kullanarak hata ayıklama sağlayıcısını belirtir.</span><span class="sxs-lookup"><span data-stu-id="f3094-315">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="f3094-316">İlk `AddFilter` bir sağlayıcı türü belirtmediğinden, tüm sağlayıcılar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f3094-316">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="f3094-317">Filtreleme kuralları nasıl uygulanır</span><span class="sxs-lookup"><span data-stu-id="f3094-317">How filtering rules are applied</span></span>

<span data-ttu-id="f3094-318">Yapılandırma verileri ve önceki örneklerde gösterilen `AddFilter` kodu, aşağıdaki tabloda gösterilen kuralları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f3094-318">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="f3094-319">İlk altı yapılandırma örneğinde ve son iki ise kod örneğinde gelir.</span><span class="sxs-lookup"><span data-stu-id="f3094-319">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="f3094-320">Sayı</span><span class="sxs-lookup"><span data-stu-id="f3094-320">Number</span></span> | <span data-ttu-id="f3094-321">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="f3094-321">Provider</span></span>      | <span data-ttu-id="f3094-322">Şununla başlayan Kategoriler...</span><span class="sxs-lookup"><span data-stu-id="f3094-322">Categories that begin with ...</span></span>          | <span data-ttu-id="f3094-323">En düşük günlük düzeyi</span><span class="sxs-lookup"><span data-stu-id="f3094-323">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="f3094-324">1\.</span><span class="sxs-lookup"><span data-stu-id="f3094-324">1</span></span>      | <span data-ttu-id="f3094-325">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="f3094-325">Debug</span></span>         | <span data-ttu-id="f3094-326">Tüm Kategoriler</span><span class="sxs-lookup"><span data-stu-id="f3094-326">All categories</span></span>                          | <span data-ttu-id="f3094-327">Bilgiler</span><span class="sxs-lookup"><span data-stu-id="f3094-327">Information</span></span>       |
| <span data-ttu-id="f3094-328">2</span><span class="sxs-lookup"><span data-stu-id="f3094-328">2</span></span>      | <span data-ttu-id="f3094-329">Konsol</span><span class="sxs-lookup"><span data-stu-id="f3094-329">Console</span></span>       | <span data-ttu-id="f3094-330">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="f3094-330">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="f3094-331">Uyarı</span><span class="sxs-lookup"><span data-stu-id="f3094-331">Warning</span></span>           |
| <span data-ttu-id="f3094-332">3</span><span class="sxs-lookup"><span data-stu-id="f3094-332">3</span></span>      | <span data-ttu-id="f3094-333">Konsol</span><span class="sxs-lookup"><span data-stu-id="f3094-333">Console</span></span>       | <span data-ttu-id="f3094-334">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="f3094-334">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="f3094-335">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="f3094-335">Debug</span></span>             |
| <span data-ttu-id="f3094-336">4</span><span class="sxs-lookup"><span data-stu-id="f3094-336">4</span></span>      | <span data-ttu-id="f3094-337">Konsol</span><span class="sxs-lookup"><span data-stu-id="f3094-337">Console</span></span>       | <span data-ttu-id="f3094-338">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="f3094-338">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="f3094-339">Hata</span><span class="sxs-lookup"><span data-stu-id="f3094-339">Error</span></span>             |
| <span data-ttu-id="f3094-340">5</span><span class="sxs-lookup"><span data-stu-id="f3094-340">5</span></span>      | <span data-ttu-id="f3094-341">Konsol</span><span class="sxs-lookup"><span data-stu-id="f3094-341">Console</span></span>       | <span data-ttu-id="f3094-342">Tüm Kategoriler</span><span class="sxs-lookup"><span data-stu-id="f3094-342">All categories</span></span>                          | <span data-ttu-id="f3094-343">Bilgiler</span><span class="sxs-lookup"><span data-stu-id="f3094-343">Information</span></span>       |
| <span data-ttu-id="f3094-344">6</span><span class="sxs-lookup"><span data-stu-id="f3094-344">6</span></span>      | <span data-ttu-id="f3094-345">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="f3094-345">All providers</span></span> | <span data-ttu-id="f3094-346">Tüm Kategoriler</span><span class="sxs-lookup"><span data-stu-id="f3094-346">All categories</span></span>                          | <span data-ttu-id="f3094-347">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="f3094-347">Debug</span></span>             |
| <span data-ttu-id="f3094-348">7</span><span class="sxs-lookup"><span data-stu-id="f3094-348">7</span></span>      | <span data-ttu-id="f3094-349">Tüm sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="f3094-349">All providers</span></span> | <span data-ttu-id="f3094-350">Sistem</span><span class="sxs-lookup"><span data-stu-id="f3094-350">System</span></span>                                  | <span data-ttu-id="f3094-351">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="f3094-351">Debug</span></span>             |
| <span data-ttu-id="f3094-352">8</span><span class="sxs-lookup"><span data-stu-id="f3094-352">8</span></span>      | <span data-ttu-id="f3094-353">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="f3094-353">Debug</span></span>         | <span data-ttu-id="f3094-354">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f3094-354">Microsoft</span></span>                               | <span data-ttu-id="f3094-355">izlemesinin</span><span class="sxs-lookup"><span data-stu-id="f3094-355">Trace</span></span>             |

<span data-ttu-id="f3094-356">Bir `ILogger` nesnesi oluşturulduğunda, `ILoggerFactory` nesnesi, bu günlükçü için uygulanacak her sağlayıcı için tek bir kural seçer.</span><span class="sxs-lookup"><span data-stu-id="f3094-356">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="f3094-357">Bir `ILogger` örneği tarafından yazılan tüm iletiler, seçilen kurallara göre filtrelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="f3094-357">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="f3094-358">Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-358">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="f3094-359">Belirli bir kategori için `ILogger` oluşturulduğunda, her sağlayıcı için aşağıdaki algoritma kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f3094-359">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="f3094-360">Sağlayıcı veya diğer adıyla eşleşen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="f3094-360">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="f3094-361">Hiçbir eşleşme bulunmazsa, boş bir sağlayıcıya sahip tüm kurallar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f3094-361">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="f3094-362">Önceki adımın sonucunda, en uzun eşleşen kategori ön ekine sahip kurallar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f3094-362">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="f3094-363">Eşleşme bulunmazsa, kategori belirtmeyen tüm kuralları seçin.</span><span class="sxs-lookup"><span data-stu-id="f3094-363">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="f3094-364">Birden çok kural seçilirse, **son** olanı götürün.</span><span class="sxs-lookup"><span data-stu-id="f3094-364">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="f3094-365">Hiçbir kural seçilmezse `MinimumLevel`kullanın.</span><span class="sxs-lookup"><span data-stu-id="f3094-365">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="f3094-366">Yukarıdaki kurallar listesinde, "Microsoft. AspNetCore. Mvc. Razor. RazorViewEngine" kategorisi için bir `ILogger` nesnesi oluşturduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="f3094-366">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="f3094-367">Hata ayıklama sağlayıcısı, kurallar 1, 6 ve 8 için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f3094-367">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="f3094-368">Kural 8 ' i en özeldir, yani seçili olanı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="f3094-368">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="f3094-369">Konsol sağlayıcısı için, kurallar 3, 4, 5 ve 6 geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f3094-369">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="f3094-370">Kural 3 en özeldir.</span><span class="sxs-lookup"><span data-stu-id="f3094-370">Rule 3 is most specific.</span></span>

<span data-ttu-id="f3094-371">Elde edilen `ILogger` örneği, hata ayıklama sağlayıcısına `Trace` düzeyi ve üzeri Günlükler gönderir.</span><span class="sxs-lookup"><span data-stu-id="f3094-371">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="f3094-372">`Debug` düzeyi ve üzeri Günlükler konsol sağlayıcısına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-372">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="f3094-373">Sağlayıcı diğer adları</span><span class="sxs-lookup"><span data-stu-id="f3094-373">Provider aliases</span></span>

<span data-ttu-id="f3094-374">Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *diğer ad* tanımlar.</span><span class="sxs-lookup"><span data-stu-id="f3094-374">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="f3094-375">Yerleşik sağlayıcılar için aşağıdaki diğer adları kullanın:</span><span class="sxs-lookup"><span data-stu-id="f3094-375">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="f3094-376">Konsol</span><span class="sxs-lookup"><span data-stu-id="f3094-376">Console</span></span>
* <span data-ttu-id="f3094-377">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="f3094-377">Debug</span></span>
* <span data-ttu-id="f3094-378">EventSource</span><span class="sxs-lookup"><span data-stu-id="f3094-378">EventSource</span></span>
* <span data-ttu-id="f3094-379">EventLog</span><span class="sxs-lookup"><span data-stu-id="f3094-379">EventLog</span></span>
* <span data-ttu-id="f3094-380">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f3094-380">TraceSource</span></span>
* <span data-ttu-id="f3094-381">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f3094-381">AzureAppServicesFile</span></span>
* <span data-ttu-id="f3094-382">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f3094-382">AzureAppServicesBlob</span></span>
* <span data-ttu-id="f3094-383">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f3094-383">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="f3094-384">Varsayılan en düşük düzey</span><span class="sxs-lookup"><span data-stu-id="f3094-384">Default minimum level</span></span>

<span data-ttu-id="f3094-385">Yalnızca belirli bir sağlayıcı ve kategori için yapılandırma veya koddan kural uygulanmaz geçerli olan en düşük düzey ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="f3094-385">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="f3094-386">Aşağıdaki örnekte, en düşük düzeyin nasıl ayarlanacağı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f3094-386">The following example shows how to set the minimum level:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

<span data-ttu-id="f3094-387">En düşük düzeyi açıkça ayarlamazsanız, varsayılan değer `Information`, bu da `Trace` ve `Debug` günlüklerinin yoksayıldığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f3094-387">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="f3094-388">Filtre işlevleri</span><span class="sxs-lookup"><span data-stu-id="f3094-388">Filter functions</span></span>

<span data-ttu-id="f3094-389">Configuration veya Code tarafından kendisine atanmış kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağırılır.</span><span class="sxs-lookup"><span data-stu-id="f3094-389">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="f3094-390">İşlevindeki kodun sağlayıcı türü, kategorisi ve günlük düzeyine erişimi vardır.</span><span class="sxs-lookup"><span data-stu-id="f3094-390">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="f3094-391">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f3094-391">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="f3094-392">Sistem kategorileri ve Düzeyler</span><span class="sxs-lookup"><span data-stu-id="f3094-392">System categories and levels</span></span>

<span data-ttu-id="f3094-393">ASP.NET Core ve Entity Framework Core tarafından kullanılan bazı kategoriler şunlardır ve bunlardan beklenen Günlükler hakkında notlar bulunur:</span><span class="sxs-lookup"><span data-stu-id="f3094-393">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="f3094-394">Kategori</span><span class="sxs-lookup"><span data-stu-id="f3094-394">Category</span></span>                            | <span data-ttu-id="f3094-395">Notlar</span><span class="sxs-lookup"><span data-stu-id="f3094-395">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="f3094-396">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="f3094-396">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="f3094-397">Genel ASP.NET Core tanılama.</span><span class="sxs-lookup"><span data-stu-id="f3094-397">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="f3094-398">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="f3094-398">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="f3094-399">Hangi anahtarların kabul edildiği, bulunduğu ve kullanıldığı.</span><span class="sxs-lookup"><span data-stu-id="f3094-399">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="f3094-400">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="f3094-400">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="f3094-401">İzin verilen konaklar.</span><span class="sxs-lookup"><span data-stu-id="f3094-401">Hosts allowed.</span></span> |
| <span data-ttu-id="f3094-402">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="f3094-402">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="f3094-403">HTTP isteklerinin tamamlanması için geçen süre ve ne zaman başladıkları.</span><span class="sxs-lookup"><span data-stu-id="f3094-403">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="f3094-404">Hangi barındırma başlangıç derlemeleri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="f3094-404">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="f3094-405">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="f3094-405">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="f3094-406">MVC ve Razor tanılama.</span><span class="sxs-lookup"><span data-stu-id="f3094-406">MVC and Razor diagnostics.</span></span> <span data-ttu-id="f3094-407">Model bağlama, filtre yürütme, derlemeyi görüntüleme, eylem seçimi.</span><span class="sxs-lookup"><span data-stu-id="f3094-407">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="f3094-408">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="f3094-408">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="f3094-409">Eşleşen bilgileri yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="f3094-409">Route matching information.</span></span> |
| <span data-ttu-id="f3094-410">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="f3094-410">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="f3094-411">Bağlantı başlatın, durdurun ve canlı yanıtları koruyun.</span><span class="sxs-lookup"><span data-stu-id="f3094-411">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="f3094-412">HTTPS sertifika bilgileri.</span><span class="sxs-lookup"><span data-stu-id="f3094-412">HTTPS certificate information.</span></span> |
| <span data-ttu-id="f3094-413">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="f3094-413">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="f3094-414">Sunulan dosyalar.</span><span class="sxs-lookup"><span data-stu-id="f3094-414">Files served.</span></span> |
| <span data-ttu-id="f3094-415">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="f3094-415">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="f3094-416">Genel Entity Framework Core tanılama.</span><span class="sxs-lookup"><span data-stu-id="f3094-416">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="f3094-417">Veritabanı etkinliği ve yapılandırması, değişiklik algılama, geçişler.</span><span class="sxs-lookup"><span data-stu-id="f3094-417">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="f3094-418">Günlük kapsamları</span><span class="sxs-lookup"><span data-stu-id="f3094-418">Log scopes</span></span>

 <span data-ttu-id="f3094-419">*Kapsam* bir mantıksal işlemler kümesini gruplandırabilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-419">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="f3094-420">Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-420">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="f3094-421">Örneğin, bir işlemin işlenmesi kapsamında oluşturulan her günlük işlem KIMLIĞI içerebilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-421">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="f3094-422">Kapsam, <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> yöntemi tarafından döndürülen ve atılana kadar bir `IDisposable` türüdür.</span><span class="sxs-lookup"><span data-stu-id="f3094-422">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="f3094-423">`using` bloğunda günlükçü çağrılarını sarmalayarak kapsam kullanın:</span><span class="sxs-lookup"><span data-stu-id="f3094-423">Use a scope by wrapping logger calls in a `using` block:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

<span data-ttu-id="f3094-424">Aşağıdaki kod konsol sağlayıcısı için kapsamları etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="f3094-424">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="f3094-425">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f3094-425">*Program.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="f3094-426">`IncludeScopes` konsolu günlükçüsü seçeneğini yapılandırmak, kapsam tabanlı günlüğe kaydetmeyi etkinleştirmek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f3094-426">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="f3094-427">Yapılandırma hakkında bilgi için [yapılandırma](#configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="f3094-427">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="f3094-428">Her günlük iletisi kapsamlı bilgiler içerir:</span><span class="sxs-lookup"><span data-stu-id="f3094-428">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="f3094-429">Yerleşik günlük oluşturma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="f3094-429">Built-in logging providers</span></span>

<span data-ttu-id="f3094-430">ASP.NET Core aşağıdaki sağlayıcıları sevk eder:</span><span class="sxs-lookup"><span data-stu-id="f3094-430">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="f3094-431">Console</span><span class="sxs-lookup"><span data-stu-id="f3094-431">Console</span></span>](#console-provider)
* [<span data-ttu-id="f3094-432">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="f3094-432">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="f3094-433">EventSource</span><span class="sxs-lookup"><span data-stu-id="f3094-433">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="f3094-434">EventLog</span><span class="sxs-lookup"><span data-stu-id="f3094-434">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="f3094-435">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f3094-435">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="f3094-436">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f3094-436">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f3094-437">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f3094-437">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f3094-438">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f3094-438">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="f3094-439">ASP.NET Core modülüyle stdout ve hata ayıklama günlüğü hakkında daha fazla bilgi için, bkz. <xref:test/troubleshoot-azure-iis> ve <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="f3094-439">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="f3094-440">Konsol sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="f3094-440">Console provider</span></span>

<span data-ttu-id="f3094-441">[Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sağlayıcı paketi, günlük çıktısını konsola gönderir.</span><span class="sxs-lookup"><span data-stu-id="f3094-441">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="f3094-442">Konsol günlüğü çıkışını görmek için proje klasöründe bir komut istemi açın ve aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f3094-442">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="f3094-443">Hata ayıklama sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="f3094-443">Debug provider</span></span>

<span data-ttu-id="f3094-444">[Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi, [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) sınıfını (`Debug.WriteLine` Yöntem çağrıları) kullanarak günlük çıktısını yazar.</span><span class="sxs-lookup"><span data-stu-id="f3094-444">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="f3094-445">Linux 'ta, bu sağlayıcı günlükleri */var/log/Message*dosyasına yazar.</span><span class="sxs-lookup"><span data-stu-id="f3094-445">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="f3094-446">Olay kaynak sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="f3094-446">Event Source provider</span></span>

<span data-ttu-id="f3094-447">[Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi, `Microsoft-Extensions-Logging`adı Ile bir olay kaynağı platformlar arası yazar.</span><span class="sxs-lookup"><span data-stu-id="f3094-447">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="f3094-448">Windows 'da, sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.</span><span class="sxs-lookup"><span data-stu-id="f3094-448">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="f3094-449">Konak oluşturmak için `CreateDefaultBuilder` çağrıldığında olay kaynak sağlayıcısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="f3094-449">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

::: moniker range=">= aspnetcore-3.0"

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="f3094-450">DotNet izleme araçları</span><span class="sxs-lookup"><span data-stu-id="f3094-450">dotnet trace tooling</span></span>

<span data-ttu-id="f3094-451">[DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace) Aracı, çalışan bir Işlemin .NET Core izlemelerinin toplanmasını sağlayan platformlar arası CLI genel aracıdır.</span><span class="sxs-lookup"><span data-stu-id="f3094-451">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="f3094-452">Araç, bir <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>kullanarak <xref:Microsoft.Extensions.Logging.EventSource> sağlayıcı verileri toplar.</span><span class="sxs-lookup"><span data-stu-id="f3094-452">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="f3094-453">DotNet Trace araçları komutunu aşağıdaki komutla birlikte yüklersiniz:</span><span class="sxs-lookup"><span data-stu-id="f3094-453">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="f3094-454">Bir uygulamadan izleme toplamak için DotNet Trace araçları kullanın:</span><span class="sxs-lookup"><span data-stu-id="f3094-454">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="f3094-455">Uygulama ana bilgisayarı `CreateDefaultBuilder`oluşturmaz, [olay kaynak sağlayıcısını](#event-source-provider) uygulamanın günlük yapılandırmasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f3094-455">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="f3094-456">`dotnet run` komutuyla uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f3094-456">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="f3094-457">.NET Core uygulamasının işlem tanımlayıcısını (PID) belirleme:</span><span class="sxs-lookup"><span data-stu-id="f3094-457">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="f3094-458">Windows 'ta aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f3094-458">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="f3094-459">Görev Yöneticisi (Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="f3094-459">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="f3094-460">Tasklist komutu</span><span class="sxs-lookup"><span data-stu-id="f3094-460">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="f3094-461">Get-Process PowerShell komutu</span><span class="sxs-lookup"><span data-stu-id="f3094-461">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="f3094-462">Linux 'ta [pidof komutunu](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)kullanın.</span><span class="sxs-lookup"><span data-stu-id="f3094-462">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="f3094-463">Uygulamanın derlemesi ile aynı ada sahip olan işlem için PID 'i bulun.</span><span class="sxs-lookup"><span data-stu-id="f3094-463">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="f3094-464">`dotnet trace` komutunu yürütün.</span><span class="sxs-lookup"><span data-stu-id="f3094-464">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="f3094-465">Genel komut sözdizimi:</span><span class="sxs-lookup"><span data-stu-id="f3094-465">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="f3094-466">PowerShell komut kabuğu kullanırken `--providers` değerini tek tırnak içine alın (`'`):</span><span class="sxs-lookup"><span data-stu-id="f3094-466">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="f3094-467">Windows dışı platformlarda, çıkış izleme dosyasının biçimini `speedscope`olarak değiştirmek için `-f speedscope` seçeneğini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f3094-467">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="f3094-468">Anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="f3094-468">Keyword</span></span> | <span data-ttu-id="f3094-469">Açıklama</span><span class="sxs-lookup"><span data-stu-id="f3094-469">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="f3094-470">1\.</span><span class="sxs-lookup"><span data-stu-id="f3094-470">1</span></span>       | <span data-ttu-id="f3094-471">`LoggingEventSource`ilgili meta olayları günlüğe kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f3094-471">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="f3094-472">Olayları `ILogger`) günlüğe kaydetmez.</span><span class="sxs-lookup"><span data-stu-id="f3094-472">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="f3094-473">2</span><span class="sxs-lookup"><span data-stu-id="f3094-473">2</span></span>       | <span data-ttu-id="f3094-474">`ILogger.Log()` çağrıldığında `Message` olayı açar.</span><span class="sxs-lookup"><span data-stu-id="f3094-474">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f3094-475">Programlı (biçimlendirilmedi) bir şekilde bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f3094-475">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="f3094-476">4</span><span class="sxs-lookup"><span data-stu-id="f3094-476">4</span></span>       | <span data-ttu-id="f3094-477">`ILogger.Log()` çağrıldığında `FormatMessage` olayı açar.</span><span class="sxs-lookup"><span data-stu-id="f3094-477">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f3094-478">, Bilgilerin biçimlendirilen dize sürümünü sağlar.</span><span class="sxs-lookup"><span data-stu-id="f3094-478">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="f3094-479">8</span><span class="sxs-lookup"><span data-stu-id="f3094-479">8</span></span>       | <span data-ttu-id="f3094-480">`ILogger.Log()` çağrıldığında `MessageJson` olayı açar.</span><span class="sxs-lookup"><span data-stu-id="f3094-480">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f3094-481">Bağımsız değişkenlerin JSON gösterimini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f3094-481">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="f3094-482">Olay düzeyi</span><span class="sxs-lookup"><span data-stu-id="f3094-482">Event Level</span></span> | <span data-ttu-id="f3094-483">Açıklama</span><span class="sxs-lookup"><span data-stu-id="f3094-483">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="f3094-484">0</span><span class="sxs-lookup"><span data-stu-id="f3094-484">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="f3094-485">1\.</span><span class="sxs-lookup"><span data-stu-id="f3094-485">1</span></span>           | `Critical`      |
   | <span data-ttu-id="f3094-486">2</span><span class="sxs-lookup"><span data-stu-id="f3094-486">2</span></span>           | `Error`         |
   | <span data-ttu-id="f3094-487">3</span><span class="sxs-lookup"><span data-stu-id="f3094-487">3</span></span>           | `Warning`       |
   | <span data-ttu-id="f3094-488">4</span><span class="sxs-lookup"><span data-stu-id="f3094-488">4</span></span>           | `Informational` |
   | <span data-ttu-id="f3094-489">5</span><span class="sxs-lookup"><span data-stu-id="f3094-489">5</span></span>           | `Verbose`       |

   <span data-ttu-id="f3094-490">`{Logger Category}` ve `{Event Level}` için `FilterSpecs` girişleri ek günlük filtreleme koşullarını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f3094-490">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="f3094-491">`FilterSpecs` girdileri noktalı virgülle ayırın (`;`).</span><span class="sxs-lookup"><span data-stu-id="f3094-491">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="f3094-492">Windows komut kabuğu ile örnek (`--providers` değeri etrafında tek tırnak**yoktur** ):</span><span class="sxs-lookup"><span data-stu-id="f3094-492">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="f3094-493">Yukarıdaki komut şunları etkinleştirir:</span><span class="sxs-lookup"><span data-stu-id="f3094-493">The preceding command activates:</span></span>

   * <span data-ttu-id="f3094-494">Hatalar için (`2`) biçimlendirilen dizeler (`4`) üretmek üzere olay kaynağı günlükçüsü.</span><span class="sxs-lookup"><span data-stu-id="f3094-494">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="f3094-495">günlüğe kaydetme `Informational` günlük düzeyinde (`4`) `Microsoft.AspNetCore.Hosting`.</span><span class="sxs-lookup"><span data-stu-id="f3094-495">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="f3094-496">ENTER tuşuna veya CTRL + C tuşlarına basarak DotNet izleme araçlarını durdurun.</span><span class="sxs-lookup"><span data-stu-id="f3094-496">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="f3094-497">İzleme, `dotnet trace` komutunun yürütüldüğü klasörde *Trace. NetTrace* adıyla kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-497">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="f3094-498">Trace 'i [PerfView](#perfview)ile açın.</span><span class="sxs-lookup"><span data-stu-id="f3094-498">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="f3094-499">*Trace. NetTrace* dosyasını açın ve izleme olaylarını araştırın.</span><span class="sxs-lookup"><span data-stu-id="f3094-499">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="f3094-500">Daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="f3094-500">For more information, see:</span></span>

* <span data-ttu-id="f3094-501">[Performans Analizi yardımcı programı Için izleme (DotNet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core belgeleri)</span><span class="sxs-lookup"><span data-stu-id="f3094-501">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="f3094-502">[Performans Analizi yardımcı programı (DotNet-Trace) Için izleme](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (DotNet/Diagnostics GitHub deposu belgeleri)</span><span class="sxs-lookup"><span data-stu-id="f3094-502">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="f3094-503">[Loggingeventsource sınıfı](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API tarayıcısı)</span><span class="sxs-lookup"><span data-stu-id="f3094-503">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="f3094-504">[Loggingeventsource başvuru kaynağı (3,0)](https://github.com/aspnet/Extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; farklı bir sürüm için başvuru kaynağı elde etmek üzere, dalı `release/{Version}`olarak değiştirin, burada `{Version}` istenen ASP.NET Core sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="f3094-504">[LoggingEventSource reference source (3.0)](https://github.com/aspnet/Extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="f3094-505">[PerfView](#perfview) , olay kaynağı izlemelerini görüntülemek için kullanışlıdır &ndash;.</span><span class="sxs-lookup"><span data-stu-id="f3094-505">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="f3094-506">PerfView</span><span class="sxs-lookup"><span data-stu-id="f3094-506">Perfview</span></span>

::: moniker-end

<span data-ttu-id="f3094-507">Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın.</span><span class="sxs-lookup"><span data-stu-id="f3094-507">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="f3094-508">ETW günlüklerini görüntülemeye yönelik başka araçlar da mevcuttur, ancak PerfView, ASP.NET Core tarafından yayınlanan ETW olaylarıyla çalışmak için en iyi deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f3094-508">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="f3094-509">Bu sağlayıcı tarafından günlüğe kaydedilen olayları toplamak için PerfView 'ı yapılandırmak için, `*Microsoft-Extensions-Logging` dizeyi **ek sağlayıcılar** listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f3094-509">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="f3094-510">(Dizenin başlangıcında yıldız işaretini kaçırmayın.)</span><span class="sxs-lookup"><span data-stu-id="f3094-510">(Don't miss the asterisk at the start of the string.)</span></span>

![PerfView ek sağlayıcıları](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="f3094-512">Windows olay günlüğü sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="f3094-512">Windows EventLog provider</span></span>

<span data-ttu-id="f3094-513">[Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi, Windows olay günlüğüne günlük çıktısı gönderir.</span><span class="sxs-lookup"><span data-stu-id="f3094-513">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="f3094-514">[AddEventLog aşırı yüklemeler](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>iletmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f3094-514">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span>

### <a name="tracesource-provider"></a><span data-ttu-id="f3094-515">TraceSource sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="f3094-515">TraceSource provider</span></span>

<span data-ttu-id="f3094-516">[Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıklarını ve sağlayıcıları kullanır.</span><span class="sxs-lookup"><span data-stu-id="f3094-516">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="f3094-517">[Addtracesource aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) , bir kaynak anahtarı ve bir izleme dinleyicisi geçirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="f3094-517">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="f3094-518">Bu sağlayıcıyı kullanmak için, bir uygulamanın .NET Framework çalışması gerekir (.NET Core yerine).</span><span class="sxs-lookup"><span data-stu-id="f3094-518">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="f3094-519">Sağlayıcı, iletileri örnek uygulamada kullanılan <xref:System.Diagnostics.TextWriterTraceListener> gibi çeşitli [dinleyicilerine](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-519">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="f3094-520">Azure App Service sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="f3094-520">Azure App Service provider</span></span>

<span data-ttu-id="f3094-521">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, günlükleri bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına ve bir Azure depolama hesabındaki [BLOB depolama](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) alanına yazar.</span><span class="sxs-lookup"><span data-stu-id="f3094-521">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3094-522">Sağlayıcı paketi, paylaşılan çerçeveye dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="f3094-522">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="f3094-523">Sağlayıcıyı kullanmak için sağlayıcı paketini projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f3094-523">To use the provider, add the provider package to the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f3094-524">Sağlayıcı paketi [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="f3094-524">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="f3094-525">.NET Framework veya `Microsoft.AspNetCore.App` metapackage 'e başvuru yaparken, sağlayıcı paketini projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f3094-525">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3094-526">Sağlayıcı ayarlarını yapılandırmak için aşağıdaki örnekte gösterildiği gibi <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> ve <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>kullanın:</span><span class="sxs-lookup"><span data-stu-id="f3094-526">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f3094-527">Sağlayıcı ayarlarını yapılandırmak için aşağıdaki örnekte gösterildiği gibi <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> ve <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>kullanın:</span><span class="sxs-lookup"><span data-stu-id="f3094-527">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="f3094-528"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> aşırı yüklemesi <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>geçirmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f3094-528">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="f3094-529">Ayarlar nesnesi, günlük çıkış şablonu, blob adı ve dosya boyutu sınırı gibi varsayılan ayarları geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-529">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="f3094-530">(*Çıktı şablonu* , bir `ILogger` yöntemi çağrısıyla sağlandığının yanı sıra tüm günlüklere uygulanan bir ileti şablonudur.)</span><span class="sxs-lookup"><span data-stu-id="f3094-530">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

<span data-ttu-id="f3094-531">App Service uygulamasına dağıtırken, uygulama, Azure portal **App Service** sayfasının [App Service Günlükler](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarları kabul eder.</span><span class="sxs-lookup"><span data-stu-id="f3094-531">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="f3094-532">Aşağıdaki ayarlar güncelleştirilirken, değişiklikler uygulamanın yeniden başlatılmasını veya yeniden dağıtımı gerekmeden hemen etkili olur.</span><span class="sxs-lookup"><span data-stu-id="f3094-532">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="f3094-533">**Uygulama günlüğü (dosya sistemi)**</span><span class="sxs-lookup"><span data-stu-id="f3094-533">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="f3094-534">**Uygulama günlüğü (blob)**</span><span class="sxs-lookup"><span data-stu-id="f3094-534">**Application Logging (Blob)**</span></span>

<span data-ttu-id="f3094-535">Günlük dosyaları için varsayılan konum *D:\\home\\LogFiles\\uygulama* klasöründedir ve varsayılan dosya adı *Diagnostics-YYYYMMDD. txt*' dir.</span><span class="sxs-lookup"><span data-stu-id="f3094-535">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="f3094-536">Varsayılan dosya boyutu sınırı 10 MB 'tır ve tutulan varsayılan en fazla dosya sayısı 2 ' dir.</span><span class="sxs-lookup"><span data-stu-id="f3094-536">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="f3094-537">Varsayılan blob adı *{app-name} {timestamp}/yyyy/mm/dd/ss/{Guid}-ApplicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="f3094-537">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="f3094-538">Sağlayıcı yalnızca proje Azure ortamında çalıştırıldığında çalışır.</span><span class="sxs-lookup"><span data-stu-id="f3094-538">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="f3094-539">Proje yerel olarak çalıştırıldığında, yerel dosyalara veya Bloblar için yerel geliştirme depolamasına yazmazsa&mdash;hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="f3094-539">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="f3094-540">Azure günlük akışı</span><span class="sxs-lookup"><span data-stu-id="f3094-540">Azure log streaming</span></span>

<span data-ttu-id="f3094-541">Azure günlük akışı, günlük etkinliklerini gerçek zamanlı olarak görüntülemenize izin verir:</span><span class="sxs-lookup"><span data-stu-id="f3094-541">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="f3094-542">Uygulama sunucusu</span><span class="sxs-lookup"><span data-stu-id="f3094-542">The app server</span></span>
* <span data-ttu-id="f3094-543">Web sunucusu</span><span class="sxs-lookup"><span data-stu-id="f3094-543">The web server</span></span>
* <span data-ttu-id="f3094-544">Başarısız istek izleme</span><span class="sxs-lookup"><span data-stu-id="f3094-544">Failed request tracing</span></span>

<span data-ttu-id="f3094-545">Azure günlük akışını yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="f3094-545">To configure Azure log streaming:</span></span>

* <span data-ttu-id="f3094-546">Uygulamanızın Portal sayfasından **App Service günlükleri** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="f3094-546">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="f3094-547">**Uygulama günlüğünü (FileSystem)** **Açık**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f3094-547">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="f3094-548">Günlük **düzeyini**seçin.</span><span class="sxs-lookup"><span data-stu-id="f3094-548">Choose the log **Level**.</span></span> <span data-ttu-id="f3094-549">Bu ayar, uygulamadaki diğer günlük sağlayıcılarını değil, yalnızca Azure günlük akışı için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f3094-549">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="f3094-550">Uygulama iletilerini görüntülemek için **günlük akışı** sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="f3094-550">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="f3094-551">Uygulama tarafından `ILogger` arabirimi aracılığıyla günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-551">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="f3094-552">Azure Application Insights izleme günlüğü</span><span class="sxs-lookup"><span data-stu-id="f3094-552">Azure Application Insights trace logging</span></span>

<span data-ttu-id="f3094-553">[Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi günlükleri Azure Application Insights yazar.</span><span class="sxs-lookup"><span data-stu-id="f3094-553">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="f3094-554">Application Insights, bir Web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="f3094-554">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="f3094-555">Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve analiz edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f3094-555">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="f3094-556">Günlüğe kaydetme sağlayıcısı, ASP.NET Core için tüm kullanılabilir telemetri sağlayan paket olan [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)'un bağımlılığı olarak eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="f3094-556">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="f3094-557">Bu paketi kullanırsanız, sağlayıcı paketini yüklemek zorunda kalmazsınız.</span><span class="sxs-lookup"><span data-stu-id="f3094-557">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="f3094-558">ASP.NET 4. x için olan [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini kullanmayın&mdash;.</span><span class="sxs-lookup"><span data-stu-id="f3094-558">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="f3094-559">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="f3094-559">For more information, see the following resources:</span></span>

* [<span data-ttu-id="f3094-560">Application Insights genel bakış</span><span class="sxs-lookup"><span data-stu-id="f3094-560">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="f3094-561">[ASP.NET Core uygulamalar için Application Insights](/azure/azure-monitor/app/asp-net-core) -günlük kaydı ile birlikte Application Insights telemetrinin tam aralığını uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="f3094-561">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="f3094-562">[.NET Core ILogger günlükleri Için Applicationınsightsloggerprovider](/azure/azure-monitor/app/ilogger) -günlük sağlayıcısını Application Insights telemetri olmadan uygulamak istiyorsanız buraya başlayın.</span><span class="sxs-lookup"><span data-stu-id="f3094-562">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="f3094-563">[Günlüğe kaydetme bağdaştırıcılarını Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="f3094-563">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="f3094-564">Microsoft Learn sitede Application Insights SDK-etkileşimli öğreticisini [yükleyip başlatın](/learn/modules/instrument-web-app-code-with-application-insights) .</span><span class="sxs-lookup"><span data-stu-id="f3094-564">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="f3094-565">Üçüncü taraf günlük oluşturma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="f3094-565">Third-party logging providers</span></span>

<span data-ttu-id="f3094-566">ASP.NET Core ile birlikte çalışan üçüncü taraf günlük çerçeveleri:</span><span class="sxs-lookup"><span data-stu-id="f3094-566">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="f3094-567">[ELMAH.io](https://elmah.io/) ([GitHub deposu](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f3094-567">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="f3094-568">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub deposu](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="f3094-568">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="f3094-569">[Jsnlog](https://jsnlog.com/) ([GitHub deposu](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="f3094-569">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="f3094-570">[KissLog.net](https://kisslog.net/) ([GitHub deposu](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="f3094-570">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="f3094-571">[Log4Net](https://logging.apache.org/log4net/) ([GitHub deposu](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="f3094-571">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="f3094-572">[Loggr](https://loggr.net/) ([GitHub deposu](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f3094-572">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="f3094-573">[NLog](https://nlog-project.org/) ([GitHub deposu](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f3094-573">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="f3094-574">[Sentry](https://sentry.io/welcome/) ([GitHub deposu](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f3094-574">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="f3094-575">[Serilog](https://serilog.net/) ([GitHub deposu](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="f3094-575">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="f3094-576">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub deposu](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f3094-576">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="f3094-577">Bazı üçüncü taraf çerçeveler [, yapılandırılmış günlük olarak da bilinen anlam günlüğe kaydetme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)işlemini gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="f3094-577">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="f3094-578">Bir üçüncü taraf çerçevesinin kullanılması, yerleşik sağlayıcılardan birini kullanmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="f3094-578">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="f3094-579">Projenize bir NuGet paketi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f3094-579">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="f3094-580">Günlüğe kaydetme çerçevesi tarafından sağlanmış bir `ILoggerFactory` Extension yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="f3094-580">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="f3094-581">Daha fazla bilgi için bkz. her sağlayıcının belgeleri.</span><span class="sxs-lookup"><span data-stu-id="f3094-581">For more information, see each provider's documentation.</span></span> <span data-ttu-id="f3094-582">Üçüncü taraf günlüğü sağlayıcıları Microsoft tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="f3094-582">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3094-583">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f3094-583">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
