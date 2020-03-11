---
title: ASP.NET Core Web sunucusu uygulamasını Kestrel
author: rick-anderson
description: ASP.NET Core platformlar arası Web sunucusu olan Kestrel hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 8d96118800c47b2c551726342bf4cfba9671a09e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667372"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="993c5-103">ASP.NET Core Web sunucusu uygulamasını Kestrel</span><span class="sxs-lookup"><span data-stu-id="993c5-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="993c5-104">[Tom Dykstra](https://github.com/tdykstra), [Chris](https://github.com/Tratcher), ve [Stephen halter](https://twitter.com/halter73) tarafından</span><span class="sxs-lookup"><span data-stu-id="993c5-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="993c5-105">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="993c5-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="993c5-106">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="993c5-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="993c5-107">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="993c5-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="993c5-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="993c5-108">HTTPS</span></span>
* <span data-ttu-id="993c5-109">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="993c5-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="993c5-110">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="993c5-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="993c5-111">HTTP/2 (macOS&dagger;hariç)</span><span class="sxs-lookup"><span data-stu-id="993c5-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="993c5-112">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="993c5-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="993c5-113">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="993c5-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="993c5-114">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="993c5-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="993c5-115">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="993c5-115">HTTP/2 support</span></span>

<span data-ttu-id="993c5-116">Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="993c5-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="993c5-117">İşletim sistemi&dagger;</span><span class="sxs-lookup"><span data-stu-id="993c5-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="993c5-118">Windows Server 2016/Windows 10 veya üzeri&Dagger;</span><span class="sxs-lookup"><span data-stu-id="993c5-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="993c5-119">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="993c5-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="993c5-120">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="993c5-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="993c5-121">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="993c5-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="993c5-122">TLS 1.2 veya sonraki bir bağlantı</span><span class="sxs-lookup"><span data-stu-id="993c5-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="993c5-123">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="993c5-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="993c5-124">&Dagger;Kestrel Windows Server 2012 R2 ve Windows 8.1 üzerinde HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="993c5-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="993c5-125">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="993c5-126">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="993c5-127">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="993c5-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="993c5-128">HTTP/2 varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="993c5-129">Yapılandırma hakkında daha fazla bilgi için [Kestrel Options](#kestrel-options) ve [Listenoptions. Protocols](#listenoptionsprotocols) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="993c5-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="993c5-130">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="993c5-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="993c5-131">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu*ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="993c5-132">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="993c5-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="993c5-133">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="993c5-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="993c5-135">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="993c5-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="993c5-137">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="993c5-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="993c5-138">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="993c5-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="993c5-139">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel `Host` ' den bağımsız olarak bu bağlantı noktası için tüm trafiği işler.</span><span class="sxs-lookup"><span data-stu-id="993c5-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="993c5-140">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="993c5-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="993c5-141">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="993c5-142">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="993c5-142">A reverse proxy:</span></span>

* <span data-ttu-id="993c5-143">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="993c5-144">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="993c5-145">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="993c5-146">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="993c5-147">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="993c5-148">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="993c5-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="993c5-149">ASP.NET Core uygulamalarda Kestrel</span><span class="sxs-lookup"><span data-stu-id="993c5-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="993c5-150">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="993c5-151">*Program.cs*içinde <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> yöntemi <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>çağırır:</span><span class="sxs-lookup"><span data-stu-id="993c5-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="993c5-152">Konak oluşturma hakkında daha fazla bilgi için <xref:fundamentals/host/generic-host#set-up-a-host>*ana bilgisayar* ve *Varsayılan Oluşturucu ayarlarını* ayarlama bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="993c5-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="993c5-153">`ConfigureWebHostDefaults`çağrıldıktan sonra ek yapılandırma sağlamak için `ConfigureKestrel`kullanın:</span><span class="sxs-lookup"><span data-stu-id="993c5-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="993c5-154">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="993c5-154">Kestrel options</span></span>

<span data-ttu-id="993c5-155">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="993c5-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="993c5-156"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> sınıfının <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> özelliğindeki kısıtlamaları ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="993c5-157">`Limits` özelliği <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> sınıfının bir örneğini barındırır.</span><span class="sxs-lookup"><span data-stu-id="993c5-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="993c5-158">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="993c5-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="993c5-159">Bu makalenin ilerleyen kısımlarında gösterilen örneklerde, Kestrel seçenekleri C# kodda yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="993c5-160">Ayrıca, Kestrel seçenekleri bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="993c5-161">Örneğin, [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) bir *appSettings. JSON* veya appSettings 'ten Kestrel yapılandırmasını yükleyebilir *. { Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="993c5-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="993c5-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> ve [uç nokta yapılandırması](#endpoint-configuration) yapılandırma sağlayıcılarından yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="993c5-163">Kod içinde C# kalan Kestrel yapılandırması yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="993c5-164">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="993c5-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="993c5-165">`Startup.ConfigureServices`'de Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="993c5-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="993c5-166">`Startup` sınıfına bir `IConfiguration` örneği ekleyin.</span><span class="sxs-lookup"><span data-stu-id="993c5-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="993c5-167">Aşağıdaki örnek, eklenen yapılandırmanın `Configuration` özelliğine atandığını varsayar.</span><span class="sxs-lookup"><span data-stu-id="993c5-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="993c5-168">`Startup.ConfigureServices`, yapılandırmanın `Kestrel` bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="993c5-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="993c5-169">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="993c5-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="993c5-170">*Program.cs*' de, yapılandırmanın `Kestrel` bölümünü Kestrel yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="993c5-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="993c5-171">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="993c5-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="993c5-172">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="993c5-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="993c5-173">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="993c5-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="993c5-174">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="993c5-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="993c5-175">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="993c5-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="993c5-176">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="993c5-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="993c5-177">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="993c5-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="993c5-178">Bir bağlantı yükseltildikten sonra, `MaxConcurrentConnections` sınırına göre sayılmaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="993c5-179">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="993c5-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="993c5-180">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="993c5-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="993c5-181">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="993c5-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="993c5-182">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, bir eylem yönteminde <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="993c5-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="993c5-183">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="993c5-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="993c5-184">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="993c5-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="993c5-185">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="993c5-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="993c5-186">`MaxRequestBodySize` özelliğinin salt okuma durumunda olup olmadığını belirten `IsReadOnly` bir özellik vardır. Bu, sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="993c5-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="993c5-187">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="993c5-188">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="993c5-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="993c5-189">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="993c5-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="993c5-190">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="993c5-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="993c5-191">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="993c5-192">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="993c5-193">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="993c5-194">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, özellik ve arabirim adlarında `RequestBody` veya `Response` sahip olma dışında aynıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="993c5-195">*Program.cs*içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="993c5-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="993c5-196">Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="993c5-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="993c5-197">Önceki örnekte başvurulan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> HTTP/2 istekleri için `HttpContext.Features` mevcut değildir, çünkü bir istek temelli olarak hız sınırlarını değiştirmek, protokolün istek çoğullama desteği nedeniyle HTTP/2 için genel olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="993c5-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="993c5-198">Ancak, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> HTTP/2 istekleri için `HttpContext.Features` olmaya devam eder, çünkü okuma hızı sınırı, bir HTTP/2 isteği için bile `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` olarak ayarlanarak tamamen istek temelli olarak *devre dışı* bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="993c5-199">`IHttpMinRequestBodyDataRateFeature.MinDataRate` okumaya veya `null` dışında bir değere ayarlamaya çalışırken, bir HTTP/2 isteği verilen bir `NotSupportedException` atılmaya neden olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="993c5-200">`KestrelServerOptions.Limits` aracılığıyla yapılandırılan sunucu genelindeki hız sınırları hem HTTP/1. x hem de HTTP/2 bağlantıları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="993c5-201">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="993c5-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="993c5-202">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="993c5-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="993c5-203">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="993c5-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="993c5-204">Bağlantı başına en fazla akış</span><span class="sxs-lookup"><span data-stu-id="993c5-204">Maximum streams per connection</span></span>

<span data-ttu-id="993c5-205">`Http2.MaxStreamsPerConnection`, HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="993c5-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="993c5-206">Fazlalık akışlar reddedildi.</span><span class="sxs-lookup"><span data-stu-id="993c5-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="993c5-207">Varsayılan değer 100’dür.</span><span class="sxs-lookup"><span data-stu-id="993c5-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="993c5-208">Üst bilgi tablosu boyutu</span><span class="sxs-lookup"><span data-stu-id="993c5-208">Header table size</span></span>

<span data-ttu-id="993c5-209">HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar.</span><span class="sxs-lookup"><span data-stu-id="993c5-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="993c5-210">`Http2.HeaderTableSize`, HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlar.</span><span class="sxs-lookup"><span data-stu-id="993c5-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="993c5-211">Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="993c5-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="993c5-212">Varsayılan değer 4096 ' dir.</span><span class="sxs-lookup"><span data-stu-id="993c5-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="993c5-213">En büyük çerçeve boyutu</span><span class="sxs-lookup"><span data-stu-id="993c5-213">Maximum frame size</span></span>

<span data-ttu-id="993c5-214">`Http2.MaxFrameSize`, sunucu tarafından alınan veya gönderilen HTTP/2 bağlantı çerçevesi yükünün izin verilen en büyük boyutunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="993c5-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="993c5-215">Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="993c5-216">Varsayılan değer 2 ^ 14 ' dir (16.384).</span><span class="sxs-lookup"><span data-stu-id="993c5-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="993c5-217">En fazla istek üst bilgi boyutu</span><span class="sxs-lookup"><span data-stu-id="993c5-217">Maximum request header size</span></span>

<span data-ttu-id="993c5-218">`Http2.MaxRequestHeaderFieldSize`, istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu gösterir.</span><span class="sxs-lookup"><span data-stu-id="993c5-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="993c5-219">Bu sınır, sıkıştırılmış ve sıkıştırılmamış temsillerinde hem ad hem de değer için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="993c5-220">Değer sıfırdan büyük (0) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="993c5-221">Varsayılan değer 8.192 ' dir.</span><span class="sxs-lookup"><span data-stu-id="993c5-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="993c5-222">İlk bağlantı pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="993c5-222">Initial connection window size</span></span>

<span data-ttu-id="993c5-223">`Http2.InitialConnectionWindowSize`, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="993c5-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="993c5-224">İstekler aynı zamanda `Http2.InitialStreamWindowSize`ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="993c5-225">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="993c5-226">Varsayılan değer 128 KB 'tır (131.072).</span><span class="sxs-lookup"><span data-stu-id="993c5-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="993c5-227">İlk akış pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="993c5-227">Initial stream window size</span></span>

<span data-ttu-id="993c5-228">`Http2.InitialStreamWindowSize`, istek (Stream) başına bir seferde sunucu arabelleklerinin bayt cinsinden en yüksek istek gövde verilerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="993c5-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="993c5-229">İstekler aynı zamanda `Http2.InitialConnectionWindowSize`ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="993c5-230">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="993c5-231">Varsayılan değer 96 KB 'tır (98.304).</span><span class="sxs-lookup"><span data-stu-id="993c5-231">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="993c5-232">Zaman uyumlu GÇ</span><span class="sxs-lookup"><span data-stu-id="993c5-232">Synchronous IO</span></span>

<span data-ttu-id="993c5-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>, istek ve yanıt için zaman uyumlu GÇ 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="993c5-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="993c5-234">Varsayılan değer: `false`.</span><span class="sxs-lookup"><span data-stu-id="993c5-234">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="993c5-235">Çok sayıda engelleme zaman uyumlu GÇ işlemi, iş parçacığı havuzuna neden olabilir ve bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-235">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="993c5-236">Yalnızca zaman uyumsuz GÇ desteklemeyen bir kitaplık kullanırken `AllowSynchronousIO` etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="993c5-236">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="993c5-237">Aşağıdaki örnek, zaman uyumlu GÇ 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="993c5-237">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="993c5-238">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="993c5-238">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="993c5-239">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993c5-239">Endpoint configuration</span></span>

<span data-ttu-id="993c5-240">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="993c5-240">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="993c5-241">`https://localhost:5001` (yerel bir geliştirme sertifikası mevcut olduğunda)</span><span class="sxs-lookup"><span data-stu-id="993c5-241">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="993c5-242">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="993c5-242">Specify URLs using the:</span></span>

* <span data-ttu-id="993c5-243">ortam değişkeni `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="993c5-243">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="993c5-244">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="993c5-244">`--urls` command-line argument.</span></span>
* <span data-ttu-id="993c5-245">`urls` ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="993c5-245">`urls` host configuration key.</span></span>
* <span data-ttu-id="993c5-246">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="993c5-246">`UseUrls` extension method.</span></span>

<span data-ttu-id="993c5-247">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="993c5-247">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="993c5-248">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="993c5-248">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="993c5-249">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="993c5-249">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="993c5-250">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="993c5-250">A development certificate is created:</span></span>

* <span data-ttu-id="993c5-251">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="993c5-251">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="993c5-252">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-252">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="993c5-253">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="993c5-253">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="993c5-254">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="993c5-254">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="993c5-255">Kestrel için URL öneklerini ve bağlantı noktalarını yapılandırmak üzere <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> veya <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> yöntemlerini çağırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-255">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="993c5-256">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (HTTPS uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="993c5-256">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="993c5-257">`KestrelServerOptions` yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="993c5-257">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="993c5-258">ConfigureEndpointDefaults Varsayılanları (eylem\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="993c5-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="993c5-259">Belirtilen her bitiş noktası için çalıştırılacak bir yapılandırma `Action` belirtir.</span><span class="sxs-lookup"><span data-stu-id="993c5-259">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="993c5-260">`ConfigureEndpointDefaults` birden çok kez çağrılması, önceki `Action`s 'in belirtilen son `Action` yerini alır.</span><span class="sxs-lookup"><span data-stu-id="993c5-260">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="993c5-261"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> çağrılmadan oluşturulan bitiş noktaları, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> çağrılmadan **önce** , varsayılan olarak uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-261">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="993c5-262">ConfigureHttpsDefaults (eylem\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="993c5-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="993c5-263">Her HTTPS uç noktası için çalıştırılacak bir yapılandırma `Action` belirtir.</span><span class="sxs-lookup"><span data-stu-id="993c5-263">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="993c5-264">`ConfigureHttpsDefaults` birden çok kez çağrılması, önceki `Action`s 'in belirtilen son `Action` yerini alır.</span><span class="sxs-lookup"><span data-stu-id="993c5-264">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="993c5-265"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> çağrılmadan oluşturulan bitiş noktaları, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> çağrılmadan **önce** , varsayılan olarak uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="993c5-266">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="993c5-266">Configure(IConfiguration)</span></span>

<span data-ttu-id="993c5-267">Giriş olarak bir <xref:Microsoft.Extensions.Configuration.IConfiguration> alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="993c5-267">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="993c5-268">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-268">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="993c5-269">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="993c5-269">ListenOptions.UseHttps</span></span>

<span data-ttu-id="993c5-270">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-270">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="993c5-271">`ListenOptions.UseHttps` uzantıları:</span><span class="sxs-lookup"><span data-stu-id="993c5-271">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="993c5-272">`UseHttps` &ndash; varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-272">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="993c5-273">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="993c5-273">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="993c5-274">`ListenOptions.UseHttps` parametreleri:</span><span class="sxs-lookup"><span data-stu-id="993c5-274">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="993c5-275">`filename`, uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-275">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="993c5-276">X. 509.440 sertifika verilerine erişmek için gereken parola `password`.</span><span class="sxs-lookup"><span data-stu-id="993c5-276">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="993c5-277">`configureOptions`, `HttpsConnectionAdapterOptions`yapılandırmak için `Action`.</span><span class="sxs-lookup"><span data-stu-id="993c5-277">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="993c5-278">`ListenOptions`döndürür.</span><span class="sxs-lookup"><span data-stu-id="993c5-278">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="993c5-279">`storeName`, sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="993c5-279">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="993c5-280">`subject`, sertifikanın konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-280">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="993c5-281">`allowInvalid`, otomatik olarak imzalanan sertifikalar gibi geçersiz sertifikaların dikkate alınıp alınmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="993c5-281">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="993c5-282">`location`, sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="993c5-282">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="993c5-283">`serverCertificate` X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-283">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="993c5-284">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="993c5-284">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="993c5-285">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-285">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="993c5-286">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="993c5-286">Supported configurations described next:</span></span>

* <span data-ttu-id="993c5-287">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="993c5-287">No configuration</span></span>
* <span data-ttu-id="993c5-288">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="993c5-288">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="993c5-289">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="993c5-289">Change the defaults in code</span></span>

<span data-ttu-id="993c5-290">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="993c5-290">*No configuration*</span></span>

<span data-ttu-id="993c5-291">Kestrel `http://localhost:5000` ve `https://localhost:5001` dinler (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="993c5-291">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="993c5-292">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="993c5-292">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="993c5-293">Kestrel yapılandırmasını yüklemek için varsayılan olarak `Configure(context.Configuration.GetSection("Kestrel"))` `CreateDefaultBuilder` çağırır.</span><span class="sxs-lookup"><span data-stu-id="993c5-293">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="993c5-294">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-294">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="993c5-295">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-295">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="993c5-296">Aşağıdaki *appSettings. JSON* örneğinde:</span><span class="sxs-lookup"><span data-stu-id="993c5-296">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="993c5-297">Geçersiz sertifikaların (örneğin, otomatik olarak imzalanan sertifikalar) kullanılmasına izin vermek için, **Allowwınvalid** öğesini `true` olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-297">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="993c5-298">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan**Httpsdefaultcert** ), **varsayılan** veya geliştirme sertifikası > **Sertifikalar** altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="993c5-298">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="993c5-299">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="993c5-299">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="993c5-300">Örneğin, **sertifikalar** > **varsayılan** sertifika şöyle belirlenebilir:</span><span class="sxs-lookup"><span data-stu-id="993c5-300">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="993c5-301">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="993c5-301">Schema notes:</span></span>

* <span data-ttu-id="993c5-302">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-302">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="993c5-303">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-303">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="993c5-304">Her uç nokta için `Url` parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-304">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="993c5-305">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-305">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="993c5-306">Bu uç noktalar, üst düzey `Urls` yapılandırmasında tanımlananlara eklemek yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="993c5-306">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="993c5-307">`Listen` aracılığıyla kodda tanımlanan uç noktalar yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-307">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="993c5-308">`Certificate` bölümü isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-308">The `Certificate` section is optional.</span></span> <span data-ttu-id="993c5-309">`Certificate` bölümü belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-309">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="993c5-310">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-310">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="993c5-311">`Certificate` bölümü, hem **yol**&ndash;**parolasını** hem de **Konu**&ndash;**Mağaza** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="993c5-311">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="993c5-312">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-312">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="993c5-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))`, yapılandırılmış bir uç noktanın ayarlarını tamamlamak için kullanılabilecek `.Endpoint(string name, listenOptions => { })` yöntemi ile bir `KestrelConfigurationLoader` döndürür:</span><span class="sxs-lookup"><span data-stu-id="993c5-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="993c5-314">`KestrelServerOptions.ConfigurationLoader`, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>tarafından sağlana gibi var olan yükleyicisindeki yinelenmeye devam etmek için doğrudan erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-314">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="993c5-315">Her uç noktanın yapılandırma bölümü, özel ayarların okunabilmesi için `Endpoint` yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-315">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="993c5-316">`options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak birden çok yapılandırma yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-316">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="993c5-317">`Load` önceki örneklerde açıkça çağrılmamışsa yalnızca son yapılandırma kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-317">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="993c5-318">Metapackage, varsayılan yapılandırma bölümünün değiştirilebilmesi için `Load` çağırmaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-318">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="993c5-319">`KestrelConfigurationLoader`, API 'lerin `Listen` ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-319">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="993c5-320">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-320">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="993c5-321">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="993c5-321">*Change the defaults in code*</span></span>

<span data-ttu-id="993c5-322">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults`, önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma da dahil olmak üzere `ListenOptions` ve `HttpsConnectionAdapterOptions`için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-322">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="993c5-323">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-323">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="993c5-324">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="993c5-324">*Kestrel support for SNI*</span></span>

<span data-ttu-id="993c5-325">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-325">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="993c5-326">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="993c5-326">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="993c5-327">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-327">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="993c5-328">Kestrel `ServerCertificateSelector` geri çağırma aracılığıyla SNı destekler.</span><span class="sxs-lookup"><span data-stu-id="993c5-328">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="993c5-329">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-329">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="993c5-330">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="993c5-330">SNI support requires:</span></span>

* <span data-ttu-id="993c5-331">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="993c5-331">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="993c5-332">`net461` veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman `null`.</span><span class="sxs-lookup"><span data-stu-id="993c5-332">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="993c5-333">Ayrıca, istemci, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa da `null` `name`.</span><span class="sxs-lookup"><span data-stu-id="993c5-333">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="993c5-334">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="993c5-334">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="993c5-335">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="993c5-335">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="993c5-336">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="993c5-336">Connection logging</span></span>

<span data-ttu-id="993c5-337">Bir bağlantıda bayt düzeyinde iletişim için hata ayıklama düzeyi günlüklerini yayma <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> çağırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-337">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="993c5-338">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-338">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="993c5-339">`UseConnectionLogging` `UseHttps`önce yerleştirilmişse, şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-339">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="993c5-340">`UseConnectionLogging` `UseHttps`sonra yerleştirilmişse, şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-340">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="993c5-341">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="993c5-341">Bind to a TCP socket</span></span>

<span data-ttu-id="993c5-342"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> yöntemi bir TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="993c5-342">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="993c5-343">Örnek, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>olan bir uç nokta için HTTPS 'yi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="993c5-343">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="993c5-344">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="993c5-344">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="993c5-345">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="993c5-345">Bind to a Unix socket</span></span>

<span data-ttu-id="993c5-346">Aşağıdaki örnekte gösterildiği gibi NGINX ile daha iyi performans için <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="993c5-346">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="993c5-347">NGINX confiuguration dosyasında, `server` > `location` > `proxy_pass` girişi `http://unix:/tmp/{KESTREL SOCKET}:/;`olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-347">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="993c5-348">`{KESTREL SOCKET}`, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> için belirtilen yuvanın adıdır (örneğin, önceki örnekte `kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="993c5-348">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="993c5-349">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="993c5-349">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="993c5-350">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="993c5-350">Port 0</span></span>

<span data-ttu-id="993c5-351">`0` bağlantı noktası numarası belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-351">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="993c5-352">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="993c5-352">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="993c5-353">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="993c5-353">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="993c5-354">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="993c5-354">Limitations</span></span>

<span data-ttu-id="993c5-355">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="993c5-355">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="993c5-356">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="993c5-356">`--urls` command-line argument</span></span>
* <span data-ttu-id="993c5-357">`urls` ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="993c5-357">`urls` host configuration key</span></span>
* <span data-ttu-id="993c5-358">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="993c5-358">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="993c5-359">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-359">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="993c5-360">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="993c5-360">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="993c5-361">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece (örneğin, bu konuda daha önce gösterildiği gibi `KestrelServerOptions` yapılandırması veya yapılandırma dosyası kullanılarak), HTTPS bu yaklaşımlar ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-361">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="993c5-362">`Listen` ve `UseUrls` yaklaşımlarının her ikisi de aynı anda kullanıldığında `Listen` uç noktaları `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="993c5-362">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="993c5-363">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993c5-363">IIS endpoint configuration</span></span>

<span data-ttu-id="993c5-364">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları `Listen` veya `UseUrls`tarafından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-364">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="993c5-365">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="993c5-365">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="993c5-366">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="993c5-366">ListenOptions.Protocols</span></span>

<span data-ttu-id="993c5-367">`Protocols` özelliği, bir bağlantı uç noktasında veya sunucu için etkin HTTP protokollerini (`HttpProtocols`) belirler.</span><span class="sxs-lookup"><span data-stu-id="993c5-367">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="993c5-368">`HttpProtocols` numaralandırmasından `Protocols` özelliğine bir değer atayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-368">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="993c5-369">`HttpProtocols` Enum değeri</span><span class="sxs-lookup"><span data-stu-id="993c5-369">`HttpProtocols` enum value</span></span> | <span data-ttu-id="993c5-370">Bağlantı protokolü izin verildi</span><span class="sxs-lookup"><span data-stu-id="993c5-370">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="993c5-371">Yalnızca HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="993c5-371">HTTP/1.1 only.</span></span> <span data-ttu-id="993c5-372">, TLS olmadan veya ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-372">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="993c5-373">Yalnızca HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="993c5-373">HTTP/2 only.</span></span> <span data-ttu-id="993c5-374">Yalnızca istemci [önceki bir bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)DESTEKLIYORSA, TLS olmadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-374">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="993c5-375">HTTP/1.1 ve HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="993c5-375">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="993c5-376">HTTP/2, istemcinin TLS [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışmasında http/2 seçmesini gerektirir; Aksi takdirde, bağlantı varsayılan olarak HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="993c5-376">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="993c5-377">Herhangi bir uç nokta için varsayılan `ListenOptions.Protocols` değeri `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="993c5-377">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="993c5-378">HTTP/2 için TLS kısıtlamaları:</span><span class="sxs-lookup"><span data-stu-id="993c5-378">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="993c5-379">TLS sürüm 1,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="993c5-379">TLS version 1.2 or later</span></span>
* <span data-ttu-id="993c5-380">Yeniden anlaşma devre dışı</span><span class="sxs-lookup"><span data-stu-id="993c5-380">Renegotiation disabled</span></span>
* <span data-ttu-id="993c5-381">Sıkıştırma devre dışı</span><span class="sxs-lookup"><span data-stu-id="993c5-381">Compression disabled</span></span>
* <span data-ttu-id="993c5-382">En az kısa ömürlü anahtar değişim boyutları:</span><span class="sxs-lookup"><span data-stu-id="993c5-382">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="993c5-383">Eliptik Eğri Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bit minimum</span><span class="sxs-lookup"><span data-stu-id="993c5-383">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="993c5-384">Sınırlı alan Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bit minimum</span><span class="sxs-lookup"><span data-stu-id="993c5-384">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="993c5-385">Şifre paketi kara listede değil</span><span class="sxs-lookup"><span data-stu-id="993c5-385">Cipher suite not blacklisted</span></span>

<span data-ttu-id="993c5-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;&rbrack; `TLS-ECDHE`, P-256 eliptik eğrisi &lbrack;`FIPS186`&rbrack; varsayılan olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="993c5-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="993c5-387">Aşağıdaki örnek, 8000 numaralı bağlantı noktasında HTTP/1.1 ve HTTP/2 bağlantılarına izin verir.</span><span class="sxs-lookup"><span data-stu-id="993c5-387">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="993c5-388">Bağlantılar, sağlanan bir sertifikayla TLS ile güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="993c5-388">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="993c5-389">Gerektiğinde belirli şifrelemeler için TLS el sıkışmaları için bağlantı temelinde filtre uygulamak üzere bağlantı ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="993c5-389">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="993c5-390">Aşağıdaki örnek, uygulamanın desteklemediği tüm şifre algoritmaların <xref:System.NotSupportedException> oluşturur.</span><span class="sxs-lookup"><span data-stu-id="993c5-390">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="993c5-391">Alternatif olarak, [ılshandshakefeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) öğesini kabul edilebilir şifreleme paketleri listesi ile tanımlayın ve karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-391">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="993c5-392">[CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) şifre algoritması ile hiçbir şifreleme kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-392">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="993c5-393">Bağlantı filtrelemesi, bir <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda aracılığıyla da yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="993c5-393">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="993c5-394">Linux 'ta <xref:System.Net.Security.CipherSuitesPolicy>, TLS el sıkışmaları her bağlantı temelinde filtrelemek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="993c5-394">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="993c5-395">*Protokolü yapılandırmadan ayarla*</span><span class="sxs-lookup"><span data-stu-id="993c5-395">*Set the protocol from configuration*</span></span>

<span data-ttu-id="993c5-396">Kestrel yapılandırmasını yüklemek için varsayılan olarak `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` `CreateDefaultBuilder` çağırır.</span><span class="sxs-lookup"><span data-stu-id="993c5-396">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="993c5-397">Aşağıdaki *appSettings. JSON* örneği, tüm uç noktalar için varsayılan bağlantı protokolü olarak http/1.1 oluşturur:</span><span class="sxs-lookup"><span data-stu-id="993c5-397">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="993c5-398">Aşağıdaki *appSettings. JSON* örneği, belirli bir uç nokta için http/1.1 bağlantı protokolünü belirler:</span><span class="sxs-lookup"><span data-stu-id="993c5-398">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="993c5-399">Yapılandırma tarafından ayarlanan kod geçersiz kılma değerlerinde belirtilen protokoller.</span><span class="sxs-lookup"><span data-stu-id="993c5-399">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="993c5-400">Aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993c5-400">Transport configuration</span></span>

<span data-ttu-id="993c5-401">Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) kullanımını gerektiren projeler için:</span><span class="sxs-lookup"><span data-stu-id="993c5-401">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="993c5-402">Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="993c5-402">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="993c5-403">`IWebHostBuilder`<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> çağırın:</span><span class="sxs-lookup"><span data-stu-id="993c5-403">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

   ```csharp
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
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="993c5-404">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="993c5-404">URL prefixes</span></span>

<span data-ttu-id="993c5-405">`UseUrls`, `--urls` komut satırı bağımsız değişkenini, `urls` ana bilgisayar yapılandırma anahtarını veya `ASPNETCORE_URLS` ortam değişkenini kullanırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-405">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="993c5-406">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-406">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="993c5-407">Kestrel, `UseUrls`kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez.</span><span class="sxs-lookup"><span data-stu-id="993c5-407">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="993c5-408">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="993c5-408">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="993c5-409">`0.0.0.0`, tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="993c5-409">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="993c5-410">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="993c5-410">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="993c5-411">`[::]`, IPv4 `0.0.0.0`IPv6 eşdeğeridir.</span><span class="sxs-lookup"><span data-stu-id="993c5-411">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="993c5-412">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="993c5-412">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="993c5-413">Ana bilgisayar adları, `*`ve `+`özel değildir.</span><span class="sxs-lookup"><span data-stu-id="993c5-413">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="993c5-414">Geçerli bir IP adresi olarak tanınmayan bir şey veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-414">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="993c5-415">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [http. sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusu kullanın.</span><span class="sxs-lookup"><span data-stu-id="993c5-415">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="993c5-416">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="993c5-416">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="993c5-417">Port numarası veya bağlantı noktası numarası ile geri döngü IP 'si `localhost` ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="993c5-417">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="993c5-418">`localhost` belirtildiğinde, Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="993c5-418">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="993c5-419">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-419">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="993c5-420">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="993c5-420">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="993c5-421">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="993c5-421">Host filtering</span></span>

<span data-ttu-id="993c5-422">Kestrel, `http://example.com:5000`gibi önekleri temel alarak yapılandırmayı desteklese de, büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="993c5-422">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="993c5-423">Ana bilgisayar `localhost`, geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="993c5-423">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="993c5-424">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-424">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="993c5-425">`Host` üstbilgileri doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="993c5-425">`Host` headers aren't validated.</span></span>

<span data-ttu-id="993c5-426">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="993c5-426">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="993c5-427">Ana bilgisayar filtreleme ara yazılımı, ASP.NET Core uygulamaları için örtük olarak sunulan [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-427">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="993c5-428">Ara yazılım, <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>çağıran <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>tarafından eklenir:</span><span class="sxs-lookup"><span data-stu-id="993c5-428">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="993c5-429">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-429">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="993c5-430">Ara yazılımı etkinleştirmek için *appSettings. json*/appSettings içinde bir `AllowedHosts` anahtarı tanımlayın *.\<environmentname >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="993c5-430">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="993c5-431">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="993c5-431">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="993c5-432">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="993c5-432">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="993c5-433">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) ayrıca bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçeneği de vardır.</span><span class="sxs-lookup"><span data-stu-id="993c5-433">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="993c5-434">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="993c5-434">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="993c5-435">Iletilen üst bilgi ara sunucusu ile `AllowedHosts` ayarlama, istekleri bir ters ara sunucu veya yük dengeleyici ile iletirken, `Host` üst bilgisi korunurken uygun olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-435">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="993c5-436">Kestrel genel kullanıma açık bir uç sunucu olarak veya `Host` üstbilgisi doğrudan iletildiğinde, ana bilgisayar filtreleme ara yazılımı ile `AllowedHosts` ayarlama uygundur.</span><span class="sxs-lookup"><span data-stu-id="993c5-436">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="993c5-437">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="993c5-437">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="993c5-438">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="993c5-438">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="993c5-439">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="993c5-439">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="993c5-440">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="993c5-440">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="993c5-441">HTTPS</span><span class="sxs-lookup"><span data-stu-id="993c5-441">HTTPS</span></span>
* <span data-ttu-id="993c5-442">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="993c5-442">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="993c5-443">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="993c5-443">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="993c5-444">HTTP/2 (macOS&dagger;hariç)</span><span class="sxs-lookup"><span data-stu-id="993c5-444">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="993c5-445">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="993c5-445">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="993c5-446">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="993c5-446">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="993c5-447">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="993c5-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="993c5-448">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="993c5-448">HTTP/2 support</span></span>

<span data-ttu-id="993c5-449">Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="993c5-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="993c5-450">İşletim sistemi&dagger;</span><span class="sxs-lookup"><span data-stu-id="993c5-450">Operating system&dagger;</span></span>
  * <span data-ttu-id="993c5-451">Windows Server 2016/Windows 10 veya üzeri&Dagger;</span><span class="sxs-lookup"><span data-stu-id="993c5-451">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="993c5-452">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="993c5-452">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="993c5-453">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="993c5-453">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="993c5-454">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="993c5-454">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="993c5-455">TLS 1.2 veya sonraki bir bağlantı</span><span class="sxs-lookup"><span data-stu-id="993c5-455">TLS 1.2 or later connection</span></span>

<span data-ttu-id="993c5-456">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="993c5-456">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="993c5-457">&Dagger;Kestrel Windows Server 2012 R2 ve Windows 8.1 üzerinde HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="993c5-457">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="993c5-458">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-458">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="993c5-459">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-459">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="993c5-460">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="993c5-460">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="993c5-461">HTTP/2 varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-461">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="993c5-462">Yapılandırma hakkında daha fazla bilgi için [Kestrel Options](#kestrel-options) ve [Listenoptions. Protocols](#listenoptionsprotocols) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="993c5-462">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="993c5-463">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="993c5-463">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="993c5-464">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu*ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-464">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="993c5-465">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="993c5-465">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="993c5-466">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="993c5-466">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="993c5-468">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="993c5-468">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="993c5-470">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="993c5-470">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="993c5-471">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="993c5-471">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="993c5-472">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel `Host` ' den bağımsız olarak bu bağlantı noktası için tüm trafiği işler.</span><span class="sxs-lookup"><span data-stu-id="993c5-472">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="993c5-473">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="993c5-473">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="993c5-474">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-474">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="993c5-475">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="993c5-475">A reverse proxy:</span></span>

* <span data-ttu-id="993c5-476">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-476">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="993c5-477">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-477">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="993c5-478">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-478">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="993c5-479">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-479">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="993c5-480">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-480">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="993c5-481">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="993c5-481">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="993c5-482">ASP.NET Core uygulamalarında Kestrel kullanma</span><span class="sxs-lookup"><span data-stu-id="993c5-482">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="993c5-483">Microsoft. [AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="993c5-483">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="993c5-484">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-484">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="993c5-485">*Program.cs*' de, şablon kodu, arka planda <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> çağıran <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>çağırır.</span><span class="sxs-lookup"><span data-stu-id="993c5-485">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="993c5-486">`CreateDefaultBuilder` ve konak oluşturma hakkında daha fazla bilgi için, <xref:fundamentals/host/web-host#set-up-a-host>*bir konak ayarlama* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="993c5-486">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="993c5-487">`CreateDefaultBuilder`çağrıldıktan sonra ek yapılandırma sağlamak için `ConfigureKestrel`kullanın:</span><span class="sxs-lookup"><span data-stu-id="993c5-487">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="993c5-488">Uygulama, Konağı kurmak için `CreateDefaultBuilder` çağırmazsa, `ConfigureKestrel`çağrılmadan **önce** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> çağırın:</span><span class="sxs-lookup"><span data-stu-id="993c5-488">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="993c5-489">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="993c5-489">Kestrel options</span></span>

<span data-ttu-id="993c5-490">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="993c5-490">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="993c5-491"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> sınıfının <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> özelliğindeki kısıtlamaları ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-491">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="993c5-492">`Limits` özelliği <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> sınıfının bir örneğini barındırır.</span><span class="sxs-lookup"><span data-stu-id="993c5-492">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="993c5-493">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="993c5-493">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="993c5-494">Aşağıdaki örneklerde C# kodda yapılandırılan Kestrel seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-494">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="993c5-495">Örneğin, dosya yapılandırma sağlayıcısı bir *appSettings. JSON* veya appSettings 'ten Kestrel yapılandırmasını yükleyebilir *. { Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="993c5-495">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="993c5-496">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="993c5-496">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="993c5-497">`Startup.ConfigureServices`'de Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="993c5-497">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="993c5-498">`Startup` sınıfına bir `IConfiguration` örneği ekleyin.</span><span class="sxs-lookup"><span data-stu-id="993c5-498">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="993c5-499">Aşağıdaki örnek, eklenen yapılandırmanın `Configuration` özelliğine atandığını varsayar.</span><span class="sxs-lookup"><span data-stu-id="993c5-499">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="993c5-500">`Startup.ConfigureServices`, yapılandırmanın `Kestrel` bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="993c5-500">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="993c5-501">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="993c5-501">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="993c5-502">*Program.cs*' de, yapılandırmanın `Kestrel` bölümünü Kestrel yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="993c5-502">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="993c5-503">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="993c5-503">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="993c5-504">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="993c5-504">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="993c5-505">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="993c5-505">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="993c5-506">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="993c5-506">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="993c5-507">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="993c5-507">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="993c5-508">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="993c5-508">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="993c5-509">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="993c5-509">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="993c5-510">Bir bağlantı yükseltildikten sonra, `MaxConcurrentConnections` sınırına göre sayılmaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-510">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="993c5-511">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="993c5-511">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="993c5-512">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="993c5-512">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="993c5-513">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="993c5-513">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="993c5-514">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, bir eylem yönteminde <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="993c5-514">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="993c5-515">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="993c5-515">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="993c5-516">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="993c5-516">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="993c5-517">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="993c5-517">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="993c5-518">`MaxRequestBodySize` özelliğinin salt okuma durumunda olup olmadığını belirten `IsReadOnly` bir özellik vardır. Bu, sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="993c5-518">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="993c5-519">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-519">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="993c5-520">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="993c5-520">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="993c5-521">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="993c5-521">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="993c5-522">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="993c5-522">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="993c5-523">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-523">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="993c5-524">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-524">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="993c5-525">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-525">A minimum rate also applies to the response.</span></span> <span data-ttu-id="993c5-526">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, özellik ve arabirim adlarında `RequestBody` veya `Response` sahip olma dışında aynıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-526">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="993c5-527">*Program.cs*içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="993c5-527">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="993c5-528">Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="993c5-528">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="993c5-529">İstek çoğullama için protokol desteğinin desteklenmediği için, önceki örnekte başvurulan oran özelliği http/2 istekleri için `HttpContext.Features`, HTTP/2 için de hız sınırlarını değiştirmek için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="993c5-529">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="993c5-530">`KestrelServerOptions.Limits` aracılığıyla yapılandırılan sunucu genelindeki hız sınırları hem HTTP/1. x hem de HTTP/2 bağlantıları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-530">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="993c5-531">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="993c5-531">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="993c5-532">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="993c5-532">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="993c5-533">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="993c5-533">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="993c5-534">Bağlantı başına en fazla akış</span><span class="sxs-lookup"><span data-stu-id="993c5-534">Maximum streams per connection</span></span>

<span data-ttu-id="993c5-535">`Http2.MaxStreamsPerConnection`, HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="993c5-535">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="993c5-536">Fazlalık akışlar reddedildi.</span><span class="sxs-lookup"><span data-stu-id="993c5-536">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="993c5-537">Varsayılan değer 100’dür.</span><span class="sxs-lookup"><span data-stu-id="993c5-537">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="993c5-538">Üst bilgi tablosu boyutu</span><span class="sxs-lookup"><span data-stu-id="993c5-538">Header table size</span></span>

<span data-ttu-id="993c5-539">HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar.</span><span class="sxs-lookup"><span data-stu-id="993c5-539">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="993c5-540">`Http2.HeaderTableSize`, HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlar.</span><span class="sxs-lookup"><span data-stu-id="993c5-540">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="993c5-541">Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="993c5-541">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="993c5-542">Varsayılan değer 4096 ' dir.</span><span class="sxs-lookup"><span data-stu-id="993c5-542">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="993c5-543">En büyük çerçeve boyutu</span><span class="sxs-lookup"><span data-stu-id="993c5-543">Maximum frame size</span></span>

<span data-ttu-id="993c5-544">`Http2.MaxFrameSize`, alacak HTTP/2 bağlantı çerçevesi yükünün en büyük boyutunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="993c5-544">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="993c5-545">Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-545">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="993c5-546">Varsayılan değer 2 ^ 14 ' dir (16.384).</span><span class="sxs-lookup"><span data-stu-id="993c5-546">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="993c5-547">En fazla istek üst bilgi boyutu</span><span class="sxs-lookup"><span data-stu-id="993c5-547">Maximum request header size</span></span>

<span data-ttu-id="993c5-548">`Http2.MaxRequestHeaderFieldSize`, istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu gösterir.</span><span class="sxs-lookup"><span data-stu-id="993c5-548">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="993c5-549">Bu sınır, hem sıkıştırılmış hem de sıkıştırılmamış temsillerinde birlikte hem ad hem de değer için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-549">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="993c5-550">Değer sıfırdan büyük (0) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-550">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="993c5-551">Varsayılan değer 8.192 ' dir.</span><span class="sxs-lookup"><span data-stu-id="993c5-551">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="993c5-552">İlk bağlantı pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="993c5-552">Initial connection window size</span></span>

<span data-ttu-id="993c5-553">`Http2.InitialConnectionWindowSize`, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="993c5-553">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="993c5-554">İstekler aynı zamanda `Http2.InitialStreamWindowSize`ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-554">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="993c5-555">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-555">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="993c5-556">Varsayılan değer 128 KB 'tır (131.072).</span><span class="sxs-lookup"><span data-stu-id="993c5-556">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="993c5-557">İlk akış pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="993c5-557">Initial stream window size</span></span>

<span data-ttu-id="993c5-558">`Http2.InitialStreamWindowSize`, istek (Stream) başına bir seferde sunucu arabelleklerinin bayt cinsinden en yüksek istek gövde verilerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="993c5-558">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="993c5-559">İstekler aynı zamanda `Http2.InitialStreamWindowSize`ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-559">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="993c5-560">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-560">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="993c5-561">Varsayılan değer 96 KB 'tır (98.304).</span><span class="sxs-lookup"><span data-stu-id="993c5-561">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="993c5-562">Zaman uyumlu GÇ</span><span class="sxs-lookup"><span data-stu-id="993c5-562">Synchronous IO</span></span>

<span data-ttu-id="993c5-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>, istek ve yanıt için zaman uyumlu GÇ 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="993c5-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="993c5-564">Varsayılan değer `true`.</span><span class="sxs-lookup"><span data-stu-id="993c5-564">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="993c5-565">Çok sayıda engelleme zaman uyumlu GÇ işlemi, iş parçacığı havuzuna neden olabilir ve bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-565">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="993c5-566">Yalnızca zaman uyumsuz GÇ desteklemeyen bir kitaplık kullanırken `AllowSynchronousIO` etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="993c5-566">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="993c5-567">Aşağıdaki örnek, zaman uyumlu GÇ 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="993c5-567">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="993c5-568">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="993c5-568">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="993c5-569">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993c5-569">Endpoint configuration</span></span>

<span data-ttu-id="993c5-570">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="993c5-570">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="993c5-571">`https://localhost:5001` (yerel bir geliştirme sertifikası mevcut olduğunda)</span><span class="sxs-lookup"><span data-stu-id="993c5-571">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="993c5-572">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="993c5-572">Specify URLs using the:</span></span>

* <span data-ttu-id="993c5-573">ortam değişkeni `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="993c5-573">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="993c5-574">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="993c5-574">`--urls` command-line argument.</span></span>
* <span data-ttu-id="993c5-575">`urls` ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="993c5-575">`urls` host configuration key.</span></span>
* <span data-ttu-id="993c5-576">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="993c5-576">`UseUrls` extension method.</span></span>

<span data-ttu-id="993c5-577">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="993c5-577">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="993c5-578">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="993c5-578">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="993c5-579">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="993c5-579">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="993c5-580">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="993c5-580">A development certificate is created:</span></span>

* <span data-ttu-id="993c5-581">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="993c5-581">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="993c5-582">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-582">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="993c5-583">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="993c5-583">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="993c5-584">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="993c5-584">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="993c5-585">Kestrel için URL öneklerini ve bağlantı noktalarını yapılandırmak üzere <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> veya <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> yöntemlerini çağırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-585">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="993c5-586">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (HTTPS uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="993c5-586">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="993c5-587">`KestrelServerOptions` yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="993c5-587">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="993c5-588">ConfigureEndpointDefaults Varsayılanları (eylem\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="993c5-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="993c5-589">Belirtilen her bitiş noktası için çalıştırılacak bir yapılandırma `Action` belirtir.</span><span class="sxs-lookup"><span data-stu-id="993c5-589">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="993c5-590">`ConfigureEndpointDefaults` birden çok kez çağrılması, önceki `Action`s 'in belirtilen son `Action` yerini alır.</span><span class="sxs-lookup"><span data-stu-id="993c5-590">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="993c5-591"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> çağrılmadan oluşturulan bitiş noktaları, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> çağrılmadan **önce** , varsayılan olarak uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-591">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="993c5-592">ConfigureHttpsDefaults (eylem\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="993c5-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="993c5-593">Her HTTPS uç noktası için çalıştırılacak bir yapılandırma `Action` belirtir.</span><span class="sxs-lookup"><span data-stu-id="993c5-593">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="993c5-594">`ConfigureHttpsDefaults` birden çok kez çağrılması, önceki `Action`s 'in belirtilen son `Action` yerini alır.</span><span class="sxs-lookup"><span data-stu-id="993c5-594">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="993c5-595"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> çağrılmadan oluşturulan bitiş noktaları, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> çağrılmadan **önce** , varsayılan olarak uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-595">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="993c5-596">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="993c5-596">Configure(IConfiguration)</span></span>

<span data-ttu-id="993c5-597">Giriş olarak bir <xref:Microsoft.Extensions.Configuration.IConfiguration> alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="993c5-597">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="993c5-598">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-598">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="993c5-599">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="993c5-599">ListenOptions.UseHttps</span></span>

<span data-ttu-id="993c5-600">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-600">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="993c5-601">`ListenOptions.UseHttps` uzantıları:</span><span class="sxs-lookup"><span data-stu-id="993c5-601">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="993c5-602">`UseHttps` &ndash; varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-602">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="993c5-603">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="993c5-603">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="993c5-604">`ListenOptions.UseHttps` parametreleri:</span><span class="sxs-lookup"><span data-stu-id="993c5-604">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="993c5-605">`filename`, uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-605">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="993c5-606">X. 509.440 sertifika verilerine erişmek için gereken parola `password`.</span><span class="sxs-lookup"><span data-stu-id="993c5-606">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="993c5-607">`configureOptions`, `HttpsConnectionAdapterOptions`yapılandırmak için `Action`.</span><span class="sxs-lookup"><span data-stu-id="993c5-607">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="993c5-608">`ListenOptions`döndürür.</span><span class="sxs-lookup"><span data-stu-id="993c5-608">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="993c5-609">`storeName`, sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="993c5-609">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="993c5-610">`subject`, sertifikanın konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-610">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="993c5-611">`allowInvalid`, otomatik olarak imzalanan sertifikalar gibi geçersiz sertifikaların dikkate alınıp alınmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="993c5-611">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="993c5-612">`location`, sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="993c5-612">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="993c5-613">`serverCertificate` X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-613">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="993c5-614">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="993c5-614">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="993c5-615">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-615">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="993c5-616">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="993c5-616">Supported configurations described next:</span></span>

* <span data-ttu-id="993c5-617">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="993c5-617">No configuration</span></span>
* <span data-ttu-id="993c5-618">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="993c5-618">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="993c5-619">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="993c5-619">Change the defaults in code</span></span>

<span data-ttu-id="993c5-620">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="993c5-620">*No configuration*</span></span>

<span data-ttu-id="993c5-621">Kestrel `http://localhost:5000` ve `https://localhost:5001` dinler (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="993c5-621">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="993c5-622">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="993c5-622">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="993c5-623">Kestrel yapılandırmasını yüklemek için varsayılan olarak `Configure(context.Configuration.GetSection("Kestrel"))` `CreateDefaultBuilder` çağırır.</span><span class="sxs-lookup"><span data-stu-id="993c5-623">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="993c5-624">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-624">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="993c5-625">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-625">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="993c5-626">Aşağıdaki *appSettings. JSON* örneğinde:</span><span class="sxs-lookup"><span data-stu-id="993c5-626">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="993c5-627">Geçersiz sertifikaların (örneğin, otomatik olarak imzalanan sertifikalar) kullanılmasına izin vermek için, **Allowwınvalid** öğesini `true` olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-627">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="993c5-628">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan**Httpsdefaultcert** ), **varsayılan** veya geliştirme sertifikası > **Sertifikalar** altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="993c5-628">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="993c5-629">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="993c5-629">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="993c5-630">Örneğin, **sertifikalar** > **varsayılan** sertifika şöyle belirlenebilir:</span><span class="sxs-lookup"><span data-stu-id="993c5-630">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="993c5-631">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="993c5-631">Schema notes:</span></span>

* <span data-ttu-id="993c5-632">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-632">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="993c5-633">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-633">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="993c5-634">Her uç nokta için `Url` parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-634">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="993c5-635">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-635">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="993c5-636">Bu uç noktalar, üst düzey `Urls` yapılandırmasında tanımlananlara eklemek yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="993c5-636">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="993c5-637">`Listen` aracılığıyla kodda tanımlanan uç noktalar yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-637">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="993c5-638">`Certificate` bölümü isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-638">The `Certificate` section is optional.</span></span> <span data-ttu-id="993c5-639">`Certificate` bölümü belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-639">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="993c5-640">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-640">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="993c5-641">`Certificate` bölümü, hem **yol**&ndash;**parolasını** hem de **Konu**&ndash;**Mağaza** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="993c5-641">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="993c5-642">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-642">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="993c5-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))`, yapılandırılmış bir uç noktanın ayarlarını tamamlamak için kullanılabilecek `.Endpoint(string name, listenOptions => { })` yöntemi ile bir `KestrelConfigurationLoader` döndürür:</span><span class="sxs-lookup"><span data-stu-id="993c5-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="993c5-644">`KestrelServerOptions.ConfigurationLoader`, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>tarafından sağlana gibi var olan yükleyicisindeki yinelenmeye devam etmek için doğrudan erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-644">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="993c5-645">Her uç noktanın yapılandırma bölümü, özel ayarların okunabilmesi için `Endpoint` yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-645">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="993c5-646">`options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak birden çok yapılandırma yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-646">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="993c5-647">`Load` önceki örneklerde açıkça çağrılmamışsa yalnızca son yapılandırma kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-647">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="993c5-648">Metapackage, varsayılan yapılandırma bölümünün değiştirilebilmesi için `Load` çağırmaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-648">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="993c5-649">`KestrelConfigurationLoader`, API 'lerin `Listen` ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-649">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="993c5-650">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-650">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="993c5-651">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="993c5-651">*Change the defaults in code*</span></span>

<span data-ttu-id="993c5-652">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults`, önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma da dahil olmak üzere `ListenOptions` ve `HttpsConnectionAdapterOptions`için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-652">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="993c5-653">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-653">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="993c5-654">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="993c5-654">*Kestrel support for SNI*</span></span>

<span data-ttu-id="993c5-655">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-655">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="993c5-656">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="993c5-656">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="993c5-657">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-657">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="993c5-658">Kestrel `ServerCertificateSelector` geri çağırma aracılığıyla SNı destekler.</span><span class="sxs-lookup"><span data-stu-id="993c5-658">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="993c5-659">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-659">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="993c5-660">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="993c5-660">SNI support requires:</span></span>

* <span data-ttu-id="993c5-661">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="993c5-661">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="993c5-662">`net461` veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman `null`.</span><span class="sxs-lookup"><span data-stu-id="993c5-662">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="993c5-663">Ayrıca, istemci, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa da `null` `name`.</span><span class="sxs-lookup"><span data-stu-id="993c5-663">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="993c5-664">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="993c5-664">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="993c5-665">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="993c5-665">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="993c5-666">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="993c5-666">Connection logging</span></span>

<span data-ttu-id="993c5-667">Bir bağlantıda bayt düzeyinde iletişim için hata ayıklama düzeyi günlüklerini yayma <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> çağırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-667">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="993c5-668">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-668">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="993c5-669">`UseConnectionLogging` `UseHttps`önce yerleştirilmişse, şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-669">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="993c5-670">`UseConnectionLogging` `UseHttps`sonra yerleştirilmişse, şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-670">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="993c5-671">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="993c5-671">Bind to a TCP socket</span></span>

<span data-ttu-id="993c5-672"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> yöntemi bir TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="993c5-672">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="993c5-673">Örnek, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>olan bir uç nokta için HTTPS 'yi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="993c5-673">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="993c5-674">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="993c5-674">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="993c5-675">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="993c5-675">Bind to a Unix socket</span></span>

<span data-ttu-id="993c5-676">Aşağıdaki örnekte gösterildiği gibi NGINX ile daha iyi performans için <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="993c5-676">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="993c5-677">NGINX confiuguration dosyasında, `server` > `location` > `proxy_pass` girişi `http://unix:/tmp/{KESTREL SOCKET}:/;`olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-677">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="993c5-678">`{KESTREL SOCKET}`, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> için belirtilen yuvanın adıdır (örneğin, önceki örnekte `kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="993c5-678">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="993c5-679">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="993c5-679">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="993c5-680">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="993c5-680">Port 0</span></span>

<span data-ttu-id="993c5-681">`0` bağlantı noktası numarası belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-681">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="993c5-682">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="993c5-682">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="993c5-683">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="993c5-683">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="993c5-684">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="993c5-684">Limitations</span></span>

<span data-ttu-id="993c5-685">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="993c5-685">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="993c5-686">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="993c5-686">`--urls` command-line argument</span></span>
* <span data-ttu-id="993c5-687">`urls` ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="993c5-687">`urls` host configuration key</span></span>
* <span data-ttu-id="993c5-688">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="993c5-688">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="993c5-689">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-689">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="993c5-690">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="993c5-690">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="993c5-691">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece (örneğin, bu konuda daha önce gösterildiği gibi `KestrelServerOptions` yapılandırması veya yapılandırma dosyası kullanılarak), HTTPS bu yaklaşımlar ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-691">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="993c5-692">`Listen` ve `UseUrls` yaklaşımlarının her ikisi de aynı anda kullanıldığında `Listen` uç noktaları `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="993c5-692">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="993c5-693">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993c5-693">IIS endpoint configuration</span></span>

<span data-ttu-id="993c5-694">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları `Listen` veya `UseUrls`tarafından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-694">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="993c5-695">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="993c5-695">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="993c5-696">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="993c5-696">ListenOptions.Protocols</span></span>

<span data-ttu-id="993c5-697">`Protocols` özelliği, bir bağlantı uç noktasında veya sunucu için etkin HTTP protokollerini (`HttpProtocols`) belirler.</span><span class="sxs-lookup"><span data-stu-id="993c5-697">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="993c5-698">`HttpProtocols` numaralandırmasından `Protocols` özelliğine bir değer atayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-698">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="993c5-699">`HttpProtocols` Enum değeri</span><span class="sxs-lookup"><span data-stu-id="993c5-699">`HttpProtocols` enum value</span></span> | <span data-ttu-id="993c5-700">Bağlantı protokolü izin verildi</span><span class="sxs-lookup"><span data-stu-id="993c5-700">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="993c5-701">Yalnızca HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="993c5-701">HTTP/1.1 only.</span></span> <span data-ttu-id="993c5-702">, TLS olmadan veya ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-702">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="993c5-703">Yalnızca HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="993c5-703">HTTP/2 only.</span></span> <span data-ttu-id="993c5-704">Yalnızca istemci [önceki bir bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)DESTEKLIYORSA, TLS olmadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-704">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="993c5-705">HTTP/1.1 ve HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="993c5-705">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="993c5-706">HTTP/2 bir TLS ve [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı gerektirir; Aksi takdirde, bağlantı varsayılan olarak HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="993c5-706">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="993c5-707">Varsayılan protokol HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="993c5-707">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="993c5-708">HTTP/2 için TLS kısıtlamaları:</span><span class="sxs-lookup"><span data-stu-id="993c5-708">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="993c5-709">TLS sürüm 1,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="993c5-709">TLS version 1.2 or later</span></span>
* <span data-ttu-id="993c5-710">Yeniden anlaşma devre dışı</span><span class="sxs-lookup"><span data-stu-id="993c5-710">Renegotiation disabled</span></span>
* <span data-ttu-id="993c5-711">Sıkıştırma devre dışı</span><span class="sxs-lookup"><span data-stu-id="993c5-711">Compression disabled</span></span>
* <span data-ttu-id="993c5-712">En az kısa ömürlü anahtar değişim boyutları:</span><span class="sxs-lookup"><span data-stu-id="993c5-712">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="993c5-713">Eliptik Eğri Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bit minimum</span><span class="sxs-lookup"><span data-stu-id="993c5-713">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="993c5-714">Sınırlı alan Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bit minimum</span><span class="sxs-lookup"><span data-stu-id="993c5-714">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="993c5-715">Şifre paketi kara listede değil</span><span class="sxs-lookup"><span data-stu-id="993c5-715">Cipher suite not blacklisted</span></span>

<span data-ttu-id="993c5-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;&rbrack; `TLS-ECDHE`, P-256 eliptik eğrisi &lbrack;`FIPS186`&rbrack; varsayılan olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="993c5-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="993c5-717">Aşağıdaki örnek, 8000 numaralı bağlantı noktasında HTTP/1.1 ve HTTP/2 bağlantılarına izin verir.</span><span class="sxs-lookup"><span data-stu-id="993c5-717">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="993c5-718">Bağlantılar, sağlanan bir sertifikayla TLS ile güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="993c5-718">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="993c5-719">İsteğe bağlı olarak, belirli şifrelemeler için bağlantı başına TLS el sıkışmaları filtrelemek üzere `IConnectionAdapter` bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="993c5-719">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="993c5-720">*Protokolü yapılandırmadan ayarla*</span><span class="sxs-lookup"><span data-stu-id="993c5-720">*Set the protocol from configuration*</span></span>

<span data-ttu-id="993c5-721">Kestrel yapılandırmasını yüklemek için varsayılan olarak `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> çağırır.</span><span class="sxs-lookup"><span data-stu-id="993c5-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="993c5-722">Aşağıdaki *appSettings. JSON* örneğinde, tüm Kestrel uç noktaları için varsayılan bir bağlantı protokolü (http/1.1 ve http/2) oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="993c5-722">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="993c5-723">Aşağıdaki yapılandırma dosyası örneği, belirli bir uç nokta için bağlantı protokolü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="993c5-723">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="993c5-724">Yapılandırma tarafından ayarlanan kod geçersiz kılma değerlerinde belirtilen protokoller.</span><span class="sxs-lookup"><span data-stu-id="993c5-724">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="993c5-725">Aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993c5-725">Transport configuration</span></span>

<span data-ttu-id="993c5-726">ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır.</span><span class="sxs-lookup"><span data-stu-id="993c5-726">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="993c5-727">Bu, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> çağıran ve aşağıdaki paketlerden birine bağlı olan 2,1 ' ye yükselten ASP.NET Core 2,0 uygulamaları için bir son değişiklik değildir:</span><span class="sxs-lookup"><span data-stu-id="993c5-727">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="993c5-728">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)</span><span class="sxs-lookup"><span data-stu-id="993c5-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="993c5-729">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="993c5-729">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="993c5-730">Libuv kullanımını gerektiren projeler için:</span><span class="sxs-lookup"><span data-stu-id="993c5-730">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="993c5-731">Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="993c5-731">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="993c5-732"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>çağrısı:</span><span class="sxs-lookup"><span data-stu-id="993c5-732">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="993c5-733">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="993c5-733">URL prefixes</span></span>

<span data-ttu-id="993c5-734">`UseUrls`, `--urls` komut satırı bağımsız değişkenini, `urls` ana bilgisayar yapılandırma anahtarını veya `ASPNETCORE_URLS` ortam değişkenini kullanırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-734">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="993c5-735">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-735">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="993c5-736">Kestrel, `UseUrls`kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez.</span><span class="sxs-lookup"><span data-stu-id="993c5-736">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="993c5-737">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="993c5-737">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="993c5-738">`0.0.0.0`, tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="993c5-738">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="993c5-739">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="993c5-739">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="993c5-740">`[::]`, IPv4 `0.0.0.0`IPv6 eşdeğeridir.</span><span class="sxs-lookup"><span data-stu-id="993c5-740">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="993c5-741">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="993c5-741">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="993c5-742">Ana bilgisayar adları, `*`ve `+`özel değildir.</span><span class="sxs-lookup"><span data-stu-id="993c5-742">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="993c5-743">Geçerli bir IP adresi olarak tanınmayan bir şey veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-743">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="993c5-744">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [http. sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusu kullanın.</span><span class="sxs-lookup"><span data-stu-id="993c5-744">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="993c5-745">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="993c5-745">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="993c5-746">Port numarası veya bağlantı noktası numarası ile geri döngü IP 'si `localhost` ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="993c5-746">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="993c5-747">`localhost` belirtildiğinde, Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="993c5-747">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="993c5-748">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-748">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="993c5-749">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="993c5-749">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="993c5-750">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="993c5-750">Host filtering</span></span>

<span data-ttu-id="993c5-751">Kestrel, `http://example.com:5000`gibi önekleri temel alarak yapılandırmayı desteklese de, büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="993c5-751">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="993c5-752">Ana bilgisayar `localhost`, geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="993c5-752">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="993c5-753">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-753">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="993c5-754">`Host` üstbilgileri doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="993c5-754">`Host` headers aren't validated.</span></span>

<span data-ttu-id="993c5-755">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="993c5-755">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="993c5-756">Ana bilgisayar filtreleme ara yazılımı, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya 2,2) ' de yer alan [Microsoft. Aspnetcore. hostfiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-756">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="993c5-757">Ara yazılım, <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>çağıran <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>tarafından eklenir:</span><span class="sxs-lookup"><span data-stu-id="993c5-757">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="993c5-758">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-758">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="993c5-759">Ara yazılımı etkinleştirmek için *appSettings. json*/appSettings içinde bir `AllowedHosts` anahtarı tanımlayın *.\<environmentname >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="993c5-759">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="993c5-760">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="993c5-760">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="993c5-761">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="993c5-761">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="993c5-762">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) ayrıca bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçeneği de vardır.</span><span class="sxs-lookup"><span data-stu-id="993c5-762">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="993c5-763">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="993c5-763">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="993c5-764">Iletilen üst bilgi ara sunucusu ile `AllowedHosts` ayarlama, istekleri bir ters ara sunucu veya yük dengeleyici ile iletirken, `Host` üst bilgisi korunurken uygun olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-764">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="993c5-765">Kestrel genel kullanıma açık bir uç sunucu olarak veya `Host` üstbilgisi doğrudan iletildiğinde, ana bilgisayar filtreleme ara yazılımı ile `AllowedHosts` ayarlama uygundur.</span><span class="sxs-lookup"><span data-stu-id="993c5-765">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="993c5-766">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="993c5-766">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="993c5-767">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="993c5-767">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="993c5-768">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="993c5-768">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="993c5-769">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="993c5-769">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="993c5-770">HTTPS</span><span class="sxs-lookup"><span data-stu-id="993c5-770">HTTPS</span></span>
* <span data-ttu-id="993c5-771">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="993c5-771">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="993c5-772">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="993c5-772">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="993c5-773">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="993c5-773">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="993c5-774">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="993c5-774">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="993c5-775">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="993c5-775">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="993c5-776">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu*ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-776">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="993c5-777">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="993c5-777">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="993c5-778">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="993c5-778">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="993c5-780">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="993c5-780">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="993c5-782">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="993c5-782">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="993c5-783">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="993c5-783">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="993c5-784">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel `Host` ' den bağımsız olarak bu bağlantı noktası için tüm trafiği işler.</span><span class="sxs-lookup"><span data-stu-id="993c5-784">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="993c5-785">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="993c5-785">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="993c5-786">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-786">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="993c5-787">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="993c5-787">A reverse proxy:</span></span>

* <span data-ttu-id="993c5-788">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-788">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="993c5-789">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-789">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="993c5-790">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-790">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="993c5-791">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-791">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="993c5-792">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-792">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="993c5-793">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="993c5-793">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="993c5-794">ASP.NET Core uygulamalarında Kestrel kullanma</span><span class="sxs-lookup"><span data-stu-id="993c5-794">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="993c5-795">Microsoft. [AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="993c5-795">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="993c5-796">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-796">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="993c5-797">*Program.cs*' de, şablon kodu, arka planda <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> çağıran <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>çağırır.</span><span class="sxs-lookup"><span data-stu-id="993c5-797">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="993c5-798">`CreateDefaultBuilder`çağrıldıktan sonra ek yapılandırma sağlamak için, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>çağırın:</span><span class="sxs-lookup"><span data-stu-id="993c5-798">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="993c5-799">`CreateDefaultBuilder` ve konak oluşturma hakkında daha fazla bilgi için, <xref:fundamentals/host/web-host#set-up-a-host>*bir konak ayarlama* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="993c5-799">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="993c5-800">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="993c5-800">Kestrel options</span></span>

<span data-ttu-id="993c5-801">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="993c5-801">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="993c5-802"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> sınıfının <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> özelliğindeki kısıtlamaları ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-802">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="993c5-803">`Limits` özelliği <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> sınıfının bir örneğini barındırır.</span><span class="sxs-lookup"><span data-stu-id="993c5-803">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="993c5-804">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="993c5-804">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="993c5-805">Aşağıdaki örneklerde C# kodda yapılandırılan Kestrel seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-805">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="993c5-806">Örneğin, dosya yapılandırma sağlayıcısı bir *appSettings. JSON* veya appSettings 'ten Kestrel yapılandırmasını yükleyebilir *. { Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="993c5-806">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="993c5-807">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="993c5-807">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="993c5-808">`Startup.ConfigureServices`'de Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="993c5-808">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="993c5-809">`Startup` sınıfına bir `IConfiguration` örneği ekleyin.</span><span class="sxs-lookup"><span data-stu-id="993c5-809">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="993c5-810">Aşağıdaki örnek, eklenen yapılandırmanın `Configuration` özelliğine atandığını varsayar.</span><span class="sxs-lookup"><span data-stu-id="993c5-810">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="993c5-811">`Startup.ConfigureServices`, yapılandırmanın `Kestrel` bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="993c5-811">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="993c5-812">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="993c5-812">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="993c5-813">*Program.cs*' de, yapılandırmanın `Kestrel` bölümünü Kestrel yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="993c5-813">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="993c5-814">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="993c5-814">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="993c5-815">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="993c5-815">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="993c5-816">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="993c5-816">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="993c5-817">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="993c5-817">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="993c5-818">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="993c5-818">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="993c5-819">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="993c5-819">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="993c5-820">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="993c5-820">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="993c5-821">Bir bağlantı yükseltildikten sonra, `MaxConcurrentConnections` sınırına göre sayılmaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-821">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="993c5-822">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="993c5-822">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="993c5-823">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="993c5-823">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="993c5-824">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="993c5-824">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="993c5-825">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, bir eylem yönteminde <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="993c5-825">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="993c5-826">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="993c5-826">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="993c5-827">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="993c5-827">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="993c5-828">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="993c5-828">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="993c5-829">`MaxRequestBodySize` özelliğinin salt okuma durumunda olup olmadığını belirten `IsReadOnly` bir özellik vardır. Bu, sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="993c5-829">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="993c5-830">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-830">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="993c5-831">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="993c5-831">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="993c5-832">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="993c5-832">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="993c5-833">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="993c5-833">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="993c5-834">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-834">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="993c5-835">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-835">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="993c5-836">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-836">A minimum rate also applies to the response.</span></span> <span data-ttu-id="993c5-837">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, özellik ve arabirim adlarında `RequestBody` veya `Response` sahip olma dışında aynıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-837">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="993c5-838">*Program.cs*içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="993c5-838">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="993c5-839">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="993c5-839">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="993c5-840">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="993c5-840">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="993c5-841">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="993c5-841">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="993c5-842">Zaman uyumlu GÇ</span><span class="sxs-lookup"><span data-stu-id="993c5-842">Synchronous IO</span></span>

<span data-ttu-id="993c5-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>, istek ve yanıt için zaman uyumlu GÇ 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="993c5-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="993c5-844">Varsayılan değer `true`.</span><span class="sxs-lookup"><span data-stu-id="993c5-844">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="993c5-845">Çok sayıda engelleme zaman uyumlu GÇ işlemi, iş parçacığı havuzuna neden olabilir ve bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-845">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="993c5-846">Yalnızca zaman uyumsuz GÇ desteklemeyen bir kitaplık kullanırken `AllowSynchronousIO` etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="993c5-846">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="993c5-847">Aşağıdaki örnek, zaman uyumlu GÇ 'yi devre dışı bırakır:</span><span class="sxs-lookup"><span data-stu-id="993c5-847">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="993c5-848">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="993c5-848">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="993c5-849">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993c5-849">Endpoint configuration</span></span>

<span data-ttu-id="993c5-850">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="993c5-850">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="993c5-851">`https://localhost:5001` (yerel bir geliştirme sertifikası mevcut olduğunda)</span><span class="sxs-lookup"><span data-stu-id="993c5-851">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="993c5-852">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="993c5-852">Specify URLs using the:</span></span>

* <span data-ttu-id="993c5-853">ortam değişkeni `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="993c5-853">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="993c5-854">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="993c5-854">`--urls` command-line argument.</span></span>
* <span data-ttu-id="993c5-855">`urls` ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="993c5-855">`urls` host configuration key.</span></span>
* <span data-ttu-id="993c5-856">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="993c5-856">`UseUrls` extension method.</span></span>

<span data-ttu-id="993c5-857">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="993c5-857">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="993c5-858">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="993c5-858">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="993c5-859">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="993c5-859">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="993c5-860">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="993c5-860">A development certificate is created:</span></span>

* <span data-ttu-id="993c5-861">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="993c5-861">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="993c5-862">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-862">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="993c5-863">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="993c5-863">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="993c5-864">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="993c5-864">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="993c5-865">Kestrel için URL öneklerini ve bağlantı noktalarını yapılandırmak üzere <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> veya <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> yöntemlerini çağırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-865">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="993c5-866">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (HTTPS uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="993c5-866">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="993c5-867">`KestrelServerOptions` yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="993c5-867">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="993c5-868">ConfigureEndpointDefaults Varsayılanları (eylem\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="993c5-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="993c5-869">Belirtilen her bitiş noktası için çalıştırılacak bir yapılandırma `Action` belirtir.</span><span class="sxs-lookup"><span data-stu-id="993c5-869">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="993c5-870">`ConfigureEndpointDefaults` birden çok kez çağrılması, önceki `Action`s 'in belirtilen son `Action` yerini alır.</span><span class="sxs-lookup"><span data-stu-id="993c5-870">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="993c5-871"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> çağrılmadan oluşturulan bitiş noktaları, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> çağrılmadan **önce** , varsayılan olarak uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-871">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="993c5-872">ConfigureHttpsDefaults (eylem\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="993c5-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="993c5-873">Her HTTPS uç noktası için çalıştırılacak bir yapılandırma `Action` belirtir.</span><span class="sxs-lookup"><span data-stu-id="993c5-873">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="993c5-874">`ConfigureHttpsDefaults` birden çok kez çağrılması, önceki `Action`s 'in belirtilen son `Action` yerini alır.</span><span class="sxs-lookup"><span data-stu-id="993c5-874">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="993c5-875"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> çağrılmadan oluşturulan bitiş noktaları, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> çağrılmadan **önce** , varsayılan olarak uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-875">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="993c5-876">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="993c5-876">Configure(IConfiguration)</span></span>

<span data-ttu-id="993c5-877">Giriş olarak bir <xref:Microsoft.Extensions.Configuration.IConfiguration> alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="993c5-877">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="993c5-878">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-878">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="993c5-879">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="993c5-879">ListenOptions.UseHttps</span></span>

<span data-ttu-id="993c5-880">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-880">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="993c5-881">`ListenOptions.UseHttps` uzantıları:</span><span class="sxs-lookup"><span data-stu-id="993c5-881">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="993c5-882">`UseHttps` &ndash; varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-882">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="993c5-883">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="993c5-883">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="993c5-884">`ListenOptions.UseHttps` parametreleri:</span><span class="sxs-lookup"><span data-stu-id="993c5-884">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="993c5-885">`filename`, uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-885">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="993c5-886">X. 509.440 sertifika verilerine erişmek için gereken parola `password`.</span><span class="sxs-lookup"><span data-stu-id="993c5-886">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="993c5-887">`configureOptions`, `HttpsConnectionAdapterOptions`yapılandırmak için `Action`.</span><span class="sxs-lookup"><span data-stu-id="993c5-887">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="993c5-888">`ListenOptions`döndürür.</span><span class="sxs-lookup"><span data-stu-id="993c5-888">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="993c5-889">`storeName`, sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="993c5-889">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="993c5-890">`subject`, sertifikanın konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-890">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="993c5-891">`allowInvalid`, otomatik olarak imzalanan sertifikalar gibi geçersiz sertifikaların dikkate alınıp alınmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="993c5-891">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="993c5-892">`location`, sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="993c5-892">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="993c5-893">`serverCertificate` X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-893">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="993c5-894">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="993c5-894">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="993c5-895">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-895">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="993c5-896">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="993c5-896">Supported configurations described next:</span></span>

* <span data-ttu-id="993c5-897">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="993c5-897">No configuration</span></span>
* <span data-ttu-id="993c5-898">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="993c5-898">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="993c5-899">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="993c5-899">Change the defaults in code</span></span>

<span data-ttu-id="993c5-900">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="993c5-900">*No configuration*</span></span>

<span data-ttu-id="993c5-901">Kestrel `http://localhost:5000` ve `https://localhost:5001` dinler (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="993c5-901">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="993c5-902">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="993c5-902">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="993c5-903">Kestrel yapılandırmasını yüklemek için varsayılan olarak `Configure(context.Configuration.GetSection("Kestrel"))` `CreateDefaultBuilder` çağırır.</span><span class="sxs-lookup"><span data-stu-id="993c5-903">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="993c5-904">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-904">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="993c5-905">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-905">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="993c5-906">Aşağıdaki *appSettings. JSON* örneğinde:</span><span class="sxs-lookup"><span data-stu-id="993c5-906">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="993c5-907">Geçersiz sertifikaların (örneğin, otomatik olarak imzalanan sertifikalar) kullanılmasına izin vermek için, **Allowwınvalid** öğesini `true` olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-907">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="993c5-908">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan**Httpsdefaultcert** ), **varsayılan** veya geliştirme sertifikası > **Sertifikalar** altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="993c5-908">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="993c5-909">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="993c5-909">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="993c5-910">Örneğin, **sertifikalar** > **varsayılan** sertifika şöyle belirlenebilir:</span><span class="sxs-lookup"><span data-stu-id="993c5-910">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="993c5-911">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="993c5-911">Schema notes:</span></span>

* <span data-ttu-id="993c5-912">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-912">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="993c5-913">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-913">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="993c5-914">Her uç nokta için `Url` parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-914">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="993c5-915">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-915">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="993c5-916">Bu uç noktalar, üst düzey `Urls` yapılandırmasında tanımlananlara eklemek yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="993c5-916">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="993c5-917">`Listen` aracılığıyla kodda tanımlanan uç noktalar yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-917">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="993c5-918">`Certificate` bölümü isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-918">The `Certificate` section is optional.</span></span> <span data-ttu-id="993c5-919">`Certificate` bölümü belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-919">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="993c5-920">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-920">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="993c5-921">`Certificate` bölümü, hem **yol**&ndash;**parolasını** hem de **Konu**&ndash;**Mağaza** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="993c5-921">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="993c5-922">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-922">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="993c5-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))`, yapılandırılmış bir uç noktanın ayarlarını tamamlamak için kullanılabilecek `.Endpoint(string name, listenOptions => { })` yöntemi ile bir `KestrelConfigurationLoader` döndürür:</span><span class="sxs-lookup"><span data-stu-id="993c5-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="993c5-924">`KestrelServerOptions.ConfigurationLoader`, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>tarafından sağlana gibi var olan yükleyicisindeki yinelenmeye devam etmek için doğrudan erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-924">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="993c5-925">Her uç noktanın yapılandırma bölümü, özel ayarların okunabilmesi için `Endpoint` yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-925">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="993c5-926">`options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak birden çok yapılandırma yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-926">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="993c5-927">`Load` önceki örneklerde açıkça çağrılmamışsa yalnızca son yapılandırma kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-927">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="993c5-928">Metapackage, varsayılan yapılandırma bölümünün değiştirilebilmesi için `Load` çağırmaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-928">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="993c5-929">`KestrelConfigurationLoader`, API 'lerin `Listen` ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-929">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="993c5-930">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-930">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="993c5-931">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="993c5-931">*Change the defaults in code*</span></span>

<span data-ttu-id="993c5-932">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults`, önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma da dahil olmak üzere `ListenOptions` ve `HttpsConnectionAdapterOptions`için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-932">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="993c5-933">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-933">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="993c5-934">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="993c5-934">*Kestrel support for SNI*</span></span>

<span data-ttu-id="993c5-935">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-935">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="993c5-936">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="993c5-936">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="993c5-937">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-937">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="993c5-938">Kestrel `ServerCertificateSelector` geri çağırma aracılığıyla SNı destekler.</span><span class="sxs-lookup"><span data-stu-id="993c5-938">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="993c5-939">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="993c5-939">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="993c5-940">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="993c5-940">SNI support requires:</span></span>

* <span data-ttu-id="993c5-941">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="993c5-941">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="993c5-942">`net461` veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman `null`.</span><span class="sxs-lookup"><span data-stu-id="993c5-942">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="993c5-943">Ayrıca, istemci, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa da `null` `name`.</span><span class="sxs-lookup"><span data-stu-id="993c5-943">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="993c5-944">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="993c5-944">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="993c5-945">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="993c5-945">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="993c5-946">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="993c5-946">Connection logging</span></span>

<span data-ttu-id="993c5-947">Bir bağlantıda bayt düzeyinde iletişim için hata ayıklama düzeyi günlüklerini yayma <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> çağırın.</span><span class="sxs-lookup"><span data-stu-id="993c5-947">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="993c5-948">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-948">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="993c5-949">`UseConnectionLogging` `UseHttps`önce yerleştirilmişse, şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-949">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="993c5-950">`UseConnectionLogging` `UseHttps`sonra yerleştirilmişse, şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-950">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="993c5-951">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="993c5-951">Bind to a TCP socket</span></span>

<span data-ttu-id="993c5-952"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> yöntemi bir TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="993c5-952">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="993c5-953">Örnek, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>olan bir uç nokta için HTTPS 'yi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="993c5-953">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="993c5-954">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="993c5-954">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="993c5-955">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="993c5-955">Bind to a Unix socket</span></span>

<span data-ttu-id="993c5-956">Aşağıdaki örnekte gösterildiği gibi NGINX ile daha iyi performans için <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="993c5-956">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="993c5-957">NGINX confiuguration dosyasında, `server` > `location` > `proxy_pass` girişi `http://unix:/tmp/{KESTREL SOCKET}:/;`olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="993c5-957">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="993c5-958">`{KESTREL SOCKET}`, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> için belirtilen yuvanın adıdır (örneğin, önceki örnekte `kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="993c5-958">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="993c5-959">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="993c5-959">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="993c5-960">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="993c5-960">Port 0</span></span>

<span data-ttu-id="993c5-961">`0` bağlantı noktası numarası belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-961">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="993c5-962">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="993c5-962">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="993c5-963">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="993c5-963">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="993c5-964">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="993c5-964">Limitations</span></span>

<span data-ttu-id="993c5-965">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="993c5-965">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="993c5-966">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="993c5-966">`--urls` command-line argument</span></span>
* <span data-ttu-id="993c5-967">`urls` ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="993c5-967">`urls` host configuration key</span></span>
* <span data-ttu-id="993c5-968">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="993c5-968">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="993c5-969">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-969">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="993c5-970">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="993c5-970">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="993c5-971">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece (örneğin, bu konuda daha önce gösterildiği gibi `KestrelServerOptions` yapılandırması veya yapılandırma dosyası kullanılarak), HTTPS bu yaklaşımlar ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-971">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="993c5-972">`Listen` ve `UseUrls` yaklaşımlarının her ikisi de aynı anda kullanıldığında `Listen` uç noktaları `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="993c5-972">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="993c5-973">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993c5-973">IIS endpoint configuration</span></span>

<span data-ttu-id="993c5-974">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları `Listen` veya `UseUrls`tarafından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-974">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="993c5-975">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="993c5-975">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="993c5-976">Aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993c5-976">Transport configuration</span></span>

<span data-ttu-id="993c5-977">ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır.</span><span class="sxs-lookup"><span data-stu-id="993c5-977">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="993c5-978">Bu, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> çağıran ve aşağıdaki paketlerden birine bağlı olan 2,1 ' ye yükselten ASP.NET Core 2,0 uygulamaları için bir son değişiklik değildir:</span><span class="sxs-lookup"><span data-stu-id="993c5-978">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="993c5-979">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)</span><span class="sxs-lookup"><span data-stu-id="993c5-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="993c5-980">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="993c5-980">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="993c5-981">Libuv kullanımını gerektiren projeler için:</span><span class="sxs-lookup"><span data-stu-id="993c5-981">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="993c5-982">Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="993c5-982">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="993c5-983"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>çağrısı:</span><span class="sxs-lookup"><span data-stu-id="993c5-983">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="993c5-984">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="993c5-984">URL prefixes</span></span>

<span data-ttu-id="993c5-985">`UseUrls`, `--urls` komut satırı bağımsız değişkenini, `urls` ana bilgisayar yapılandırma anahtarını veya `ASPNETCORE_URLS` ortam değişkenini kullanırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="993c5-985">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="993c5-986">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="993c5-986">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="993c5-987">Kestrel, `UseUrls`kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez.</span><span class="sxs-lookup"><span data-stu-id="993c5-987">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="993c5-988">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="993c5-988">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="993c5-989">`0.0.0.0`, tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="993c5-989">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="993c5-990">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="993c5-990">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="993c5-991">`[::]`, IPv4 `0.0.0.0`IPv6 eşdeğeridir.</span><span class="sxs-lookup"><span data-stu-id="993c5-991">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="993c5-992">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="993c5-992">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="993c5-993">Ana bilgisayar adları, `*`ve `+`özel değildir.</span><span class="sxs-lookup"><span data-stu-id="993c5-993">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="993c5-994">Geçerli bir IP adresi olarak tanınmayan bir şey veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-994">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="993c5-995">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [http. sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusu kullanın.</span><span class="sxs-lookup"><span data-stu-id="993c5-995">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="993c5-996">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="993c5-996">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="993c5-997">Port numarası veya bağlantı noktası numarası ile geri döngü IP 'si `localhost` ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="993c5-997">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="993c5-998">`localhost` belirtildiğinde, Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="993c5-998">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="993c5-999">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="993c5-999">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="993c5-1000">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="993c5-1000">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="993c5-1001">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="993c5-1001">Host filtering</span></span>

<span data-ttu-id="993c5-1002">Kestrel, `http://example.com:5000`gibi önekleri temel alarak yapılandırmayı desteklese de, büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="993c5-1002">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="993c5-1003">Ana bilgisayar `localhost`, geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="993c5-1003">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="993c5-1004">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-1004">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="993c5-1005">`Host` üstbilgileri doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="993c5-1005">`Host` headers aren't validated.</span></span>

<span data-ttu-id="993c5-1006">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="993c5-1006">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="993c5-1007">Ana bilgisayar filtreleme ara yazılımı, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya 2,2) ' de yer alan [Microsoft. Aspnetcore. hostfiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="993c5-1007">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="993c5-1008">Ara yazılım, <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>çağıran <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>tarafından eklenir:</span><span class="sxs-lookup"><span data-stu-id="993c5-1008">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="993c5-1009">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="993c5-1009">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="993c5-1010">Ara yazılımı etkinleştirmek için *appSettings. json*/appSettings içinde bir `AllowedHosts` anahtarı tanımlayın *.\<environmentname >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="993c5-1010">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="993c5-1011">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="993c5-1011">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="993c5-1012">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="993c5-1012">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="993c5-1013">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) ayrıca bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçeneği de vardır.</span><span class="sxs-lookup"><span data-stu-id="993c5-1013">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="993c5-1014">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="993c5-1014">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="993c5-1015">Iletilen üst bilgi ara sunucusu ile `AllowedHosts` ayarlama, istekleri bir ters ara sunucu veya yük dengeleyici ile iletirken, `Host` üst bilgisi korunurken uygun olur.</span><span class="sxs-lookup"><span data-stu-id="993c5-1015">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="993c5-1016">Kestrel genel kullanıma açık bir uç sunucu olarak veya `Host` üstbilgisi doğrudan iletildiğinde, ana bilgisayar filtreleme ara yazılımı ile `AllowedHosts` ayarlama uygundur.</span><span class="sxs-lookup"><span data-stu-id="993c5-1016">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="993c5-1017">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="993c5-1017">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="993c5-1018">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="993c5-1018">Additional resources</span></span>

* <span data-ttu-id="993c5-1019">Linux üzerinde UNIX yuvaları kullanırken, yuva uygulama kapatılırken otomatik olarak silinmez.</span><span class="sxs-lookup"><span data-stu-id="993c5-1019">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="993c5-1020">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/14134)bakın.</span><span class="sxs-lookup"><span data-stu-id="993c5-1020">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="993c5-1021">RFC 7230: Ileti sözdizimi ve yönlendirme (Bölüm 5,4: Ana bilgisayar)</span><span class="sxs-lookup"><span data-stu-id="993c5-1021">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
