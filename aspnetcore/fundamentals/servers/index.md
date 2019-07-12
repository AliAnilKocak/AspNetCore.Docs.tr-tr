---
title: ASP.NET Core Web sunucu uygulamalarında
author: guardrex
description: ASP.NET Core için web sunucuları Kestrel ve HTTP.sys keşfedin. Bir sunucu seçin ve ne zaman bir ters proxy sunucusu kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/01/2019
uid: fundamentals/servers/index
ms.openlocfilehash: 10876a61d40679b1a022ce9c58329bf53c36c1bb
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855975"
---
# <a name="web-server-implementations-in-aspnet-core"></a>ASP.NET Core Web sunucu uygulamalarında

Tarafından [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), ve [Chris Ross](https://github.com/Tratcher)

ASP.NET Core uygulaması bir işlemde HTTP sunucusu uygulamasını ile çalışır. HTTP istekleri ve bunları uygulamaya bir dizi ortaya çıkarır sunucusu uygulaması dinler [istek özellikleri](xref:fundamentals/request-features) içine oluşan bir <xref:Microsoft.AspNetCore.Http.HttpContext>.

## <a name="kestrel"></a>Kestrel

Kestrel'i ASP.NET Core proje şablonları dahil varsayılan web sunucusudur.

Kestrel'i kullanın:

* Tek başına bir uç sunucusu olarak doğrudan Internet dahil olmak üzere, bir ağ isteği işleniyor.

  ![Kestrel'i ters Ara sunucu olmadan Internet ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

* İle bir *ters Ara sunucu*, gibi [Internet Information Services (IIS)](https://www.iis.net/), [Ngınx](https://nginx.org), veya [Apache](https://httpd.apache.org/). Bir tersine Ara sunucunun Internet'ten HTTP isteklerini alır ve bunları Kestrel için iletir.

  ![Kestrel'i dolaylı olarak IIS, Ngınx veya Apache gibi bir ters Ara sunucu üzerinden Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet.png)

Barındırma ya da yapılandırma&mdash;ile veya ters Ara sunucu olmadan&mdash;ASP.NET Core 2.1 veya daha sonraki uygulamalar için desteklenir.

Kestrel'i Yapılandırma Kılavuzu ve ne zaman bir ters proxy yapılandırma Kestrel kullanılacağı hakkında bilgi için bkz. <xref:fundamentals/servers/kestrel>.

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core aşağıdaki ile birlikte gelir:

* [Kestrel'i sunucu](xref:fundamentals/servers/kestrel) platformlar arası HTTP sunucusu uygulamasını, varsayılan olan.
* IIS HTTP sunucusu bir [işlem sunucusu](#hosting-models) IIS için.
* [HTTP.sys sunucu](xref:fundamentals/servers/httpsys) yalnızca Windows HTTP sunucu dayanır [HTTP.sys çekirdek sürücüsü ve HTTP Sunucusu API](/windows/desktop/Http/http-api-start-page).

Kullanırken [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), uygulama ya da çalışır:

* IIS çalışan işlemi ile aynı işlemde ( [işlem içi barındırma modeli](#hosting-models)) IIS HTTP sunucusuyla. *İşlem içi* önerilen yapılandırmadır.
* Bir işlemde ayrı IIS çalışan işleminden ( [işlem dışı barındırma modeli](#hosting-models)) ile [Kestrel sunucu](#kestrel).

[ASP.NET Core Modülü](xref:host-and-deploy/aspnet-core-module) IIS ve işlem içi IIS HTTP sunucusu veya Kestrel arasında yerel IIS istekleri işleyen yerel bir IIS modülüdür. Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.

## <a name="hosting-models"></a>Barındırma modelleri

İşlemdeki barındırma, bir ASP.NET Core kullanarak uygulama IIS çalışan işlemi ile aynı işlemde çalıştırır. Barındırma işlemi içinde istekleri geri döngü bağdaştırıcı, giden ağ trafiğini aynı makinede geri döndüren bir ağ arabirimi üzerinden proxy olmadığından işlem dışı barındırma üzerinden geliştirilmiş performans sağlar. IIS işleme Süreci Yönetimi ile [Windows İşlem Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Barındırma işlemi çıkış kullanarak ASP.NET Core uygulamaları IIS çalışan işlemi ve modül tanıtıcıları işlem Yönetimi'nden ayrı bir işlem içinde çalıştırın. İlk istek ulaştığında ve kapatılır veya çöküyor uygulama yeniden başlatmalarını modülü ASP.NET Core uygulaması için bir işlem başlar. Bu aslında aynı işlemde çalışan tarafından yönetilen uygulamalarla görüldüğü gibi davranıştır [Windows İşlem Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Daha fazla bilgi ve yapılandırma yönergeleri için aşağıdaki konulara bakın:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core ile birlikte gelir [Kestrel sunucu](xref:fundamentals/servers/kestrel), platformlar arası bir HTTP sunucusu, varsayılan olan.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core ile birlikte gelir [Kestrel sunucu](xref:fundamentals/servers/kestrel), platformlar arası bir HTTP sunucusu, varsayılan olan.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core aşağıdaki ile birlikte gelir:

* [Kestrel'i sunucu](xref:fundamentals/servers/kestrel) olduğundan varsayılan, platformlar arası bir HTTP sunucusu.
* [HTTP.sys sunucu](xref:fundamentals/servers/httpsys) yalnızca Windows HTTP sunucu dayanır [HTTP.sys çekirdek sürücüsü ve HTTP Sunucusu API](/windows/desktop/Http/http-api-start-page).

Kullanırken [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), uygulama IIS çalışan işleminden ayrı bir işlemde çalışır (*işlem dışı*) ile [Kestrel sunucu](#kestrel).

Bir işlem içinde çalıştırmak, ASP.NET Core uygulamaları IIS çalışan işleminden ayrı olduğundan, işlem yönetimi modül işler. İlk istek ulaştığında ve kapatılır veya çöküyor uygulama yeniden başlatmalarını modülü ASP.NET Core uygulaması için bir işlem başlar. Bu aslında aynı işlemde çalışan tarafından yönetilen uygulamalarla görüldüğü gibi davranıştır [Windows İşlem Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Aşağıdaki diyagram IIS, ASP.NET Core modülü arasındaki ilişkiyi gösterir ve uygulama barındırılan işlem dışı:

![ASP.NET Core Modülü](_static/ancm-outofprocess.png)

İstekleri için çekirdek modu HTTP.sys sürücüsünü Web'den ulaşır. Sürücü istekler IIS Web sitesinin yapılandırılan bağlantı noktası, genellikle 80 (HTTP) veya 443 (HTTPS) üzerinde yönlendirir. Modül Kestrel rastgele bağlantı noktası için 80 veya 443 bağlantı noktası olmadığından uygulama isteklerini iletir.

Modül, başlangıçta bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) dinleyecek şekilde yapılandırır `http://localhost:{port}`. Ek denetimler gerçekleştirilir ve modülünden değilsiniz kaynaklı istekler reddedilir. İstekler HTTP üzerinden HTTPS üzerinden IIS tarafından alınan bile iletilir modülü HTTPS iletmeyi desteklemez.

Modül istekten Kestrel seçer sonra ASP.NET Core ara yazılım ardışık düzende isteği gönderilir. Ara yazılım ardışık düzenini isteği işler ve olarak geçirir bir `HttpContext` örneği uygulama mantığına. IIS tümleştirme tarafından eklenen bir ara yazılım istek için Kestrel iletmek için hesap için şema, uzak IP ve pathbase güncelleştirir. Uygulamanın yanıt IIS, yeniden istek başlatılan HTTP istemcisi için hangi bildirim geçirilir.

IIS ve ASP.NET Core Module yapılandırma yönergeleri için aşağıdaki konulara bakın:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core ile birlikte gelir [Kestrel sunucu](xref:fundamentals/servers/kestrel), platformlar arası bir HTTP sunucusu, varsayılan olan.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core ile birlikte gelir [Kestrel sunucu](xref:fundamentals/servers/kestrel), platformlar arası bir HTTP sunucusu, varsayılan olan.

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Ngınx Kestrel ile

Ngınx Linux üzerinde bir ters proxy sunucusu olarak Kestrel için nasıl kullanılacağı hakkında daha fazla bilgi için bkz: <xref:host-and-deploy/linux-nginx>.

### <a name="apache-with-kestrel"></a>Kestrel'i Apache

Apache Linux üzerinde bir ters proxy sunucusu olarak Kestrel için nasıl kullanılacağı hakkında daha fazla bilgi için bkz: <xref:host-and-deploy/linux-apache>.

## <a name="httpsys"></a>HTTP.sys

Windows üzerinde çalışan ASP.NET Core uygulamaları, HTTP.sys Kestrel alternatif olur. Kestrel'i genellikle en iyi performans için önerilir. HTTP.sys burada uygulama Internet erişimine açıktır ve gerekli özellikleri HTTP.sys ancak değil Kestrel tarafından desteklenen senaryolarda kullanılabilir. Daha fazla bilgi için bkz. <xref:fundamentals/servers/httpsys>.

![HTTP.sys doğrudan Internet ile iletişim kurar.](httpsys/_static/httpsys-to-internet.png)

HTTP.sys, yalnızca iç ağa sunulan uygulamalar için de kullanılabilir.

![HTTP.sys iç ağa ile doğrudan iletişim kurar.](httpsys/_static/httpsys-to-internal.png)

HTTP.sys yapılandırma yönergeleri için bkz <xref:fundamentals/servers/httpsys>.

## <a name="aspnet-core-server-infrastructure"></a>ASP.NET Core sunucu altyapısı

<xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> Bulunan `Startup.Configure` yöntemi kullanıma sunan <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> türünün özelliği <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>. Kestrel ve HTTP.sys yalnızca tek bir özelliği her kullanıma <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, ancak farklı bir sunucu uygulamaları, ek işlevler sunarsınız.

`IServerAddressesFeature` Sunucu Uygulama çalışma zamanında bağlı bağlantı noktasını bulmak için kullanılabilir.

## <a name="custom-servers"></a>Özel sunucuları

Özel sunucu uygulaması, yerleşik sunucuları uygulamanın gereksinimleri karşılamıyorsanız oluşturulabilir. [.NET (OWIN) kılavuzu için açık Web arabirimi](xref:fundamentals/owin) nasıl yazılacağını gösteren bir [Nowin](https://github.com/Bobris/Nowin)-tabanlı <xref:Microsoft.AspNetCore.Hosting.Server.IServer> uygulaması. Uygulamanın kullandığı özellik arabirimler uygulama, en az olsa gerektirir. yalnızca <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> ve <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> desteklenmesi gerekir.

## <a name="server-startup"></a>Sunucu başlangıç

Sunucu tümleşik geliştirme ortamı (IDE) veya düzenleyicide uygulama başlatıldığında başlatılır:

* [Visual Studio](https://visualstudio.microsoft.com) &ndash; başlatma profilleri, uygulama ve sunucu ile başlatmak için kullanılabilir [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Modülü](xref:host-and-deploy/aspnet-core-module) veya Konsolu.
* [Visual Studio Code](https://code.visualstudio.com/) &ndash; uygulama ve sunucu başlatılır [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), CoreCLR hata ayıklayıcı etkinleştirir.
* [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/) &ndash; uygulama ve sunucu başlatılır [Mono modu geçici hata ayıklayıcı](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).

Proje klasöründeki bir komut isteminden uygulamayı başlatırken [çalıştırma dotnet](/dotnet/core/tools/dotnet-run) uygulama ve sunucu (Kestrel ve yalnızca HTTP.sys) başlatır. Yapılandırması tarafından belirtilen `-c|--configuration` ayarlandığından seçeneği `Debug` (varsayılan) veya `Release`. Başlatma profili varsa bir *launchSettings.json* dosya, kullanın `--launch-profile <NAME>` başlatma profili ayarlamak için seçeneği (örneğin, `Development` veya `Production`). Daha fazla bilgi için [çalıştırma dotnet](/dotnet/core/tools/dotnet-run) ve [.NET Core dağıtımı paketleme](/dotnet/core/build/distribution-packaging).

## <a name="http2-support"></a>HTTP/2 desteği

[HTTP/2](https://httpwg.org/specs/rfc7540.html) ile ASP.NET Core aşağıdaki dağıtım senaryolarında desteklenir:

::: moniker range=">= aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * İşletim sistemi
    * Windows Server 2016/Windows 10 veya üzeri&dagger;
    * Linux OpenSSL 1.0.2 veya daha sonra (örneğin, Ubuntu 16.04 veya üzeri)
    * HTTP/2 macos'ta gelecek sürümlerde desteklenecektir.
  * Hedef çerçeve: .NET Core 2.2 veya üzeri
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 veya üzeri
  * Hedef çerçeve: HTTP.sys dağıtımlar için geçerli değildir.
* [IIS (işlem içi)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri
  * Hedef çerçeve: .NET Core 2.2 veya üzeri
* [IIS (giden işlem)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri
  * HTTP/2 genel kullanıma yönelik uç sunucu bağlantılarını kullanın, ancak HTTP/1.1 Kestrel ters proxy bağlantı kullanır.
  * Hedef çerçeve: IIS işlem dışı dağıtımlar için geçerli değildir.

&dagger;Kestrel'i HTTP/2 Windows Server 2012 R2 ve Windows 8.1 için destek sınırlıdır. Bu işletim sistemlerinde desteklenen TLS şifre paketlerinin listesini sınırlı olduğundan destek sınırlıdır. Bir Eliptik Eğri Dijital imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika, TLS bağlantıları güvenli hale getirmek için gerekebilir.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 veya üzeri
  * Hedef çerçeve: HTTP.sys dağıtımlar için geçerli değildir.
* [IIS (giden işlem)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri
  * HTTP/2 genel kullanıma yönelik uç sunucu bağlantılarını kullanın, ancak HTTP/1.1 Kestrel ters proxy bağlantı kullanır.
  * Hedef çerçeve: IIS işlem dışı dağıtımlar için geçerli değildir.

::: moniker-end

Bir HTTP/2 bağlantı kullanmalısınız [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ve TLS 1.2 veya sonraki bir sürümü. Daha fazla bilgi için sunucu dağıtım senaryolarınız için ilgili konulara bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
