---
title: ASP.NET Core 'de HTTP. sys Web sunucusu uygulama
author: guardrex
description: Windows üzerinde ASP.NET Core için bir Web sunucusu olan HTTP. sys hakkında bilgi edinin. HTTP. sys çekirdek modu sürücüsü üzerine inşa edilen HTTP. sys, Kestrel için IIS olmadan doğrudan Internet bağlantısı için kullanılabilen bir alternatiftir.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/20/2019
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 5ee866c862f16c2c22539bf880b5a93415504fb1
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975512"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>ASP.NET Core 'de HTTP. sys Web sunucusu uygulama

[Tom Dykstra](https://github.com/tdykstra), [Chris](https://github.com/Tratcher), ve [Luke Latham](https://github.com/guardrex) tarafından

[Http. sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) , yalnızca Windows üzerinde çalışan [ASP.NET Core için bir Web sunucusudur](xref:fundamentals/servers/index) . HTTP. sys, [Kestrel](xref:fundamentals/servers/kestrel) Server için bir alternatiftir ve Kestrel tarafından sağlamayan bazı özellikler sunar.

> [!IMPORTANT]
> HTTP. sys [ASP.NET Core modülüyle](xref:host-and-deploy/aspnet-core-module) uyumlu DEĞILDIR ve ııs veya IIS Express ile kullanılamaz.

HTTP. sys aşağıdaki özellikleri destekler:

* [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)
* Bağlantı noktası Paylaşımı
* SNı ile HTTPS
* TLS üzerinden HTTP/2 (Windows 10 veya üzeri)
* Doğrudan dosya iletimi
* Yanıtları Önbelleğe Alma
* WebSockets (Windows 8 veya üzeri)

Desteklenen Windows sürümleri:

* Windows 7 veya üzeri
* Windows Server 2008 R2 veya üzeri

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP. sys ne zaman kullanılır

HTTP. sys, şu durumlarda olduğu dağıtımlar için yararlıdır:

* Sunucuyu IIS kullanmadan doğrudan Internet 'e sunmaya gerek vardır.

  ![HTTP. sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* İç dağıtım, [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)gibi Kestrel 'de kullanılamayan bir özelliği gerektirir.

  ![HTTP. sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

HTTP. sys pek çok tür saldırılara karşı koruyan ve tam özellikli bir Web sunucusu için sağlamlık, güvenlik ve ölçeklenebilirlik sağlayan çok sayıda teknolojiden oluşur. IIS, HTTP. sys ' nin üstünde HTTP dinleyicisi olarak çalışır.

## <a name="http2-support"></a>HTTP/2 desteği

Aşağıdaki temel gereksinimler karşılanıyorsa ASP.NET Core uygulamalar için [http/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:

* Windows Server 2016/Windows 10 veya üzeri
* [Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1.2 veya sonraki bir bağlantı

::: moniker range=">= aspnetcore-2.2"

Bir HTTP/2 bağlantı kurulur, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/2`.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Bir HTTP/2 bağlantı kurulur, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/1.1`.

::: moniker-end

HTTP/2 varsayılan olarak etkindir. HTTP/2 bağlantısı kurulmadıysa, bağlantı HTTP/1.1 'ye geri döner. Windows 'un gelecek bir sürümünde http/2 yapılandırma bayrakları http/2 ' yi HTTP. sys ile devre dışı bırakma özelliği de dahil olmak üzere kullanılabilir olacaktır.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kerberos ile çekirdek modu kimlik doğrulaması

Çekirdek modu kimlik doğrulaması Kerberos kimlik doğrulama protokolü HTTP.sys temsil eder. Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP.sys ile desteklenmez. Makine hesabı Kerberos belirteci/Active Directory'den elde edilen anahtar şifresini çözmek için kullanılan ve kullanıcının kimliğini doğrulamak için istemcinin sunucuya iletilir. Hizmet asıl adı (SPN) konak için değil uygulamanın kullanıcı kaydedin.

## <a name="how-to-use-httpsys"></a>HTTP. sys kullanma

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>ASP.NET Core uygulamasını HTTP. sys kullanacak şekilde yapılandırma

1. [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) (ASP.NET Core 2,1 veya üzeri) kullanılırken proje dosyasındaki bir paket başvurusu gerekli değildir. `Microsoft.AspNetCore.App` Metapackage 'i kullanmıyorsanız [Microsoft. aspnetcore. Server. httpsys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)öğesine bir paket başvurusu ekleyin.

2. Ana bilgisayarı oluştururken, gerekli <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>herhangi bir yöntemi belirterek uzantıyönteminiçağırın.<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> Aşağıdaki örnek, seçeneklerini varsayılan değerlerine ayarlar:

::: moniker range=">= aspnetcore-3.0"

   ```csharp
   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .UseStartup<Startup>()
          .UseHttpSys(options =>
          {
              options.AllowSynchronousIO = false;
              options.Authentication.Schemes = AuthenticationSchemes.None;
              options.Authentication.AllowAnonymous = true;
              options.MaxConnections = null;
              options.MaxRequestBodySize = 30000000;
              options.UrlPrefixes.Add("http://localhost:5000");
          });
   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

   [!code-csharp[](httpsys/sample/Program.cs?name=snippet1&highlight=4-12)]

::: moniker-end

   Ek HTTP. sys yapılandırması, [kayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)aracılığıyla işlenir.

   **HTTP. sys seçenekleri**

::: moniker range=">= aspnetcore-3.0"

   | Özellik | Açıklama | Varsayılan |
   | -------- | ----------- | :-----: |
   | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | `HttpContext.Request.Body` Ve`HttpContext.Response.Body`için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini denetleyin. | `false` |
   | [Authentication. AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Anonim isteklere izin verin. | `true` |
   | [Authentication. düzenleri](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | İzin verilen kimlik doğrulama düzenlerini belirtin. Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir. Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic`, `Negotiate` `Kerberos`, `None`,, ve `NTLM`. | `None` |
   | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin. Yanıt, `Set-Cookie` `Vary`veya üstbilgileriiçermeyebilir.`Pragma` `Cache-Control` Bir`shared-max-age`ya dadeğeri`Expires` ya da bir üst bilgi içermelidir. `max-age` `public` | `true` |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | En fazla eşzamanlı kabul sayısı. | 5 &times; [ortam.<br> ProcessorCount](xref:System.Environment.ProcessorCount) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı. Sonsuz `-1` için kullanın. Kayıt `null` defterinin makine genelindeki ayarını kullanmak için kullanın. | `null`<br>sayısız |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın. | 30000000 bayt<br>(~ 28,6 MB) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Sıraya alınabilen en fazla istek sayısı. | 1000 |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin. | `false`<br>(normal olarak) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Kayıt defterinde da yapılandırılabilen http <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> . sys yapılandırmasını kullanıma sunun. Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:<ul><li>HTTP sunucusu API 'sinin varlık gövdesini etkin tut bağlantısı üzerinde boşaltmasına izin verilen [TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; süresi.</li><li>[TimeoutManager. entitybody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; zamanına, istek varlığı gövdesinin gelmesi için izin verildi.</li><li>HTTP sunucusu API 'sinin istek üst bilgisini ayrıştırması için [TimeoutManager. headerwaıt](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; zamanına izin verilir.</li><li>[TimeoutManager.](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; boştaki bir bağlantı için ıdletimeout bağlantı zamanına izin verilir.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; yanıt için en düşük gönderme hızı.</li><li>[TimeoutManager.](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; isteğin, istek sırasında, uygulamanın onu çekmeden önce kalmasına izin verilen RequestQueue süresi.</li></ul> |  |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | HTTP. sys ile kaydolmak içinöğesinibelirtin.<xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> En yararlı olan [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir ön ek eklemek için kullanılır. Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir. |  |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

   | Özellik | Açıklama | Varsayılan |
   | -------- | ----------- | :-----: |
   | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | `HttpContext.Request.Body` Ve`HttpContext.Response.Body`için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini denetleyin. | `true` |
   | [Authentication. AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Anonim isteklere izin verin. | `true` |
   | [Authentication. düzenleri](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | İzin verilen kimlik doğrulama düzenlerini belirtin. Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir. Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic`, `Negotiate` `Kerberos`, `None`,, ve `NTLM`. | `None` |
   | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin. Yanıt, `Set-Cookie` `Vary`veya üstbilgileriiçermeyebilir.`Pragma` `Cache-Control` Bir`shared-max-age`ya dadeğeri`Expires` ya da bir üst bilgi içermelidir. `max-age` `public` | `true` |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | En fazla eşzamanlı kabul sayısı. | 5 &times; [ortam.<br> ProcessorCount](xref:System.Environment.ProcessorCount) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı. Sonsuz `-1` için kullanın. Kayıt `null` defterinin makine genelindeki ayarını kullanmak için kullanın. | `null`<br>sayısız |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın. | 30000000 bayt<br>(~ 28,6 MB) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Sıraya alınabilen en fazla istek sayısı. | 1000 |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin. | `false`<br>(normal olarak) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Kayıt defterinde da yapılandırılabilen http <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> . sys yapılandırmasını kullanıma sunun. Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:<ul><li>HTTP sunucusu API 'sinin varlık gövdesini etkin tut bağlantısı üzerinde boşaltmasına izin verilen [TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; süresi.</li><li>[TimeoutManager. entitybody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; zamanına, istek varlığı gövdesinin gelmesi için izin verildi.</li><li>HTTP sunucusu API 'sinin istek üst bilgisini ayrıştırması için [TimeoutManager. headerwaıt](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; zamanına izin verilir.</li><li>[TimeoutManager.](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; boştaki bir bağlantı için ıdletimeout bağlantı zamanına izin verilir.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; yanıt için en düşük gönderme hızı.</li><li>[TimeoutManager.](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; isteğin, istek sırasında, uygulamanın onu çekmeden önce kalmasına izin verilen RequestQueue süresi.</li></ul> |  |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | HTTP. sys ile kaydolmak içinöğesinibelirtin.<xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> En yararlı olan [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir ön ek eklemek için kullanılır. Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir. |  |

::: moniker-end

   <a name="maxrequestbodysize"></a>

   **MaxRequestBodySize**

   Herhangi bir istek gövdesinin bayt olarak izin verilen en büyük boyutu. Olarak `null`ayarlandığında, en büyük istek gövdesi boyutu sınırsızdır. Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantıları üzerinde hiçbir etkisi yoktur.

   Tek `IActionResult` bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yöntem, bir eylem yönteminde <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> özniteliğini kullanmaktır:

   ```csharp
   [RequestSizeLimit(100000000)]
   public IActionResult MyActionMethod()
   ```

   Uygulama isteği okumayı başlattıktan sonra bir istek üzerindeki sınırı yapılandırmayı denerse bir özel durum oluşur. Özelliğin `IsReadOnly` salt okuma durumunda olup `MaxRequestBodySize` olmadığını göstermek için bir özellik kullanılabilir, yani sınırı yapılandırmak için çok geç.

   Uygulamanın istek başına geçersiz kılması <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> gerekiyorsa, <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>şunu kullanın:

   [!code-csharp[](httpsys/sample/Startup.cs?name=snippet1&highlight=6-7)]

3. Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express çalıştıracak şekilde yapılandırılmadığından emin olun.

   Visual Studio 'da varsayılan başlatma profili IIS Express içindir. Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:

   ![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Windows Server 'ı yapılandırma

1. Uygulamanın açmak için bağlantı noktalarını belirleme ve [Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 'Nı veya [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell CMDLET 'ini kullanarak trafiğin http. sys ' ye ulaşmasını sağlamak için güvenlik duvarı bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.

1. Bir Azure sanal makinesine dağıtım yaparken, [ağ güvenlik grubundaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.

1. Gerekirse, X. 509.440 sertifikalarını edinin ve yükler.

   Windows 'da, [New-SelfSignedCertificate PowerShell cmdlet 'ini](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak otomatik olarak imzalanan sertifikalar oluşturun. Desteklenmeyen bir örnek için bkz [. UpdateIISExpressSSLForChrome. ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Sunucunun **yerel makine** > **Kişisel** deposunda otomatik olarak imzalanan veya CA imzalı sertifikalar yükler.

1. Uygulama [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise, .net core, .NET Framework veya her ikisini de (uygulama .NET Framework hedefleyen bir .NET Core uygulaması ise) yükler.

   * **.NET Core** Uygulama .NET Core gerektiriyorsa .NET Core [indirmelerinde](https://dotnet.microsoft.com/download) **.NET Core çalışma zamanı** yükleyicisini edinin ve çalıştırın. &ndash; Tam SDK 'Yı sunucuya yüklemeyin.
   * **.NET Framework** Uygulama .NET Framework gerektiriyorsa, [.NET Framework yükleme kılavuzuna](/dotnet/framework/install/)bakın. &ndash; Gerekli .NET Framework yüklemesi. En son .NET Framework yükleyicisi [.NET Core İndirmeleri](https://dotnet.microsoft.com/download) sayfasından edinilebilir.

   Uygulama, [kendi içinde bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise, uygulama çalışma zamanını dağıtımda içerir. Sunucuda çerçeve yüklemesi gerekmez.

1. Uygulamada URL 'Leri ve bağlantı noktalarını yapılandırın.

   Varsayılan olarak, ASP.NET Core öğesine `http://localhost:5000`bağlanır. URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunları içerir:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`komut satırı bağımsız değişkeni
   * `ASPNETCORE_URLS`ortam değişkeni
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Aşağıdaki kod örneği, 443 numaralı bağlantı noktasında <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> sunucunun yerel IP adresi `10.0.0.4` ile nasıl kullanılacağını göstermektedir:

   [!code-csharp[](httpsys/sample_snapshot/Program.cs?name=snippet1&highlight=6)]

   Avantajı `UrlPrefixes` , hatalı biçimli ön ekler için hemen bir hata iletisi oluşturulmasından oluşur.

   `UseUrls` Ayarlarıgeçersizkıl`urls` . / `UrlPrefixes` / `ASPNETCORE_URLS` Bu nedenle, ve `UseUrls` `ASPNETCORE_URLS` ortam değişkeninin `urls`avantajı Kestrel ve http. sys arasında geçiş yapmak daha kolay olabilir.

   HTTP. sys, [http sunucusu API UrlPrefix dize biçimlerini](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)kullanır.

   > [!WARNING]
   > Üst düzey joker bağlamaları (`http://*:80/` ve `http://+:80`) gereken **değil** kullanılır. Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıklarını oluşturur. Bu, güçlü ve zayıf joker karakterler için geçerlidir. Joker karakterler yerine açık konak adlarını veya IP adreslerini kullanın. Alt etki alanı joker bağlantısı (örneğin `*.mysub.com`,), tüm üst etki alanını (Bu güvenlik açığı olan `*.com`aksine) kontrol ediyorsanız bir güvenlik riski değildir. Daha fazla bilgi için bkz [. RFC 7230: Bölüm 5,4: Ana](https://tools.ietf.org/html/rfc7230#section-5.4)bilgisayar.

1. Ön ek, sunucudaki URL öneklerini ister.

   HTTP. sys ' yi yapılandırmaya yönelik yerleşik araç *netsh. exe*' dir. *netsh. exe* , URL öneklerini ayırmak ve X. 509.440 sertifikaları atamak için kullanılır. Araç, yönetici ayrıcalıkları gerektirir.

   Uygulamanın URL 'Lerini kaydettirmek için *netsh. exe* aracını kullanın:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; Tam nitelikli Tekdüzen Kaynak Bulucu (URL). Joker karakter bağlama kullanmayın. Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın. *URL, sondaki eğik çizgi içermelidir.*
   * `<USER>`&ndash; Kullanıcı veya Kullanıcı grubu adını belirtir.

   Aşağıdaki örnekte sunucusunun `10.0.0.4`yerel IP adresi:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Bir URL kaydedildiğinde, araç ile `URL reservation successfully added`yanıt verir.

   Kayıtlı bir URL 'yi silmek için şu `delete urlacl` komutu kullanın:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. X. 509.440 sertifikalarını sunucusuna kaydedin.

   Uygulamanın sertifikalarını kaydetmek için *netsh. exe* aracını kullanın:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Bağlama için yerel IP adresini belirtir. Joker karakter bağlama kullanmayın. Geçerli bir IP adresi kullanın.
   * `<PORT>`&ndash; Bağlama için bağlantı noktasını belirtir.
   * `<THUMBPRINT>`&ndash; X. 509.440 sertifikası parmak izi.
   * `<GUID>`&ndash; Uygulamayı bilgilendirme amacıyla temsil eden geliştirici tarafından oluşturulan GUID.

   Başvuru amacıyla, GUID 'yi uygulamada bir paket etiketi olarak depolayın:

   * Visual Studio'da:
     * **Çözüm Gezgini** ' de uygulamaya sağ tıklayıp **Özellikler**' i seçerek uygulamanın proje özelliklerini açın.
     * **Paket** sekmesini seçin.
     * **Etiketler** alanında oluşturduğunuz GUID 'yi girin.
   * Visual Studio kullanmadığınız durumlarda:
     * Uygulamanın proje dosyasını açın.
     * Oluşturduğunuz GUID ile yeni veya var olan `<PropertyGroup>` bir özelliğiekleyin:`<PackageTags>`

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Aşağıdaki örnekte:

   * Sunucunun yerel IP adresi `10.0.0.4`.
   * Bir çevrimiçi rastgele GUID Oluşturucu `appid` değeri sağlar.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Bir sertifika kaydedildiğinde, araç ile `SSL Certificate successfully added`yanıt verir.

   Bir sertifika kaydını silmek için şu `delete sslcert` komutu kullanın:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   *Netsh. exe*için başvuru belgeleri:

   * [Köprü Metni Aktarım Protokolü (HTTP) için Netsh komutları](https://technet.microsoft.com/library/cc725882.aspx)
   * [UrlPrefix dizeleri](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Uygulamayı çalıştırın.

   1024 'den büyük bir bağlantı noktası numarası ile HTTP (HTTPS değil) kullanarak localhost 'a bağlanırken uygulamayı çalıştırmak için yönetici ayrıcalıklarına gerek yoktur. Diğer yapılandırmalarda (örneğin, yerel bir IP adresi veya 443 numaralı bağlantı noktasına bağlama), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.

   Uygulama, sunucunun genel IP adresinde yanıt verir. Bu örnekte, sunucusuna, genel IP adresinde `104.214.79.47`Internet 'ten ulaşılırsa.

   Bu örnekte bir geliştirme sertifikası kullanılır. Tarayıcının güvenilmeyen sertifika uyarısı atlandıktan sonra sayfa güvenli bir şekilde yüklenir.

   ![Uygulamanın dizin sayfasını yüklü olarak gösteren tarayıcı penceresi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Ara sunucu ve yük dengeleyici senaryoları

Internet veya şirket ağından gelen isteklerle etkileşime geçen HTTP. sys tarafından barındırılan uygulamalar için, proxy sunucularının ve yük dengeleyiciler 'nin arkasında barındırılırken ek yapılandırma gerekebilir. Daha fazla bilgi için [proxy sunucuları ile çalışma ve yük Dengeleyiciler için ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Ek kaynaklar

* [HTTP. sys ile Windows kimlik doğrulamasını etkinleştirme](xref:security/authentication/windowsauth#httpsys)
* [HTTP Sunucusu API 'SI](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [ASPNET/HttpSysServer GitHub deposu (kaynak kodu)](https://github.com/aspnet/HttpSysServer/)
* [Ana bilgisayar](xref:fundamentals/index#host)
* <xref:test/troubleshoot>
