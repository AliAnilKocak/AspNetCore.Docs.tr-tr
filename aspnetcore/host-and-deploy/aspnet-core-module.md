---
title: ASP.NET Core Modülü
author: guardrex
description: ASP.NET Core uygulamalarını barındırmak için ASP.NET Core modülünü yapılandırmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/13/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 917ee462a8f9592120685b53d059a661cb4a7452
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333888"
---
# <a name="aspnet-core-module"></a>ASP.NET Core Modülü

, [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris](https://github.com/Tratcher)No, [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [adatın kotalik](https://github.com/jkotalik)ve [Luke Latham](https://github.com/guardrex) tarafından

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core modülü, IIS ardışık düzenine şu şekilde takılan yerel bir IIS modülüdür:

* [İşlem içi barındırma modeli](#in-process-hosting-model)olarak adlandırılan IIS çalışan işleminin (`w3wp.exe`) içinde bir ASP.NET Core uygulaması barındırın.
* Web isteklerini, [işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılan [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)çalıştıran bir arka uç ASP.NET Core uygulamasına iletin.

Desteklenen Windows sürümleri:

* Windows 7 veya üzeri
* Windows Server 2008 R2 veya üzeri

İşlem içi barındırma sırasında, modül IIS HTTP sunucusu (`IISHttpServer`) adlı IIS için işlem içi sunucu uygulamasını kullanır.

İşlem dışı barındırma sırasında modül yalnızca Kestrel ile birlikte kullanılabilir. Modül, [http. sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.

## <a name="hosting-models"></a>Barındırma modelleri

### <a name="in-process-hosting-model"></a>İşlem içi barındırma modeli

Uygulamalar, işlem içi barındırma modelinde varsayılan olarak ASP.NET Core.

İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:

* [Kestrel](xref:fundamentals/servers/kestrel) Server yerıne IIS HTTP sunucusu (`IISHttpServer`) kullanılır. İşlem içi, [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> ' i çağırır:

  * @No__t kaydedin-0.
  * ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.
  * Konağı, başlatma hatalarını yakalamak üzere yapılandırın.

* [RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.

* Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez. Uygulama başına bir uygulama havuzu kullanın.

* [Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya dağıtımda el ile bir [app_offline. htm dosyası](xref:host-and-deploy/iis/index#locked-deployment-files)yerleştirilirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir. Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.

* Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir.

* İstemci bağlantısı kesiliyor algılandı. İstemci bağlantısı kesildiğinde [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci iptal edilir.

* ASP.NET Core 2.2.1 veya önceki sürümlerde, <xref:System.IO.Directory.GetCurrentDirectory*>, uygulamanın dizini yerine IIS tarafından başlatılan işlemin çalışan dizinini döndürür (örneğin, *W3wp. exe*için *c:\Windows\System32\inetsrv* ).

  Uygulamanın geçerli dizinini ayarlayan örnek kod için bkz. [Currentdirectoryyardımcıları sınıfı](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs). @No__t-0 yöntemini çağırın. @No__t-0 ' a yönelik sonraki çağrılar, uygulamanın dizinini sağlar.

* İşlem sırasında barındırırken, bir kullanıcıyı başlatmak için <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> iç olarak çağrılmaz. Bu nedenle, her kimlik doğrulamasından sonra talepleri dönüştürmek için kullanılan <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> bir uygulama varsayılan olarak etkinleştirilmez. Talepler <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamasıyla dönüştürülürken, kimlik doğrulama hizmetleri eklemek için <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> çağırın:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * [Web paketi (tek dosya) dağıtımları](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) desteklenmez.

### <a name="out-of-process-hosting-model"></a>İşlem dışı barındırma modeli

Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, `<AspNetCoreHostingModel>` özelliğinin değerini proje dosyasında ( *. csproj*) `OutOfProcess` olarak ayarlayın:

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

İşlem içi barındırma, varsayılan değer olan `InProcess` ile ayarlanır.

[Kestrel](xref:fundamentals/servers/kestrel) sunucusu IIS HTTP sunucusu yerine kullanılır (`IISHttpServer`).

İşlem dışı için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> ' i çağırır:

* ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.
* Konağı, başlatma hatalarını yakalamak üzere yapılandırın.

### <a name="hosting-model-changes"></a>Barındırma modeli değişiklikleri

@No__t-0 ayarı *Web. config* dosyasında değiştirilirse ( [Web. config ile yapılandırma](#configuration-with-webconfig) bölümünde AÇıKLANMıŞTıR), modül IIS için çalışan işlemini geri dönüştürür.

IIS Express için modül çalışan işlemini geri dönüştürmez, bunun yerine geçerli IIS Express işleminin düzgün bir şekilde kapatılmasını tetikler. Uygulamaya yönelik bir sonraki istek, yeni bir IIS Express işlem olarak çoğaltılır.

### <a name="process-name"></a>İşlem adı

`Process.GetCurrentProcess().ProcessName` rapor `w3wp` @ no__t-2 @ no__t-3 (işlem içi) veya `dotnet` (işlem dışı).

Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır. ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi için, bkz. <xref:host-and-deploy/iis/modules>.

ASP.NET Core modülü de şunları yapabilir:

* Çalışan işlem için ortam değişkenlerini ayarlayın.
* Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.
* Windows kimlik doğrulama belirteçlerini ilet.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>ASP.NET Core modülünü yüklemek ve kullanmak

ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Web. config ile yapılandırma

ASP.NET Core modülü, sitenin *Web. config* dosyasındaki `system.webServer` düğümünün `aspNetCore` bölümü ile yapılandırılır.

Aşağıdaki *Web. config* dosyası, [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

Aşağıdaki *Web. config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

@No__t-0 özelliği, [\<location >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) öğesi içinde belirtilen ayarların uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını göstermek için `false` olarak ayarlanır.

Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında `stdoutLogFile` yolu `\\?\%home%\LogFiles\stdout` olarak ayarlanır. Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.

IIS alt uygulama yapılandırması hakkında bilgi için bkz. <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>AspNetCore öğesinin öznitelikleri

| Öznitelik | Açıklama | Varsayılan |
| --------- | ----------- | :-----: |
| `arguments` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</p> | |
| `disableStartUpErrorPage` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *Web. config* dosyasında yapılandırılan 502 durum kodu sayfası önceliklidir.</p> | `false` |
| `forwardWindowsAuthToken` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir. Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</p> | `true` |
| `hostingModel` | <p>İsteğe bağlı dize özniteliği.</p><p>Barındırma modelini işlem içi (`InProcess`) veya işlem dışı (`OutOfProcess`) olarak belirtir.</p> | `InProcess` |
| `processesPerApplication` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</p><p>&dagger;Işlem içi barındırma Için, değer `1` ile sınırlıdır.</p><p>@No__t-0 ayarı önerilmez. Bu öznitelik gelecek bir sürümde kaldırılacak.</p> | Varsayılan: `1`<br>Min: `1`<br>En fazla: `100` @ no__t-1 |
| `processPath` | <p>Gerekli dize özniteliği.</p><p>HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu. Göreli yollar desteklenir. Yol `.` ile başlıyorsa, yol site köküne göreli olarak kabul edilir.</p> | |
| `rapidFailsPerMinute` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir. Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</p><p>İşlem içi barındırma ile desteklenmez.</p> | Varsayılan: `10`<br>Min: `0`<br>En fazla: `100` |
| `requestTimeout` | <p>İsteğe bağlı TimeSpan özniteliği.</p><p>ASP.NET Core modülünün,% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</p><p>ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye olarak belirtilir.</p><p>İşlem içi barındırma için uygulanmaz. İşlem içi barındırma için modül, uygulamanın isteği işlemesini bekler.</p><p>Dizenin dakika ve saniye kesimleri için geçerli değerler 0-59 aralığındadır. Dakika veya saniye değerindeki **60** kullanımı, *500-iç sunucu hatasına*neden olur.</p> | Varsayılan: `00:02:00`<br>Min: `00:00:00`<br>En fazla: `360:00:00` |
| `shutdownTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>*App_offline. htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</p> | Varsayılan: `10`<br>Min: `0`<br>En fazla: `600` |
| `startupTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre. Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider. Modül, yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** kez başlayamadığı sürece sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</p><p>0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</p> | Varsayılan: `120`<br>Min: `0`<br>En fazla: `3600` |
| `stdoutLogEnabled` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</p> | `false` |
| `stdoutLogFile` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir. Göreli yollar, sitenin köküne göredir. @No__t-0 ' dan başlayan tüm yollar site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir. Yolda sunulan klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı ( *. log*) **stdoutLogFile** yolunun son kesimine eklenir. @No__t-0 değeri bir değer olarak sağlanırsa, 2/5/2018 tarihinde işlem 1934 KIMLIĞI ile 19:41:32 ' de kaydedildiğinde, *Günlükler* klasöründe *stdout_20180205194132_1934. log* adlı bir örnek stdout günlüğü kaydedilir.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Ortam değişkenlerini ayarlama

@No__t-0 özniteliğinde işlem için ortam değişkenleri belirtilebilir. Bir `<environmentVariables>` koleksiyon öğesinin `<environmentVariable>` alt öğesiyle bir ortam değişkeni belirtin. Bu bölümde ayarlanan ortam değişkenleri, sistem ortamı değişkenlerine göre önceliklidir.

Aşağıdaki örnek, *Web. config*dosyasında iki ortam değişkenini ayarlar. `ASPNETCORE_ENVIRONMENT`, uygulamanın ortamını `Development` ' ye yapılandırır. Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *Web. config* dosyasında ayarlayabilir. `CONFIG_DIR`, geliştiricinin uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı Kullanıcı tanımlı ortam değişkenine bir örnektir.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Ortamı doğrudan *Web. config* içinde ayarlamaya alternatif olarak, `<EnvironmentName>` özelliği yayımlama profili ( *. pubxml*) veya proje dosyasına dahil edilir. Bu yaklaşım, proje yayımlandığında *Web. config* içinde ortamı ayarlar:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Yalnızca `ASPNETCORE_ENVIRONMENT` ortam değişkenini, Internet gibi güvenilmeyen ağlarla erişilemeyen hazırlama ve test sunucularında `Development` olarak ayarlayın.

## <a name="app_offlinehtm"></a>app_offline. htm

Bir uygulamanın kök dizininde *app_offline. htm* adlı bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır. Uygulama, `shutdownTimeLimit` ' da tanımlanan saniye sayısından sonra hala çalışıyorsa, ASP.NET Core modülü çalışan işlemi de yok eder.

*App_offline. htm* dosyası mevcut olsa da ASP.NET Core modülü, istekleri, *app_offline. htm* dosyasının içeriğini geri göndererek yanıtlar. *App_offline. htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.

İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir. Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.

## <a name="start-up-error-page"></a>Başlatma hatası sayfası

Hem işlem içi hem de işlem dışı barındırma, uygulamayı başlatamadıklarında özel hata sayfaları üretir.

ASP.NET Core modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500,0-işlem içi/işlem dışı Işleyici yükleme hatası* durum kodu sayfası görüntülenir.

ASP.NET Core modülü uygulamayı başlatamadığında işlem içi barındırma için, *500,30-başlatma hatası* durum kodu sayfası görüntülenir.

ASP.NET Core modülü arka uç işlemini başlatamadığında veya arka uç işlemi başlatılırsa ancak yapılandırılmış bağlantı noktasında dinleyemediğinde, işlem dışı barındırma için *502,5-Işlem hatası* durum kodu sayfası görüntülenir.

Bu sayfayı bastırın ve varsayılan IIS 5xx durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın. Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http hataları \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Günlük oluşturma ve yeniden yönlendirme

@No__t-2 öğesinin `stdoutLogEnabled` ve `stdoutLogFile` öznitelikleri ayarlandıysa ASP.NET Core modülü stdout ve stderr konsol çıkışını diske yönlendirir. @No__t-0 yolundaki klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir (yazma izni sağlamak için `IIS AppPool\<app_pool_name>` kullanın).

İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez. Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.

Stdout günlüğünün kullanılması yalnızca uygulama başlatma sorunlarını gidermek için önerilir. Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın. ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir. Günlük dosyası adı, alt çizgi ile ayrılmış `stdoutLogFile` yolunun (genellikle *stdout*) son kesimine zaman damgası, işlem kimliği ve dosya uzantısı ( *. log*) eklenerek oluşur. @No__t-0 yolu *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan bir uygulama için günlük kaydı *stdout_20180205194132_1934. log*dosya adına sahiptir.

@No__t-0 yanlış ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır. Başlangıçtan sonra tüm ek Günlükler atılır.

Bir *Web. config* dosyasındaki aşağıdaki örnek `aspNetCore` öğesi, Azure App Service barındırılan bir uygulama için stdout günlüğünü yapılandırır. Yerel günlük kaydı için bir yerel yol veya ağ paylaşımının yolu kabul edilebilir. AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a>Gelişmiş tanılama günlükleri

ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir. @No__t-0 öğesini *Web. config*içindeki `<aspNetCore>` öğesine ekleyin. @No__t-3 ' ü `TRACE` olarak ayarlamak, tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Yoldaki tüm klasörler (önceki örnekteki*Günlükler* ), günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir (yazma izni sağlamak için `IIS AppPool\<app_pool_name>` kullanın).

Hata ayıklama düzeyi (`debugLevel`) değerleri hem düzeyi hem de konumu içerebilir.

Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):

* HATAYLA
* WARNING
* BILGISINE
* TRACE

Konumlar (birden çok konuma izin verilir):

* KONSOLA
* EVENTLOG
* DOSYASÝNÝ

İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:

* `ASPNETCORE_MODULE_DEBUG_FILE` @no__t-hata ayıklama günlük dosyasının yolu. (Varsayılan: *aspnetcore-Debug. log*)
* `ASPNETCORE_MODULE_DEBUG` &ndash; hata ayıklama düzeyi ayarı.

> [!WARNING]
> Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.** Günlüğün boyutu sınırlı değil. Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.

*Web. config* dosyasındaki `aspNetCore` öğesinin bir örneği için bkz. [Web. config ile yapılandırma](#configuration-with-webconfig) .

## <a name="modify-the-stack-size"></a>Yığın boyutunu değiştirme

*Yalnızca işlem içi barındırma modeli kullanılırken geçerlidir.*

Yönetilen yığın boyutunu, *Web. config*dosyasında bayt cinsinden `stackSize` ayarını kullanarak yapılandırın. Varsayılan boyut `1048576` bayttır (1 MB).

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır

*Yalnızca işlem dışı barındırma için geçerlidir.*

ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır. Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.

Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır. Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine (`ASPNETCORE_TOKEN`) ayarlanır. Eşleştirme belirteci, her proxy isteğinde de bir üst bilgi (`MS-ASPNETCORE-TOKEN`) olarak ayarlanır. IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler. Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir. Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir. Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü

ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır. Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici, ' deki *ApplicationHost. config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar. paylaşma.

IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, ASP.NET Core barındırma paketi yükleyicisini `1` olarak ayarlanmış `OPT_NO_SHARED_CONFIG_CHECK` parametresiyle çalıştırın:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:

1. IIS paylaşılan yapılandırmasını devre dışı bırakın.
1. Yükleyiciyi çalıştırın.
1. Güncelleştirilmiş *ApplicationHost. config* dosyasını paylaşıma dışarı aktarın.
1. IIS paylaşılan yapılandırmasını yeniden etkinleştirin.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Modül sürümü ve barındırma paketi yükleyici günlükleri

Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:

1. Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.
1. *Aspnetcore. dll* dosyasını bulun.
1. Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.
1. **Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.

Modülün barındırma paketi yükleyici günlükleri *C: \\Users @ no__t-2% username% \\AppData @ no__t-4Local @ no__t-5Temp*konumunda bulunur. Dosya, *dd_DotNetCoreWinSvrHosting__ @ no__t-7timestamp > _000_Aspnetcoremodupa_x64. log*olarak adlandırılır.

## <a name="module-schema-and-configuration-file-locations"></a>Modül, şema ve yapılandırma dosyası konumları

### <a name="module"></a>Modül

**IIS (X86/AMD64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\v2\aspnetcorev2,dll

* % ProgramFiles (x86)% \ ııs\ ASP.NET Core Module\v2\aspnetcorev2,dll

**IIS Express (X86/AMD64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* % ProgramFiles (x86)% \ IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\v2\aspnetcorev2,dll

* % ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\v2\aspnetcorev2,dll

### <a name="schema"></a>Şema

**ISS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Yapılandırma

**ISS**

* %Windir%\System32\inetsrv\config\applicationHost,config

**IIS Express**

* Visual Studio: {APPLICATION ROOT} \\. Vs\config\applicationhost,config

* *ıısexpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Dosyalar, *ApplicationHost. config* dosyasında *aspnetcore* ' u arayarak bulunabilir.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

ASP.NET Core modülü, IIS ardışık düzenine şu şekilde takılan yerel bir IIS modülüdür:

* [İşlem içi barındırma modeli](#in-process-hosting-model)olarak adlandırılan IIS çalışan işleminin (`w3wp.exe`) içinde bir ASP.NET Core uygulaması barındırın.
* Web isteklerini, [işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılan [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)çalıştıran bir arka uç ASP.NET Core uygulamasına iletin.

Desteklenen Windows sürümleri:

* Windows 7 veya üzeri
* Windows Server 2008 R2 veya üzeri

İşlem içi barındırma sırasında, modül IIS HTTP sunucusu (`IISHttpServer`) adlı IIS için işlem içi sunucu uygulamasını kullanır.

İşlem dışı barındırma sırasında modül yalnızca Kestrel ile birlikte kullanılabilir. Modül, [http. sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.

## <a name="hosting-models"></a>Barındırma modelleri

### <a name="in-process-hosting-model"></a>İşlem içi barındırma modeli

Bir uygulamayı işlem içi barındırma için yapılandırmak için, `<AspNetCoreHostingModel>` özelliğini uygulamanın proje dosyasına `InProcess` (işlem dışı barındırma `OutOfProcess` ile ayarlanır) ile birlikte ekleyin:

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

İşlem içi barındırma modeli, .NET Framework hedef ASP.NET Core uygulamalar için desteklenmez.

Dosyada `<AspNetCoreHostingModel>` özelliği yoksa, varsayılan değer `OutOfProcess` ' dir.

İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:

* [Kestrel](xref:fundamentals/servers/kestrel) Server yerıne IIS HTTP sunucusu (`IISHttpServer`) kullanılır. İşlem içi, [Createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> ' i çağırır:

  * @No__t kaydedin-0.
  * ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.
  * Konağı, başlatma hatalarını yakalamak üzere yapılandırın.

* [RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.

* Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez. Uygulama başına bir uygulama havuzu kullanın.

* [Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya dağıtımda el ile bir [app_offline. htm dosyası](xref:host-and-deploy/iis/index#locked-deployment-files)yerleştirilirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir. Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.

* Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir.

* İstemci bağlantısı kesiliyor algılandı. İstemci bağlantısı kesildiğinde [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci iptal edilir.

* ASP.NET Core 2.2.1 veya önceki sürümlerde, <xref:System.IO.Directory.GetCurrentDirectory*>, uygulamanın dizini yerine IIS tarafından başlatılan işlemin çalışan dizinini döndürür (örneğin, *W3wp. exe*için *c:\Windows\System32\inetsrv* ).

  Uygulamanın geçerli dizinini ayarlayan örnek kod için bkz. [Currentdirectoryyardımcıları sınıfı](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs). @No__t-0 yöntemini çağırın. @No__t-0 ' a yönelik sonraki çağrılar, uygulamanın dizinini sağlar.

* İşlem sırasında barındırırken, bir kullanıcıyı başlatmak için <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> iç olarak çağrılmaz. Bu nedenle, her kimlik doğrulamasından sonra talepleri dönüştürmek için kullanılan <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> bir uygulama varsayılan olarak etkinleştirilmez. Talepler <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamasıyla dönüştürülürken, kimlik doğrulama hizmetleri eklemek için <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> çağırın:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a>İşlem dışı barındırma modeli

Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, proje dosyasında aşağıdaki yaklaşımlardan birini kullanın:

* @No__t-0 özelliğini belirtmeyin. Dosyada `<AspNetCoreHostingModel>` özelliği yoksa, varsayılan değer `OutOfProcess` ' dir.
* @No__t-0 özelliğinin değerini `OutOfProcess` olarak ayarlayın (işlem içi barındırma `InProcess` ile ayarlanır):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

[Kestrel](xref:fundamentals/servers/kestrel) sunucusu IIS HTTP sunucusu yerine kullanılır (`IISHttpServer`).

İşlem dışı için [Createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> ' i çağırır:

* ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.
* Konağı, başlatma hatalarını yakalamak üzere yapılandırın.

### <a name="hosting-model-changes"></a>Barındırma modeli değişiklikleri

@No__t-0 ayarı *Web. config* dosyasında değiştirilirse ( [Web. config ile yapılandırma](#configuration-with-webconfig) bölümünde AÇıKLANMıŞTıR), modül IIS için çalışan işlemini geri dönüştürür.

IIS Express için modül çalışan işlemini geri dönüştürmez, bunun yerine geçerli IIS Express işleminin düzgün bir şekilde kapatılmasını tetikler. Uygulamaya yönelik bir sonraki istek, yeni bir IIS Express işlem olarak çoğaltılır.

### <a name="process-name"></a>İşlem adı

`Process.GetCurrentProcess().ProcessName` rapor `w3wp` @ no__t-2 @ no__t-3 (işlem içi) veya `dotnet` (işlem dışı).

Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır. ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi için, bkz. <xref:host-and-deploy/iis/modules>.

ASP.NET Core modülü de şunları yapabilir:

* Çalışan işlem için ortam değişkenlerini ayarlayın.
* Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.
* Windows kimlik doğrulama belirteçlerini ilet.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>ASP.NET Core modülünü yüklemek ve kullanmak

ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Web. config ile yapılandırma

ASP.NET Core modülü, sitenin *Web. config* dosyasındaki `system.webServer` düğümünün `aspNetCore` bölümü ile yapılandırılır.

Aşağıdaki *Web. config* dosyası, [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

Aşağıdaki *Web. config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

@No__t-0 özelliği, [\<location >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) öğesi içinde belirtilen ayarların uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını göstermek için `false` olarak ayarlanır.

Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında `stdoutLogFile` yolu `\\?\%home%\LogFiles\stdout` olarak ayarlanır. Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.

IIS alt uygulama yapılandırması hakkında bilgi için bkz. <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>AspNetCore öğesinin öznitelikleri

| Öznitelik | Açıklama | Varsayılan |
| --------- | ----------- | :-----: |
| `arguments` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</p> | |
| `disableStartUpErrorPage` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *Web. config* dosyasında yapılandırılan 502 durum kodu sayfası önceliklidir.</p> | `false` |
| `forwardWindowsAuthToken` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir. Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</p> | `true` |
| `hostingModel` | <p>İsteğe bağlı dize özniteliği.</p><p>Barındırma modelini işlem içi (`InProcess`) veya işlem dışı (`OutOfProcess`) olarak belirtir.</p> | `OutOfProcess` |
| `processesPerApplication` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</p><p>&dagger;Işlem içi barındırma Için, değer `1` ile sınırlıdır.</p><p>@No__t-0 ayarı önerilmez. Bu öznitelik gelecek bir sürümde kaldırılacak.</p> | Varsayılan: `1`<br>Min: `1`<br>En fazla: `100` @ no__t-1 |
| `processPath` | <p>Gerekli dize özniteliği.</p><p>HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu. Göreli yollar desteklenir. Yol `.` ile başlıyorsa, yol site köküne göreli olarak kabul edilir.</p> | |
| `rapidFailsPerMinute` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir. Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</p><p>İşlem içi barındırma ile desteklenmez.</p> | Varsayılan: `10`<br>Min: `0`<br>En fazla: `100` |
| `requestTimeout` | <p>İsteğe bağlı TimeSpan özniteliği.</p><p>ASP.NET Core modülünün,% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</p><p>ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye olarak belirtilir.</p><p>İşlem içi barındırma için uygulanmaz. İşlem içi barındırma için modül, uygulamanın isteği işlemesini bekler.</p><p>Dizenin dakika ve saniye kesimleri için geçerli değerler 0-59 aralığındadır. Dakika veya saniye değerindeki **60** kullanımı, *500-iç sunucu hatasına*neden olur.</p> | Varsayılan: `00:02:00`<br>Min: `00:00:00`<br>En fazla: `360:00:00` |
| `shutdownTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>*App_offline. htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</p> | Varsayılan: `10`<br>Min: `0`<br>En fazla: `600` |
| `startupTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre. Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider. Modül, yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** kez başlayamadığı sürece sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</p><p>0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</p> | Varsayılan: `120`<br>Min: `0`<br>En fazla: `3600` |
| `stdoutLogEnabled` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</p> | `false` |
| `stdoutLogFile` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir. Göreli yollar, sitenin köküne göredir. @No__t-0 ' dan başlayan tüm yollar site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir. Yolda sunulan klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı ( *. log*) **stdoutLogFile** yolunun son kesimine eklenir. @No__t-0 değeri bir değer olarak sağlanırsa, 2/5/2018 tarihinde işlem 1934 KIMLIĞI ile 19:41:32 ' de kaydedildiğinde, *Günlükler* klasöründe *stdout_20180205194132_1934. log* adlı bir örnek stdout günlüğü kaydedilir.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Ortam değişkenlerini ayarlama

@No__t-0 özniteliğinde işlem için ortam değişkenleri belirtilebilir. Bir `<environmentVariables>` koleksiyon öğesinin `<environmentVariable>` alt öğesiyle bir ortam değişkeni belirtin. Bu bölümde ayarlanan ortam değişkenleri, sistem ortamı değişkenlerine göre önceliklidir.

Aşağıdaki örnek iki ortam değişkenini ayarlar. `ASPNETCORE_ENVIRONMENT`, uygulamanın ortamını `Development` olarak yapılandırır. Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *Web. config* dosyasında ayarlayabilir. `CONFIG_DIR`, geliştiricinin uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı Kullanıcı tanımlı ortam değişkenine bir örnektir.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Ortamı doğrudan *Web. config* içinde ayarlamaya alternatif olarak, `<EnvironmentName>` özelliği yayımlama profili ( *. pubxml*) veya proje dosyasına dahil edilir. Bu yaklaşım, proje yayımlandığında *Web. config* içinde ortamı ayarlar:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Yalnızca `ASPNETCORE_ENVIRONMENT` ortam değişkenini, Internet gibi güvenilmeyen ağlarla erişilemeyen hazırlama ve test sunucularında `Development` olarak ayarlayın.

## <a name="app_offlinehtm"></a>app_offline. htm

Bir uygulamanın kök dizininde *app_offline. htm* adlı bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır. Uygulama, `shutdownTimeLimit` ' da tanımlanan saniye sayısından sonra hala çalışıyorsa, ASP.NET Core modülü çalışan işlemi de yok eder.

*App_offline. htm* dosyası mevcut olsa da ASP.NET Core modülü, istekleri, *app_offline. htm* dosyasının içeriğini geri göndererek yanıtlar. *App_offline. htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.

İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir. Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.

## <a name="start-up-error-page"></a>Başlatma hatası sayfası

Hem işlem içi hem de işlem dışı barındırma, uygulamayı başlatamadıklarında özel hata sayfaları üretir.

ASP.NET Core modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500,0-işlem içi/işlem dışı Işleyici yükleme hatası* durum kodu sayfası görüntülenir.

ASP.NET Core modülü uygulamayı başlatamadığında işlem içi barındırma için, *500,30-başlatma hatası* durum kodu sayfası görüntülenir.

ASP.NET Core modülü arka uç işlemini başlatamadığında veya arka uç işlemi başlatılırsa ancak yapılandırılmış bağlantı noktasında dinleyemediğinde, işlem dışı barındırma için *502,5-Işlem hatası* durum kodu sayfası görüntülenir.

Bu sayfayı bastırın ve varsayılan IIS 5xx durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın. Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http hataları \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Günlük oluşturma ve yeniden yönlendirme

@No__t-2 öğesinin `stdoutLogEnabled` ve `stdoutLogFile` öznitelikleri ayarlandıysa ASP.NET Core modülü stdout ve stderr konsol çıkışını diske yönlendirir. @No__t-0 yolundaki klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir (yazma izni sağlamak için `IIS AppPool\<app_pool_name>` kullanın).

İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez. Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.

Stdout günlüğünün kullanılması yalnızca uygulama başlatma sorunlarını gidermek için önerilir. Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın. ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir. Günlük dosyası adı, alt çizgi ile ayrılmış `stdoutLogFile` yolunun (genellikle *stdout*) son kesimine zaman damgası, işlem kimliği ve dosya uzantısı ( *. log*) eklenerek oluşur. @No__t-0 yolu *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan bir uygulama için günlük kaydı *stdout_20180205194132_1934. log*dosya adına sahiptir.

@No__t-0 yanlış ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır. Başlangıçtan sonra tüm ek Günlükler atılır.

Aşağıdaki örnek `aspNetCore` öğesi, Azure App Service barındırılan bir uygulama için stdout günlüğünü yapılandırır. Yerel günlük kaydı için bir yerel yol veya ağ paylaşımının yolu kabul edilebilir. AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a>Gelişmiş tanılama günlükleri

ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir. @No__t-0 öğesini *Web. config*içindeki `<aspNetCore>` öğesine ekleyin. @No__t-3 ' ü `TRACE` olarak ayarlamak, tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

@No__t-0 değerine (önceki örnekteki*Günlükler* ) belirtilen yoldaki klasörler, modül tarafından otomatik olarak oluşturulmaz ve dağıtımda önceden var olmalıdır. Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir (yazma izni sağlamak için `IIS AppPool\<app_pool_name>` kullanın).

Hata ayıklama düzeyi (`debugLevel`) değerleri hem düzeyi hem de konumu içerebilir.

Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):

* HATAYLA
* WARNING
* BILGISINE
* TRACE

Konumlar (birden çok konuma izin verilir):

* KONSOLA
* EVENTLOG
* DOSYASÝNÝ

İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:

* `ASPNETCORE_MODULE_DEBUG_FILE` @no__t-hata ayıklama günlük dosyasının yolu. (Varsayılan: *aspnetcore-Debug. log*)
* `ASPNETCORE_MODULE_DEBUG` &ndash; hata ayıklama düzeyi ayarı.

> [!WARNING]
> Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.** Günlüğün boyutu sınırlı değil. Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.

*Web. config* dosyasındaki `aspNetCore` öğesinin bir örneği için bkz. [Web. config ile yapılandırma](#configuration-with-webconfig) .

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır

*Yalnızca işlem dışı barındırma için geçerlidir.*

ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır. Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.

Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır. Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine (`ASPNETCORE_TOKEN`) ayarlanır. Eşleştirme belirteci, her proxy isteğinde de bir üst bilgi (`MS-ASPNETCORE-TOKEN`) olarak ayarlanır. IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler. Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir. Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir. Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü

ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır. Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici, ' deki *ApplicationHost. config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar. paylaşma.

IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, ASP.NET Core barındırma paketi yükleyicisini `1` olarak ayarlanmış `OPT_NO_SHARED_CONFIG_CHECK` parametresiyle çalıştırın:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:

1. IIS paylaşılan yapılandırmasını devre dışı bırakın.
1. Yükleyiciyi çalıştırın.
1. Güncelleştirilmiş *ApplicationHost. config* dosyasını paylaşıma dışarı aktarın.
1. IIS paylaşılan yapılandırmasını yeniden etkinleştirin.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Modül sürümü ve barındırma paketi yükleyici günlükleri

Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:

1. Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.
1. *Aspnetcore. dll* dosyasını bulun.
1. Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.
1. **Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.

Modülün barındırma paketi yükleyici günlükleri *C: \\Users @ no__t-2% username% \\AppData @ no__t-4Local @ no__t-5Temp*konumunda bulunur. Dosya, *dd_DotNetCoreWinSvrHosting__ @ no__t-7timestamp > _000_Aspnetcoremodupa_x64. log*olarak adlandırılır.

## <a name="module-schema-and-configuration-file-locations"></a>Modül, şema ve yapılandırma dosyası konumları

### <a name="module"></a>Modül

**IIS (X86/AMD64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\v2\aspnetcorev2,dll

* % ProgramFiles (x86)% \ ııs\ ASP.NET Core Module\v2\aspnetcorev2,dll

**IIS Express (X86/AMD64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* % ProgramFiles (x86)% \ IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\v2\aspnetcorev2,dll

* % ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\v2\aspnetcorev2,dll

### <a name="schema"></a>Şema

**ISS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Yapılandırma

**ISS**

* %Windir%\System32\inetsrv\config\applicationHost,config

**IIS Express**

* Visual Studio: {APPLICATION ROOT} \\. Vs\config\applicationhost,config

* *ıısexpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Dosyalar, *ApplicationHost. config* dosyasında *aspnetcore* ' u arayarak bulunabilir.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

ASP.NET Core modülü, Web isteklerini arka uca ASP.NET Core uygulamalarına iletmek için IIS ardışık düzenine takılan yerel bir IIS modülüdür.

Desteklenen Windows sürümleri:

* Windows 7 veya üzeri
* Windows Server 2008 R2 veya üzeri

Modül yalnızca Kestrel ile birlikte kullanılabilir. Modül, [http. sys](xref:fundamentals/servers/httpsys)ile uyumsuzdur.

ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, modül işlem yönetimini de işler. Modül, ilk istek ulaştığında ASP.NET Core App işlemini başlatır ve kilitlenirse uygulamayı yeniden başlatır. Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen IIS 'de işlem içinde çalışan ASP.NET 4. x uygulamaları ile görüldüğü aynı davranıştır.

Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve bir uygulama arasındaki ilişki gösterilmektedir:

![ASP.NET Core Modülü](aspnet-core-module/_static/ancm-outofprocess.png)

İstekler Web 'den çekirdek modu HTTP. sys sürücüsüne ulaşır. Sürücü, istekleri Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS). Modül, 80 veya 443 numaralı bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktasında istekleri Kestrel 'e iletir.

Modül, başlangıç sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve [IIS tümleştirme ara](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucusu, `http://localhost:{port}` ' i dinlemek için sunucuyu yapılandırır. Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir. Modül HTTPS iletmeyi desteklemez, bu nedenle istekler HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.

Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir. Ara yazılım ardışık düzeni isteği işler ve uygulamanın mantığına `HttpContext` örneği olarak geçirir. IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir. Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri gönderilir.

Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır. ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi için, bkz. <xref:host-and-deploy/iis/modules>.

ASP.NET Core modülü de şunları yapabilir:

* Çalışan işlem için ortam değişkenlerini ayarlayın.
* Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.
* Windows kimlik doğrulama belirteçlerini ilet.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>ASP.NET Core modülünü yüklemek ve kullanmak

ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Web. config ile yapılandırma

ASP.NET Core modülü, sitenin *Web. config* dosyasındaki `system.webServer` düğümünün `aspNetCore` bölümü ile yapılandırılır.

Aşağıdaki *Web. config* dosyası, [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Aşağıdaki *Web. config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında `stdoutLogFile` yolu `\\?\%home%\LogFiles\stdout` olarak ayarlanır. Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.

IIS alt uygulama yapılandırması hakkında bilgi için bkz. <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>AspNetCore öğesinin öznitelikleri

| Öznitelik | Açıklama | Varsayılan |
| --------- | ----------- | :-----: |
| `arguments` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</p>| |
| `disableStartUpErrorPage` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *Web. config* dosyasında yapılandırılan 502 durum kodu sayfası önceliklidir.</p> | `false` |
| `forwardWindowsAuthToken` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir. Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</p> | `true` |
| `processesPerApplication` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</p><p>@No__t-0 ayarı önerilmez. Bu öznitelik gelecek bir sürümde kaldırılacak.</p> | Varsayılan: `1`<br>Min: `1`<br>En fazla: `100` |
| `processPath` | <p>Gerekli dize özniteliği.</p><p>HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu. Göreli yollar desteklenir. Yol `.` ile başlıyorsa, yol site köküne göreli olarak kabul edilir.</p> | |
| `rapidFailsPerMinute` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir. Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</p> | Varsayılan: `10`<br>Min: `0`<br>En fazla: `100` |
| `requestTimeout` | <p>İsteğe bağlı TimeSpan özniteliği.</p><p>ASP.NET Core modülünün,% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</p><p>ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye olarak belirtilir.</p> | Varsayılan: `00:02:00`<br>Min: `00:00:00`<br>En fazla: `360:00:00` |
| `shutdownTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>*App_offline. htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</p> | Varsayılan: `10`<br>Min: `0`<br>En fazla: `600` |
| `startupTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre. Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider. Modül, yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** kez başlayamadığı sürece sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</p><p>0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</p> | Varsayılan: `120`<br>Min: `0`<br>En fazla: `3600` |
| `stdoutLogEnabled` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</p> | `false` |
| `stdoutLogFile` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir. Göreli yollar, sitenin köküne göredir. @No__t-0 ' dan başlayan tüm yollar site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir. Modülün günlük dosyasını oluşturması için yolda sunulan klasörlerin bulunması gerekir. Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı ( *. log*) **stdoutLogFile** yolunun son kesimine eklenir. @No__t-0 değeri bir değer olarak sağlanırsa, 2/5/2018 tarihinde işlem 1934 KIMLIĞI ile 19:41:32 ' de kaydedildiğinde, *Günlükler* klasöründe *stdout_20180205194132_1934. log* adlı bir örnek stdout günlüğü kaydedilir.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Ortam değişkenlerini ayarlama

@No__t-0 özniteliğinde işlem için ortam değişkenleri belirtilebilir. Bir `<environmentVariables>` koleksiyon öğesinin `<environmentVariable>` alt öğesiyle bir ortam değişkeni belirtin.

> [!WARNING]
> Bu bölümde ayarlanan ortam değişkenleri, aynı ada sahip sistem ortam değişkenleri ile çakışıyor. Bir ortam değişkeni hem *Web. config* dosyasında hem de Windows 'un sistem düzeyinde ayarlandıysa, *Web. config* dosyasındaki değer sistem ortam değişkeni değerine (örneğin, `ASPNETCORE_ENVIRONMENT: Development;Development`) eklenerek uygulamanın şunlar.

Aşağıdaki örnek iki ortam değişkenini ayarlar. `ASPNETCORE_ENVIRONMENT`, uygulamanın ortamını `Development` olarak yapılandırır. Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *Web. config* dosyasında ayarlayabilir. `CONFIG_DIR`, geliştiricinin uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı Kullanıcı tanımlı ortam değişkenine bir örnektir.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> Yalnızca `ASPNETCORE_ENVIRONMENT` ortam değişkenini, Internet gibi güvenilmeyen ağlarla erişilemeyen hazırlama ve test sunucularında `Development` olarak ayarlayın.

## <a name="app_offlinehtm"></a>app_offline. htm

Bir uygulamanın kök dizininde *app_offline. htm* adlı bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır. Uygulama, `shutdownTimeLimit` ' da tanımlanan saniye sayısından sonra hala çalışıyorsa, ASP.NET Core modülü çalışan işlemi de yok eder.

*App_offline. htm* dosyası mevcut olsa da ASP.NET Core modülü, istekleri, *app_offline. htm* dosyasının içeriğini geri göndererek yanıtlar. *App_offline. htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.

## <a name="start-up-error-page"></a>Başlatma hatası sayfası

ASP.NET Core modülü arka uç işlemini başlatamaz veya arka uç işlemi başlatılır, ancak yapılandırılmış bağlantı noktasında dinleme başarısız olursa, *502,5-Işlem hatası* durum kodu sayfası görüntülenir. Bu sayfayı bastırın ve varsayılan IIS 502 durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın. Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http hataları \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).

![502,5 işlem hatası durum kodu sayfası](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a>Günlük oluşturma ve yeniden yönlendirme

@No__t-2 öğesinin `stdoutLogEnabled` ve `stdoutLogFile` öznitelikleri ayarlandıysa ASP.NET Core modülü stdout ve stderr konsol çıkışını diske yönlendirir. @No__t-0 yolundaki klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir (yazma izni sağlamak için `IIS AppPool\<app_pool_name>` kullanın).

İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez. Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.

Stdout günlüğünün kullanılması yalnızca uygulama başlatma sorunlarını gidermek için önerilir. Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın. ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir. Günlük dosyası adı, alt çizgi ile ayrılmış `stdoutLogFile` yolunun (genellikle *stdout*) son kesimine zaman damgası, işlem kimliği ve dosya uzantısı ( *. log*) eklenerek oluşur. @No__t-0 yolu *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan bir uygulama için günlük kaydı *stdout_20180205194132_1934. log*dosya adına sahiptir.

Aşağıdaki örnek `aspNetCore` öğesi, Azure App Service barındırılan bir uygulama için stdout günlüğünü yapılandırır. Yerel günlük kaydı için bir yerel yol veya ağ paylaşımının yolu kabul edilebilir. AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

@No__t-0 değerine (önceki örnekteki*Günlükler* ) belirtilen yoldaki klasörler, modül tarafından otomatik olarak oluşturulmaz ve dağıtımda önceden var olmalıdır. Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir (yazma izni sağlamak için `IIS AppPool\<app_pool_name>` kullanın).

*Web. config* dosyasındaki `aspNetCore` öğesinin bir örneği için bkz. [Web. config ile yapılandırma](#configuration-with-webconfig) .

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır

ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır. Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.

Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır. Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine (`ASPNETCORE_TOKEN`) ayarlanır. Eşleştirme belirteci, her proxy isteğinde de bir üst bilgi (`MS-ASPNETCORE-TOKEN`) olarak ayarlanır. IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler. Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir. Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir. Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü

ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır. Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici, ' deki *ApplicationHost. config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar. paylaşma.

IIS paylaşılan yapılandırması kullanırken, şu adımları izleyin:

1. IIS paylaşılan yapılandırmasını devre dışı bırakın.
1. Yükleyiciyi çalıştırın.
1. Güncelleştirilmiş *ApplicationHost. config* dosyasını paylaşıma dışarı aktarın.
1. IIS paylaşılan yapılandırmasını yeniden etkinleştirin.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Modül sürümü ve barındırma paketi yükleyici günlükleri

Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:

1. Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.
1. *Aspnetcore. dll* dosyasını bulun.
1. Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.
1. **Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.

Modülün barındırma paketi yükleyici günlükleri *C: \\Users @ no__t-2% username% \\AppData @ no__t-4Local @ no__t-5Temp*konumunda bulunur. Dosya, *dd_DotNetCoreWinSvrHosting__ @ no__t-7timestamp > _000_Aspnetcoremodupa_x64. log*olarak adlandırılır.

## <a name="module-schema-and-configuration-file-locations"></a>Modül, şema ve yapılandırma dosyası konumları

### <a name="module"></a>Modül

**IIS (X86/AMD64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (X86/AMD64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* % ProgramFiles (x86)% \ IIS Express\aspnetcore.dll

### <a name="schema"></a>Şema

**ISS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>Yapılandırma

**ISS**

* %Windir%\System32\inetsrv\config\applicationHost,config

**IIS Express**

* Visual Studio: {APPLICATION ROOT} \\. Vs\config\applicationhost,config

* *ıısexpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Dosyalar, *ApplicationHost. config* dosyasında *aspnetcore* ' u arayarak bulunabilir.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:host-and-deploy/iis/index>
* [ASP.NET Core Module GitHub deposu (başvuru kaynağı)](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
