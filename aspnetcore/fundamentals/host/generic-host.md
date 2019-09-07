---
title: .NET genel ana bilgisayar
author: tdykstra
description: Uygulama başlatma ve ömür yönetiminden sorumlu .NET Core genel ana bilgisayarı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 261abae499a0d5f807a14aebd224949881067bc7
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773809"
---
# <a name="net-generic-host"></a>.NET genel ana bilgisayar

::: moniker range=">= aspnetcore-3.0"

Bu makalede .NET Core genel Konağı (<xref:Microsoft.Extensions.Hosting.HostBuilder>) tanıtılmakta ve nasıl kullanılacağına ilişkin yönergeler sağlanmaktadır.

## <a name="whats-a-host"></a>Ana bilgisayar nedir?

*Ana bilgisayar* , bir uygulamanın kaynaklarını kapsülleyen bir nesnedir, örneğin:

* Bağımlılık ekleme (dı)
* Günlüğe Kaydetme
* Yapılandırma
* `IHostedService`uygulamalarını

Bir konak başlatıldığında, bu uygulamanın, `IHostedService.StartAsync` dı kapsayıcısında bulduğu her <xref:Microsoft.Extensions.Hosting.IHostedService> bir uygulamasına çağrı yapılır. Bir Web uygulamasında, `IHostedService` uygulamalardan biri [http sunucu uygulaması](xref:fundamentals/index#servers)Başlatan bir Web hizmetidir.

Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.

3,0 ' den önceki ASP.NET Core sürümlerinde, [Web ana BILGISAYARı](xref:fundamentals/host/web-host) http iş yükleri için kullanılır. Web ana bilgisayarı artık Web uygulamaları için önerilmez ve yalnızca geriye dönük uyumluluk için kullanılabilir durumda kalır.

## <a name="set-up-a-host"></a>Konak ayarlama

Ana bilgisayar genellikle `Program` sınıfında kodla yapılandırılır, oluşturulur ve çalıştırılır. `Main` Yöntemi:

* Bir Oluşturucu `CreateHostBuilder` nesnesi oluşturmak ve yapılandırmak için bir yöntem çağırır.
* Oluşturucu `Build` nesnesindeki `Run` çağrılar ve Yöntemler.

Bu, dı kapsayıcısına tek `IHostedService` bir uygulama eklenerek http olmayan bir iş yükü için program.cs kodu sağlar. 

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

Bir http iş yükü `Main` için yöntem aynıdır `ConfigureWebHostDefaults`ancak `CreateHostBuilder` çağrılır:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Uygulama Entity Framework Core kullanıyorsa, `CreateHostBuilder` yöntemin adını veya imzasını değiştirmeyin. [Entity Framework Core araçları](/ef/core/miscellaneous/cli/) , uygulamayı çalıştırmadan Konağı yapılandıran `CreateHostBuilder` bir yöntem bulmayı bekler. Daha fazla bilgi için bkz. [Tasarım zamanı DbContext oluşturma](/ef/core/miscellaneous/cli/dbcontext-creation).

## <a name="default-builder-settings"></a>Varsayılan Oluşturucu ayarları 

<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> Yöntemi:

* İçerik kökünü tarafından <xref:System.IO.Directory.GetCurrentDirectory*>döndürülen yola ayarlar.
* Ana bilgisayar yapılandırmasını şuradan yükler:
  * "DOTNET_" önekli ortam değişkenleri.
  * Komut satırı bağımsız değişkenleri.
* Uygulama yapılandırmasını şuradan yükler:
  * *appSettings. JSON*.
  * *appSettings. {Environment}. JSON*.
  * [](xref:security/app-secrets) Uygulama `Development` ortamda çalıştığında gizli dizi Yöneticisi.
  * Ortam değişkenleri.
  * Komut satırı bağımsız değişkenleri.
* Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:
  * Konsol
  * Hata ayıklama
  * EventSource
  * Olay günlüğü (yalnızca Windows üzerinde çalışırken)
* Ortam geliştirme sırasında [kapsam doğrulaması](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulaması](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) etkinleştirilir.

`ConfigureWebHostDefaults` Yöntemi:

* "ASPNETCORE_" önekli ortam değişkenlerinden ana bilgisayar yapılandırmasını yükler.
* [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu Web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır. Kestrel sunucusunun varsayılan seçenekleri için bkz <xref:fundamentals/servers/kestrel#kestrel-options>.
* [Ana bilgisayar filtreleme ara yazılımı](xref:fundamentals/servers/kestrel#host-filtering)ekler.
* ASPNETCORE_FORWARDEDHEADERS_ENABLED = true ise [Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler.
* IIS tümleştirmesini etkinleştirilir. IIS varsayılan seçenekleri için bkz <xref:host-and-deploy/iis/index#iis-options>.

Bu makalenin ilerleyen kısımlarında [Web Apps bölümlerine yönelik](#settings-for-web-apps) [tüm uygulama türleri](#settings-for-all-app-types) ve ayarlarının ayarları, varsayılan Oluşturucu ayarlarının nasıl geçersiz kılınacağını göstermektedir.

## <a name="framework-provided-services"></a>Framework tarafından sunulan hizmetler

Kayıtlı hizmetler otomatik olarak şunları içerir:

* [Ihostapplicationlifetime](#ihostapplicationlifetime)
* [Ihostlifetime](#ihostlifetime)
* [Ihostenvironment/ıwebhostenvironment](#ihostenvironment)

Framework tarafından sunulan tüm hizmetlerin bir listesi için bkz <xref:fundamentals/dependency-injection#framework-provided-services>.

## <a name="ihostapplicationlifetime"></a>Ihostapplicationlifetime

Başlatma sonrası ve düzgün `IApplicationLifetime`kapanma görevlerini işlemek için herhangi bir sınıfa (eskiadıyla)hizmetiniekleyin.<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir. Arabirim Ayrıca bir `StopApplication` yöntemi içerir.

Aşağıdaki örnek, `IApplicationLifetime` olayları kaydeden `IHostedService` bir uygulamasıdır:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>Ihostlifetime

<xref:Microsoft.Extensions.Hosting.IHostLifetime> Uygulama, ana bilgisayar başladığında ve durdurulduğunda denetler. Kaydedilen son uygulama kullanılır.

<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>Varsayılan `IHostLifetime` uygulama. `ConsoleLifetime`:

* CTRL + C/sigint veya sigdönem için dinler ve kapalı <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> işlemi başlatmak için çağırır.
* [RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır.

## <a name="ihostenvironment"></a>Ihostenvironment

Aşağıdaki bilgileri almak için hizmetibirsınıfaekleyin:<xref:Microsoft.Extensions.Hosting.IHostEnvironment>

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [Contentrootyolu](#contentrootpath)

Web Apps, devralan `IWebHostEnvironment` `IHostEnvironment` ve ekleyen arabirimini uygular:

* [WebRootPath](#webroot)

## <a name="host-configuration"></a>Konak yapılandırması

Ana bilgisayar yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostEnvironment> uygulamanın özellikleri için kullanılır.

Konak yapılandırması, içinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> [hostbuildercontext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) içinden kullanılabilir. Sonra `ConfigureAppConfiguration` ,`HostBuilderContext.Configuration` uygulama yapılandırması ile değiştirilmiştir.

Konak yapılandırması eklemek için üzerinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`öğesini çağırın. `ConfigureHostConfiguration`, eklenebilir sonuçlarla birden çok kez çağrılabilir. Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.

Ön eke `DOTNET_` ve komut satırı bağımsız değişkenlerine sahip ortam değişkeni sağlayıcısı, createdefaultbuilder tarafından eklenir. Web Apps için, ön ekine `ASPNETCORE_` sahip ortam değişkeni sağlayıcısı eklenir. Ortam değişkenleri okurken ön ek kaldırılır. Örneğin, için `ASPNETCORE_ENVIRONMENT` ortam değişkeni değeri, `environment` anahtar için ana bilgisayar yapılandırma değeri olur.

Aşağıdaki örnek ana bilgisayar yapılandırması oluşturur:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Uygulama yapılandırması

Uygulama yapılandırması, üzerinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder`çağırarak oluşturulur. `ConfigureAppConfiguration`, eklenebilir sonuçlarla birden çok kez çağrılabilir. Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır. 

Tarafından `ConfigureAppConfiguration` oluşturulan yapılandırma, sonraki işlemler ve dı hizmeti olarak, [hostbuildercontext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) konumunda kullanılabilir. Konak yapılandırması, uygulama yapılandırmasına de eklenir.

Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index#configureappconfiguration)konusuna bakın.

## <a name="settings-for-all-app-types"></a>Tüm uygulama türleri için ayarlar

Bu bölüm, hem HTTP hem de HTTP olmayan iş yükleri için uygulanan konak ayarlarını listeler. Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya `ASPNETCORE_` öneki olabilir.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

[Ihostenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır.

**Anahtar**: ApplicationName  
**Tür**: *dize*  
**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.
**Ortam değişkeni**:`<PREFIX_>APPLICATIONNAME`

Bu değeri ayarlamak için ortam değişkenini kullanın. 

### <a name="contentrootpath"></a>ContentRootPath

[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, konağın içerik dosyalarını aramaya başladığı yeri belirler. Yol yoksa, ana bilgisayar başlatılamaz.

**Anahtar**: contentroot  
**Tür**: *dize*  
**Varsayılan**: Uygulama derlemesinin bulunduğu klasör.  
**Ortam değişkeni**:`<PREFIX_>CONTENTROOT`

Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde `UseContentRoot` `IHostBuilder`arama yapın:

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

### <a name="environmentname"></a>EnvironmentName

[Ihostenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir. Çerçeve tanımlı değerler, `Development` `Staging`, ve `Production`içerir. Değerler büyük/küçük harfe duyarlı değildir.

**Anahtar**: ortam  
**Tür**: *dize*  
**Varsayılan**: Üretiminden  
**Ortam değişkeni**:`<PREFIX_>ENVIRONMENT`

Bu değeri ayarlamak için, ortam değişkenini kullanın veya üzerinde `UseEnvironment` `IHostBuilder`arama yapın:

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[Hostoptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) , için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman aşımını ayarlar. Varsayılan değer beş saniyedir.  Zaman aşımı süresi boyunca ana bilgisayar:

* [Ihostapplicationlifetime. Applicationdurduruluyor](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping)tetikler.
* Üzerinde durmayacak hizmetler için barındırılan Hizmetleri durdurma ve hataları günlüğe kaydetme girişimleri.

Tüm barındırılan hizmetler durmadan önce zaman aşımı süresi dolarsa, uygulama kapandığında kalan etkin hizmetler durdurulur. Hizmetler, işlemeyi tamamlamadıklarında bile durur. Hizmetlerin durdurulması için ek süre gerekiyorsa, zaman aşımını artırın.

**Anahtar**: shutdowntimeoutseconds  
**Tür**: *int*  
**Varsayılan**: 5 saniye **ortam değişkeni**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

Bu değeri ayarlamak için, ortam değişkenini kullanın veya yapılandırın `HostOptions`. Aşağıdaki örnek, zaman aşımını 20 saniye olarak ayarlar:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a>Web Apps ayarları

Bazı konak ayarları yalnızca HTTP iş yükleri için geçerlidir. Varsayılan olarak, bu ayarları yapılandırmak için kullanılan ortam değişkenlerinin bir `DOTNET_` veya `ASPNETCORE_` öneki olabilir.

Üzerinde `IWebHostBuilder` uzantı yöntemleri bu ayarlar için kullanılabilir. Aşağıdaki örnekte olduğu gibi, ' nin `webBuilder` `IWebHostBuilder`bir örneği olduğunu belirten uzantı yöntemlerinin nasıl çağrılacağını gösteren kod örnekleri:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>CaptureStartupErrors

Ne `false`zaman, başlatma sırasında oluşan hata, ana bilgisayardan çıkılıyor. Ne `true`zaman, ana bilgisayar başlangıç sırasında özel durumları yakalar ve sunucuyu başlatmaya çalışır.

**Anahtar**: capturestartuperrors  
**Tür**: *bool* (`true` veya `1`)  
**Varsayılan**: Uygulamanın IIS 'nin arkasında Kestrel, varsayılan olarak olduğu `true` durumlardışındaçalışır.`false`  
**Ortam değişkeni**:`<PREFIX_>CAPTURESTARTUPERRORS`

Bu değeri ayarlamak için yapılandırma veya çağırma `CaptureStartupErrors`kullanın:

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

Etkinleştirildiğinde veya ortam `Development`olduğunda, uygulama ayrıntılı hataları yakalar.

**Anahtar**: detailederrors  
**Tür**: *bool* (`true` veya `1`)  
**Varsayılan**: false  
**Ortam değişkeni**:`<PREFIX_>_DETAILEDERRORS`

Bu değeri ayarlamak için yapılandırma veya çağırma `UseSetting`kullanın:

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Başlangıçta yüklenecek başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize. Yapılandırma değeri boş bir dize olarak varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir. Barındırma başlangıç derlemeleri sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenir.

**Anahtar**: hostingStartupAssemblies  
**Tür**: *dize*  
**Varsayılan**: Boş dize  
**Ortam değişkeni**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Bu değeri ayarlamak için yapılandırma veya çağırma `UseSetting`kullanın:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

Başlangıçta dışlamak üzere başlangıç derlemelerinin barındırılması için noktalı virgülle ayrılmış bir dize.

**Anahtar**: hostingstartupexcludeassemblies  
**Tür**: *dize*  
**Varsayılan**: Boş dize  
**Ortam değişkeni**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Bu değeri ayarlamak için yapılandırma veya çağırma `UseSetting`kullanın:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

HTTPS yeniden yönlendirme bağlantı noktası. [Https zorlama](xref:security/enforcing-ssl)bölümünde kullanılır.

**Anahtar**: https_port  
**Tür**: *dize*  
**Varsayılan**: Varsayılan değer ayarlı değildir.  
**Ortam değişkeni**:`<PREFIX_>HTTPS_PORT`

Bu değeri ayarlamak için yapılandırma veya çağırma `UseSetting`kullanın:

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>Tercih Hostingurl 'Leri

Konağın `IWebHostBuilder` `IServer` uygulamayla yapılandırılanlar yerine ile yapılandırılan URL 'lerde dinleme yapıp kullanmayacağını belirtir.

**Anahtar**: preferhostingurl 'leri  
**Tür**: *bool* (`true` veya `1`)  
**Varsayılan**: true  
**Ortam değişkeni**:`<PREFIX_>_PREFERHOSTINGURLS`

Bu değeri ayarlamak için, ortam değişkenini veya çağrısını `PreferHostingUrls`kullanın:

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>Koruyucu Thostınstartup

Uygulamanın derlemesi tarafından yapılandırılan başlatma derlemelerinin barındırılması dahil olmak üzere, barındırma başlangıç derlemelerinin otomatik yüklenmesini engeller. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.

**Anahtar**: koruyucu thostingstartup  
**Tür**: *bool* (`true` veya `1`)  
**Varsayılan**: false  
**Ortam değişkeni**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`

Bu değeri ayarlamak için, ortam değişkenini veya çağrısını `UseSetting` kullanın:

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

`Startup` Sınıfı aramak için bütünleştirilmiş kod.

**Anahtar**: startupassembly  
**Tür**: *dize*  
**Varsayılan**: Uygulamanın derlemesi  
**Ortam değişkeni**:`<PREFIX_>STARTUPASSEMBLY`

Bu değeri ayarlamak için, ortam değişkenini veya çağrısını `UseStartup`kullanın. `UseStartup`bir derleme adı (`string`) veya bir tür (`TStartup`) alabilir. Birden çok `UseStartup` yöntem çağrılırsa, son bir öncelik alır.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>URL'ler

Sunucu istekleri için dinlemesi gereken bağlantı noktaları ve protokollerle, noktalı virgülle ayrılmış IP adresleri listesi veya ana bilgisayar adresleri. Örneğin: `http://localhost:123`. Sunucunun belirtilen\*bağlantı noktasını ve Protokolü (örneğin, `http://*:5000`) kullanarak herhangi bir IP adresi veya ana bilgisayar için istekleri dinlemesi gerektiğini belirtmek için "" kullanın. Protokol (`http://` veya `https://`) her URL 'ye dahil edilmiş olmalıdır. Desteklenen biçimler sunucular arasında farklılık gösterir.

**Anahtar**: URL 'ler  
**Tür**: *dize*  
**Varsayılan**: `http://localhost:5000` ve`https://localhost:5001`  
**Ortam değişkeni**:`<PREFIX_>URLS`

Bu değeri ayarlamak için, ortam değişkenini veya çağrısını `UseUrls`kullanın:

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel kendi uç nokta yapılandırması API 'sine sahiptir. Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>WebRoot

Uygulamanın statik varlıklarının göreli yolu.

**Anahtar**: Webroot  
**Tür**: *dize*  
**Varsayılan**: *(Içerik kökü)/Wwwroot*, yol varsa. Yol yoksa, Hayır-op dosya sağlayıcısı kullanılır.  
**Ortam değişkeni**:`<PREFIX_>WEBROOT`

Bu değeri ayarlamak için, ortam değişkenini veya çağrısını `UseWebRoot`kullanın:

```csharp
webBuilder.UseWebRoot("public");
```

## <a name="manage-the-host-lifetime"></a>Konak ömrünü yönetme

Uygulamayı başlatmak ve durdurmak için <xref:Microsoft.Extensions.Hosting.IHost> oluşturulan uygulamadaki Yöntemleri çağırın. Bu yöntemler, hizmet <xref:Microsoft.Extensions.Hosting.IHostedService> kapsayıcısına kayıtlı tüm uygulamaları etkiler.

### <a name="run"></a>Çalıştır

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırır ve ana bilgisayarı kapatıncaya kadar çağıran iş parçacığını engeller.

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırır ve iptal belirteci veya <xref:System.Threading.Tasks.Task> kapanışı tetiklendiğinde tamamlanmış bir döndürür.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteği sağlar, ana bilgisayarı oluşturur ve başlatır, sonra da CTRL + C/SIGINT veya SIGDÖNEM ' in kapatılmasını bekler.

### <a name="start"></a>Başlat

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>Konağı zaman uyumlu olarak başlatır.

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>Konağı başlatır ve iptal belirteci ya <xref:System.Threading.Tasks.Task> da kapatması tetiklendiğinde tamamlanmış bir döndürür. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>, ' nin `StartAsync`başlangıcında çağrılır ve devam etmeden önce tamamlanana kadar bekler. Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.

### <a name="waitforshutdown"></a>Waitforkapatması

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>CTRL + C/SIGINT veya SIGTERM gibi bir ıhostlifetime tarafından tetiklenene kadar çağıran iş parçacığını engeller.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen belirteç <xref:System.Threading.Tasks.Task> ve çağrılar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür.

### <a name="external-control"></a>Dış denetim

Ana bilgisayar ömrünün doğrudan denetimi dışarıdan çağrılabilen yöntemler kullanılarak sağlanabilir:

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

ASP.NET Core uygulamalar bir konağı yapılandırıp başlatır. Uygulama başlatma ve ömür yönetimi için konak sorumludur.

Bu makalede, http isteklerini işlemeyecek uygulamalar<xref:Microsoft.Extensions.Hosting.HostBuilder>için kullanılan genel ana bilgisayar () ASP.NET Core ele alınmaktadır.

Genel konağın amacı, daha geniş bir konak senaryolarını etkinleştirmek üzere Web ana bilgisayar API 'sinden HTTP işlem hattını ayırmak için kullanılır. Yapılandırma, bağımlılık ekleme (dı) ve günlüğe kaydetme gibi çapraz kesme özelliğinden genel ana bilgisayar avantajı temel alınarak mesajlaşma, arka plan görevleri ve diğer HTTP olmayan iş yükleri.

Genel ana bilgisayar ASP.NET Core 2,1 ' de yenidir ve Web barındırma senaryolarında uygun değildir. Web barındırma senaryolarında [Web konağını](xref:fundamentals/host/web-host)kullanın. Genel ana bilgisayar gelecek bir sürümdeki Web konağını değiştirecek ve hem HTTP hem de HTTP olmayan senaryolarda birincil ana bilgisayar API 'SI olarak görev yapacak.

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))

Örnek uygulamayı [Visual Studio Code](https://code.visualstudio.com/)' de çalıştırırken, *dış veya tümleşik bir Terminal*kullanın. Örneği bir `internalConsole`içinde çalıştırmayın.

Konsolu Visual Studio Code ayarlamak için:

1. *. Vscode/Launch. JSON* dosyasını açın.
1. **.NET Core başlatma (konsol)** yapılandırmasında **konsol** girişini bulun. Değeri ya da `externalTerminal` `integratedTerminal`olarak ayarlayın.

## <a name="introduction"></a>Giriş

Genel konak Kitaplığı <xref:Microsoft.Extensions.Hosting> ad alanında kullanılabilir ve [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) paketi tarafından sağlanır. Paket, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) 'e dahildir (ASP.NET Core 2,1 veya üzeri). `Microsoft.Extensions.Hosting`

<xref:Microsoft.Extensions.Hosting.IHostedService>, kod yürütmeye yönelik giriş noktasıdır. Her <xref:Microsoft.Extensions.Hosting.IHostedService> uygulama, [ConfigureServices içinde hizmet kaydı](#configureservices)sırasında yürütülür. <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>, konak başlatıldığında çağrılır <xref:Microsoft.Extensions.Hosting.IHostedService> ve <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> ana bilgisayar düzgün bir şekilde kapandığında ters kayıt sırasında çağrılır.

## <a name="set-up-a-host"></a>Konak ayarlama

<xref:Microsoft.Extensions.Hosting.IHostBuilder>, kitaplıkların ve uygulamaların Konağı başlatmak, derlemek ve çalıştırmak için kullandığı ana bileşendir:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>Seçenekler

<xref:Microsoft.Extensions.Hosting.HostOptions>için seçenekleri yapılandırın <xref:Microsoft.Extensions.Hosting.IHost>.

### <a name="shutdown-timeout"></a>Kapatılma zaman aşımı

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>zaman aşımını <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>ayarlar. Varsayılan değer beş saniyedir.

' Deki `Program.Main` aşağıdaki seçenek yapılandırması, varsayılan beş saniyelik kapatılma zaman aşımını 20 saniyeye yükseltir:

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

## <a name="default-services"></a>Varsayılan hizmetler

Aşağıdaki hizmetler konak başlatma sırasında kaydedilir:

* [Ortam](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* [Yapılandırma](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)
* <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)
* <xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)
* <xref:Microsoft.Extensions.Hosting.IHost>
* [Seçenekler](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)
* [Günlüğe kaydetme](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)

## <a name="host-configuration"></a>Konak yapılandırması

Ana bilgisayar yapılandırması şu şekilde oluşturulur:

* [İçerik kökünü](#content-root) ve ortamı <xref:Microsoft.Extensions.Hosting.IHostBuilder> ayarlamak için uzantı yöntemleri çağrılıyor. [](#environment)
* İçindeki <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>yapılandırma sağlayıcılarından yapılandırma okunuyor.

### <a name="extension-methods"></a>Uzantı yöntemleri

### <a name="application-key-name"></a>Uygulama anahtarı (ad)

[Ihostingenvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) özelliği konak oluşturma sırasında konak yapılandırmasından ayarlanır. Değeri açıkça ayarlamak için, [Hostdefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)kullanın:

**Anahtar**: ApplicationName  
**Tür**: *dize*  
**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.  
Şunu **kullanarak ayarla**:`HostBuilderContext.HostingEnvironment.ApplicationName`  
**Ortam değişkeni**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))

### <a name="content-root"></a>İçerik kökü

Bu ayar, konağın içerik dosyalarını aramaya başladığı yeri belirler.

**Anahtar**: contentroot  
**Tür**: *dize*  
**Varsayılan**: Uygulama derlemesinin bulunduğu klasörü varsayılan olarak belirler.  
Şunu **kullanarak ayarla**:`UseContentRoot`  
**Ortam değişkeni**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))

Yol yoksa, ana bilgisayar başlatılamaz.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a>Ortam

Uygulamanın [ortamını](xref:fundamentals/environments)ayarlar.

**Anahtar**: ortam  
**Tür**: *dize*  
**Varsayılan**: Üretiminden  
Şunu **kullanarak ayarla**:`UseEnvironment`  
**Ortam değişkeni**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` [isteğe bağlı ve Kullanıcı tanımlı](#configurehostconfiguration))

Ortam herhangi bir değere ayarlanabilir. Çerçeve tanımlı değerler, `Development` `Staging`, ve `Production`içerir. Değerler büyük/küçük harfe duyarlı değildir.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>ConfigureHostConfiguration

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>konak <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> için<xref:Microsoft.Extensions.Configuration.IConfiguration> oluşturmak üzere bir kullanır. Konak yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> uygulamanın derleme sürecinde kullanılmak üzere başlatmak için kullanılır.

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, eklenebilir sonuçlarla birden çok kez çağrılabilir. Ana bilgisayar, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır.

Varsayılan olarak hiçbir sağlayıcı dahil değildir. Uygulamanın gerektirdiği <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>her türlü yapılandırma sağlayıcısını açık bir şekilde belirtmeniz gerekir, örneğin:

* Dosya yapılandırması (örneğin, *HostSettings. JSON* dosyasından).
* Ortam değişkeni yapılandırması.
* Komut satırı bağımsız değişken yapılandırması.
* Diğer tüm gerekli yapılandırma sağlayıcıları.

Konağın dosya yapılandırması, uygulamanın temel yolu `SetBasePath` ve ardından [dosya yapılandırma sağlayıcılarından](xref:fundamentals/configuration/index#file-configuration-provider)birine yapılan bir çağrı tarafından belirtilerek etkinleştirilir. Örnek uygulama, bir JSON dosyası, *HostSettings. JSON*ve dosyanın ana bilgisayar <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> yapılandırma ayarlarını kullanmak için çağrılar kullanır.

Konağın [ortam değişkeni yapılandırmasını](xref:fundamentals/configuration/index#environment-variables-configuration-provider) eklemek için konak Oluşturucu ' ya <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> çağrı yapın. `AddEnvironmentVariables`Kullanıcı tanımlı isteğe bağlı bir ön eki kabul eder. Örnek uygulama, öneki `PREFIX_`kullanır. Ortam değişkenleri okurken ön ek kaldırılır. Örnek uygulamanın ana bilgisayarı yapılandırıldığında, için `PREFIX_ENVIRONMENT` ortam değişkeni değeri `environment` anahtar için ana bilgisayar yapılandırma değeri olur.

[Visual Studio](https://visualstudio.microsoft.com) kullanırken veya ile `dotnet run`bir uygulama çalıştırırken geliştirme sırasında, *Özellikler/launchsettings. JSON* dosyasında ortam değişkenleri ayarlanabilir. [Visual Studio Code](https://code.visualstudio.com/), geliştirme sırasında *. vscode/Launch. JSON* dosyasında ortam değişkenleri ayarlanabilir. Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

[Komut satırı yapılandırması](xref:fundamentals/configuration/index#command-line-configuration-provider) çağırarak <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>eklenir. Komut satırı yapılandırması, önceki yapılandırma sağlayıcıları tarafından belirtilen yapılandırmayı geçersiz kılmak için komut satırı bağımsız değişkenlerine izin vermek üzere en son eklenir.

*HostSettings. JSON*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

Ek yapılandırma [ApplicationName](#application-key-name) ve [contentroot](#content-root) anahtarlarıyla birlikte etkinleştirilebilir.

`HostBuilder` Kullanarak<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>örnek yapılandırma:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Uygulama yapılandırması, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> uygulama çağrısı yaparak oluşturulur. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>uygulama <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> için<xref:Microsoft.Extensions.Configuration.IConfiguration> oluşturmak üzere bir kullanır. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, eklenebilir sonuçlarla birden çok kez çağrılabilir. Uygulama, belirli bir anahtardaki bir değeri en son belirleyen seçeneği kullanır. Tarafından <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> oluşturulan yapılandırma, sonraki işlemler ve içinde <xref:Microsoft.Extensions.Hosting.IHost.Services*>, [hostbuildercontext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) konumunda kullanılabilir.

Uygulama yapılandırması, [Configurehostconfiguration](#configurehostconfiguration)tarafından belirtilen konak yapılandırmasını otomatik olarak alır.

Kullanarak <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>örnek uygulama yapılandırması:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appSettings. JSON*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*appSettings. Development. JSON*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*appSettings. Production. JSON*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

Ayar dosyalarını çıkış dizinine taşımak için, ayarlar dosyalarını proje dosyasında [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) olarak belirtin. Örnek uygulama, JSON uygulama ayarları dosyalarını ve *HostSettings. JSON* dosyasını şu `<Content>` öğe ile taşıdıkça:

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> Ve<xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> gibi yapılandırma uzantısı yöntemleri, örneğin [Microsoft. Extensions. Configuration. JSON](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) ve [Microsoft. Extensions. Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)gibi ek NuGet paketleri gerektirir. Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'i kullanmadığı takdirde, bu paketlerin temel [Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) paketine ek olarak projeye eklenmesi gerekir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

## <a name="configureservices"></a>ConfigureServices

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>uygulamanın [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına hizmet ekler. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.

Barındırılan hizmet, <xref:Microsoft.Extensions.Hosting.IHostedService> arabirimi uygulayan bir arka plan görevi mantığı olan bir sınıftır. Daha fazla bilgi için bkz. <xref:fundamentals/host/hosted-services>.

[Örnek uygulama](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , yaşam süresi `AddHostedService` olayları, `LifetimeEventsHostedService`ve zamanlanan bir arka plan görevi `TimedHostedService`için uygulamaya bir hizmet eklemek için genişletme yöntemini kullanır:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>ConfigureLogging

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>Belirtilen <xref:Microsoft.Extensions.Logging.ILoggingBuilder>yapılandırmayı yapılandırmak için bir temsilci ekler. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleLifetime

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>CTRL + C/sigint veya sigdönem için dinler ve kapalı <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> işlemi başlatmak için çağırır. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>[RunAsync](#runasync) ve [Waitforshutdownasync](#waitforshutdownasync)gibi uzantıları kaldırır. <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>Varsayılan yaşam süresi uygulamasına önceden kaydedilir. Kaydedilen son yaşam süresi kullanılır.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>Kapsayıcı yapılandırması

Diğer kapsayıcıları takmayı desteklemek için ana bilgisayar bir <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>kabul edebilir. Fabrika sağlamak, dı kapsayıcı kaydının bir parçası değildir, ancak bunun yerine somut dı kapsayıcısını oluşturmak için kullanılan bir konak iç sıdır. [Useserviceproviderfactory (ıvıceproviderfactory&lt;tcontainerbuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) , uygulamanın hizmet sağlayıcısını oluşturmak için kullanılan varsayılan fabrikası geçersiz kılar.

Özel kapsayıcı yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> yöntemiyle yönetilir. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>temel ana bilgisayar API 'sinin üstünde kapsayıcıyı yapılandırmak için kesin olarak belirlenmiş bir deneyim sağlar. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>, eklenebilir sonuçlarla birden çok kez çağrılabilir.

Uygulama için bir hizmet kapsayıcısı oluşturun:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

Hizmet kapsayıcısı fabrikası sağlama:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

Fabrika 'yi kullanın ve uygulama için özel hizmet kapsayıcısını yapılandırın:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>Genişletilebilirlik

Konak genişletilebilirliği, üzerindeki <xref:Microsoft.Extensions.Hosting.IHostBuilder>genişletme yöntemleriyle gerçekleştirilir. Aşağıdaki örnek, bir genişletme yönteminin ' de <xref:Microsoft.Extensions.Hosting.IHostBuilder> <xref:fundamentals/host/hosted-services>gösterilen [timedhostedservice](xref:fundamentals/host/hosted-services#timed-background-tasks) örneği ile bir uygulamayı nasıl genişlettiğini gösterir.

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

Uygulama, `UseHostedService` `T`geçirilen barındırılan hizmeti kaydetmek için uzantı yöntemi oluşturur:

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

## <a name="manage-the-host"></a>Konağı yönetme

Uygulama, hizmet kapsayıcısında kayıtlı olan <xref:Microsoft.Extensions.Hosting.IHostedService> uygulamaları başlatıp durdurmaktan sorumludur. <xref:Microsoft.Extensions.Hosting.IHost>

### <a name="run"></a>Çalıştır

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırır ve konak kapanana kadar çağıran iş parçacığını engeller:

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

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırır ve iptal belirteci veya <xref:System.Threading.Tasks.Task> kapanışı tetiklendiğinde tamamlanmış bir döndürür:

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

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Konsol desteği sağlar, ana bilgisayarı oluşturur ve başlatır, sonra da CTRL + C/SIGINT veya SIGDÖNEM ' in kapatılmasını bekler.

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

### <a name="start-and-stopasync"></a>Başlangıç ve StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>Konağı zaman uyumlu olarak başlatır.

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>belirtilen zaman aşımı süresi içinde Konağı durdurmaya çalışır.

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

### <a name="startasync-and-stopasync"></a>StartAsync ve StopAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uygulamayı başlatır.

<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>uygulamayı sonlandırır.

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

### <a name="waitforshutdown"></a>Waitforkapatması

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>, <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (örneğin, <xref:Microsoft.Extensions.Hosting.IHostLifetime>CTRL + C/sigint veya sigterim dinler) ile tetiklenir. <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>çağırır <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

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

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen belirteç <xref:System.Threading.Tasks.Task> ve çağrılar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatılma tetiklendiğinde tamamlanan bir döndürür.

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

### <a name="external-control"></a>Dış denetim

Ana bilgisayarın dış denetimine, dışarıdan çağrılabilen yöntemler kullanılarak ulaşılabilecek:

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

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>, ' nin <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>başlangıcında çağrılır ve devam etmeden önce tamamlanana kadar bekler. Bu, bir dış olay tarafından sinyallene kadar başlatmayı geciktirmek için kullanılabilir.

## <a name="ihostingenvironment-interface"></a>Ihostingenvironment arabirimi

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>uygulamanın barındırma ortamı hakkında bilgi sağlar. Özelliklerini ve uzantı yöntemlerini kullanmak üzere <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> sağlamak için [Oluşturucu Ekleme](xref:fundamentals/dependency-injection) özelliğini kullanın:

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

Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

## <a name="iapplicationlifetime-interface"></a>Iapplicationlifetime arabirimi

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime>düzgün kapanma istekleri de dahil olmak üzere başlatma sonrası ve kapanma etkinliklerine izin verir. Arabirimdeki üç özellik, başlangıç ve kapalı olayları tanımlayan yöntemleri kaydetmek <xref:System.Action> için kullanılan iptal belirteçleridir.

| İptal belirteci | Tetiklendiği zaman&#8230; |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | Konak tam olarak başlatıldı. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | Ana bilgisayar düzgün kapanma işlemini tamamlıyor. Tüm isteklerin işlenmesi gerekir. Bu olay tamamlanana kadar kapalı bloklar. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | Ana bilgisayar düzgün bir şekilde kapanma gerçekleştiriyor. İstekler hala işliyor olabilir. Bu olay tamamlanana kadar kapalı bloklar. |

Constructor- <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> hizmeti herhangi bir sınıfa ekleyin. [Örnek uygulama](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) , olayları kaydetmek için bir `LifetimeEventsHostedService` sınıfa (bir <xref:Microsoft.Extensions.Hosting.IHostedService> uygulama) ekleme oluşturucusunu kullanır.

*LifetimeEventsHostedService.cs*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>uygulamanın sonlandırılmasını ister. Aşağıdaki sınıf, `Shutdown` sınıfın <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> yöntemi çağrıldığında bir uygulamayı düzgün bir şekilde kapatmak için kullanır:

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

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/host/hosted-services>
