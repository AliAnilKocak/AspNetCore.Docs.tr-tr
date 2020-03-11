---
title: Azure App Service ve IIS 'de ASP.NET Core sorunlarını giderme
author: rick-anderson
description: ASP.NET Core uygulamalarının Azure App Service ve Internet Information Services (IIS) dağıtımlarıyla ilgili sorunları tanılamayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 671f68da2ea261cb8ae32a9d5ef875217859054d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655332"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a>Azure App Service ve IIS 'de ASP.NET Core sorunlarını giderme

, [Kotin Kotalik](https://github.com/jkotalik) tarafından

::: moniker range=">= aspnetcore-3.0"

Bu makalede, bir uygulama Azure App Service veya IIS 'ye dağıtıldığında hataların nasıl tanılanacağı hakkında genel uygulama başlatma hataları ve yönergeleri hakkında bilgi verilmektedir:

[Uygulama başlatma hataları](#app-startup-errors)  
Ortak Başlangıç HTTP durum kodu senaryolarını açıklar.

[Azure App Service sorunlarını giderme](#troubleshoot-on-azure-app-service)  
Azure App Service dağıtılan uygulamalar için sorun giderme önerisi sağlar.

[IIS üzerinde sorun giderme](#troubleshoot-on-iis)  
IIS 'ye dağıtılan veya IIS Express yerel olarak çalışan uygulamalar için sorun giderme önerisi sağlar. Bu kılavuz hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.

[Paket önbelleklerini temizle](#clear-package-caches)  
Önemli güncelleştirmeler gerçekleştirirken veya paket sürümlerini değiştirirken ne yapmanız gerektiğini açıklar.

[Ek Kaynaklar](#additional-resources)  
Ek sorun giderme konularını listeler.

## <a name="app-startup-errors"></a>Uygulama başlatma hataları

Visual Studio 'da bir ASP.NET Core projesi, hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırmak için varsayılan değerdir. *502,5-Işlem hatası* veya yerel olarak hata ayıklarken oluşan *500,30-başlatma hatası* , bu konudaki öneri kullanılarak tanılanabilir.

### <a name="40314-forbidden"></a>403,14 yasak

Uygulama başlatılamıyor. Aşağıdaki hata günlüğe kaydedilir:

```
The Web server is configured to not list the contents of this directory.
```

Hata genellikle barındırma sisteminde, aşağıdaki senaryolardan birini içeren bozuk bir dağıtım nedeniyle oluşur:

* Uygulama, barındırma sisteminde yanlış klasöre dağıtılır.
* Dağıtım işlemi, uygulamanın tüm dosyalarını ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.
* *Web. config* dosyası dağıtımda yok veya *Web. config* dosyası içerikleri hatalı biçimlendirilmiş.

Aşağıdaki adımları uygulayın:

1. Tüm dosya ve klasörleri barındırma sistemindeki dağıtım klasöründen silin.
1. Visual Studio, PowerShell veya el ile dağıtım gibi normal dağıtım yönteminizi kullanarak, uygulamanın *Yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:
   * *Web. config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.
   * Azure App Service barındırırken, uygulamanın `D:\home\site\wwwroot` klasörüne dağıtıldığını doğrulayın.
   * Uygulama IIS tarafından barındırılıyorsa, uygulamanın **IIS yöneticisinin** **temel ayarlarında**gösterilen IIS **fiziksel yoluna** dağıtıldığını doğrulayın.
1. Barındırma sistemindeki dağıtımı projenin *Yayımla* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtıldığını doğrulayın.

Yayımlanan ASP.NET Core uygulamasının düzeni hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/directory-structure>. *Web. config* dosyası hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.

### <a name="500-internal-server-error"></a>500 İç Sunucu Hatası

Uygulamayı başlatır, ancak bir hata sunucu isteği yerine getirmesini önler.

Bu hata, başlatma sırasında veya bir yanıt oluşturulurken uygulamanın kod içinde oluşur. Yanıtta içerik yok olabilir veya Yanıt, tarayıcıda *500 Iç sunucu hatası* olarak görünebilir. Uygulama olay günlüğü, genellikle uygulama normal şekilde çalışmaya belirtir. Sunucunun açısından bakıldığında, doğru olmasıdır. Uygulama başladı, ancak geçerli bir yanıt oluşturulamıyor. Uygulamayı sunucuda bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Core modülü stdout günlüğünü etkinleştirin.

### <a name="5000-in-process-handler-load-failure"></a>500.0 işlem içi işleyici yükleme hatası

Çalışan işlemi başarısız olur. Uygulama başlamaz.

[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) bileşenleri yüklenirken bilinmeyen bir hata oluştu. Aşağıdaki eylemlerden birini gerçekleştirin:

* [Microsoft desteği](https://support.microsoft.com/oas/default.aspx?prid=15832) iletişim kurun ( **Geliştirici Araçları** ve **ASP.NET Core**' i seçin).
* Stack Overflow soru sorun.
* [GitHub deponuzda](https://github.com/dotnet/AspNetCore)bir sorun yapın.

### <a name="50030-in-process-startup-failure"></a>500.30 işlemdeki başlatma hatası

Çalışan işlemi başarısız olur. Uygulama başlamaz.

[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR 'yi işlem içi başlatmaya çalışır, ancak başlatılamıyor. İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.

Yaygın hata koşulları:

* Mevcut olmayan ASP.NET Core paylaşılan çerçevesinin bir sürümünün hedeflenmesi nedeniyle uygulama yanlış yapılandırılmış. Hangi sürümlerinin bir ASP.NET Core paylaşılan çerçeve hedef makinede yüklü olduğunu denetleyin.
* Azure Key Vault kullanarak Key Vault izinlerin bulunmaması. Doğru izinlerin verildiğinden emin olmak için hedeflenen Key Vault erişim ilkelerini kontrol edin.

### <a name="50031-ancm-failed-to-find-native-dependencies"></a>500,31 ANCM yerel bağımlılıklar bulunamadı

Çalışan işlemi başarısız olur. Uygulama başlamaz.

[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) , .NET Core çalışma zamanını işlem içinde başlatmaya çalışır, ancak başlatılamıyor. Bu başlatma hatasının en yaygın nedeni, `Microsoft.NETCore.App` veya `Microsoft.AspNetCore.App` çalışma zamanının yüklenmemesine neden olur. Uygulama, hedef ASP.NET Core 3,0 ' ye dağıtılmışsa ve bu sürüm makinede yoksa, bu hata oluşur. Örnek bir hata iletisi aşağıda verilmiştir:

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

Hata iletisi, yüklü tüm .NET Core sürümlerini ve uygulama tarafından istenen sürümü listeler. Bu hatayı onarmak için aşağıdakilerden birini yapın:

* Uygun .NET Core sürümünü makineye yükler.
* Uygulamayı, makinede bulunan .NET Core 'un bir sürümünü hedefleyecek şekilde değiştirin.
* Uygulamayı [kendi kendine kapsanan bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)olarak yayımlayın.

Geliştirme aşamasında çalışırken (`ASPNETCORE_ENVIRONMENT` ortam değişkeni `Development`olarak ayarlandığında), HTTP yanıtına belirli bir hata yazılır. İşlem başlatma hatasının nedeni uygulama olay günlüğünde de bulunur.

### <a name="50032-ancm-failed-to-load-dll"></a>500,32 ANCM dll yüklenemedi

Çalışan işlemi başarısız olur. Uygulama başlamaz.

Bu hatanın en yaygın nedeni, uygulamanın uyumsuz bir işlemci mimarisi için yayımlanmakta olması olabilir. Çalışan işlemi 32 bitlik bir uygulama olarak çalışıyorsa ve uygulama 64 bit hedef için yayımlandıysa, bu hata oluşur.

Bu hatayı onarmak için aşağıdakilerden birini yapın:

* Çalışan işlemle aynı işlemci mimarisi için uygulamayı yeniden yayımlayın.
* Uygulamayı [çerçeveye bağlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-executables-fde)olarak yayımlayın.

### <a name="50033-ancm-request-handler-load-failure"></a>500,33 ANCM Istek Işleyicisi yükleme hatası

Çalışan işlemi başarısız olur. Uygulama başlamaz.

Uygulama `Microsoft.AspNetCore.App` çerçevesine başvurmadı. Yalnızca `Microsoft.AspNetCore.App` çerçevesini hedefleyen uygulamalar [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module)tarafından barındırılabilir.

Bu hatayı düzeltemedi, uygulamanın `Microsoft.AspNetCore.App` çerçevesini hedeflediğinden emin olun. Uygulamanın hedeflediği çerçeveyi doğrulamak için `.runtimeconfig.json` denetleyin.

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a>500,34 ANCM karışık barındırma modelleri desteklenmez

Çalışan işlem, aynı işlemde hem işlem içi uygulama hem de işlem dışı bir uygulama çalıştırılamaz.

Bu hatayı onarmak için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a>500,35 ANCM birden çok işlem Içi uygulama aynı Işlemde

Çalışan işlemi aynı işlemde birden çok işlem içi uygulama çalıştıramıyor.

Bu hatayı onarmak için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.

### <a name="50036-ancm-out-of-process-handler-load-failure"></a>500,36 ANCM Işlem dışı Işleyici yükleme hatası

İşlem dışı istek işleyicisi, *aspnetcorev2_outofprocess. dll*, *aspnetcorev2. dll* dosyasının yanında değildir. Bu, [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)bozuk bir yüklemesini gösterir.

Bu hatayı gidermek için [.NET Core barındırma paketi](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (IIS için) veya Visual Studio (IIS Express için) yüklemesini onarın.

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a>500,37 ANCM başlangıç zamanı sınırı Içinde başlatılamadı

ANCM, kısımları başlangıç süresi sınırı içinde başlatılamadı. Varsayılan olarak, zaman aşımı 120 saniyedir.

Aynı makinede çok sayıda uygulama başlatılırken bu hata oluşabilir. Başlangıç sırasında sunucuda CPU/bellek kullanımı artışlarını denetleyin. Birden çok uygulamanın başlatma işlemini şaşırtmayı yapmanız gerekebilir.

### <a name="5025-process-failure"></a>502.5 işlem hatası

Çalışan işlemi başarısız olur. Uygulama başlamaz.

[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) çalışan işlemini başlatmaya çalışır, ancak başlatılamıyor. İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.

Ortak bir hata durumu, uygulamanın mevcut olmayan ASP.NET Core paylaşılan framework sürümü hedefleme nedeniyle yanlış yapılandırılmış ' dir. Hangi sürümlerinin bir ASP.NET Core paylaşılan çerçeve hedef makinede yüklü olduğunu denetleyin. *Paylaşılan çerçeve* , makinede yüklü olan ve `Microsoft.AspNetCore.App`gibi bir metapackage tarafından başvurulan derleme ( *. dll* dosyaları) kümesidir. Metapackage başvurusu, gerekli en düşük sürümü belirtebilir. Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Bir barındırma veya uygulamanın yanlış yapılandırılması, çalışan işleminin başarısız olmasına neden olduğunda, *502,5 Işlem hata* hatası sayfası döndürülür:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Uygulama (hata kodu: '0x800700c1') başlatılamadı.

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Uygulamanın derlemesi ( *. dll*) yüklenemediğinden uygulama başlatılamadı.

W3wp/ıısexpress işlemi ile yayımlanan uygulama arasındaki bir bit genişliği uyuşmazlığı olduğunda bu hata oluşur.

Uygulama havuzunun 32-bit ayarının doğru olduğundan emin olun:

1. IIS yöneticisinin **uygulama havuzlarında**uygulama havuzunu seçin.
1. **Eylemler** panelinde **uygulama havuzunu Düzenle** altında **Gelişmiş ayarlar** ' ı seçin.
1. **Enable 32 bit uygulamalarını**ayarla:
   * 32-bit (x86) bir uygulama dağıtıyorsanız, değeri `True`olarak ayarlayın.
   * 64 bit (x64) uygulaması dağıtıyorsanız, değeri `False`olarak ayarlayın.

Proje dosyasındaki `<Platform>` MSBuild özelliği ile uygulamanın yayınlanan bit durumuyla ilgili bir çakışma olmadığını doğrulayın.

### <a name="connection-reset"></a>Bağlantı sıfırlama

Üstbilgiler gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Iç sunucu hatası** gönderebilmesi için çok geç olur. Bu durum, genellikle bir yanıt için karmaşık nesne serileştirme sırasında bir hata oluştuğunda gerçekleşir. Bu tür bir hata, istemcide bir *bağlantı sıfırlama* hatası olarak görüntülenir. [Uygulama günlüğü](xref:fundamentals/logging/index) bu tür hataların giderilmesine yardımcı olabilir.

### <a name="default-startup-limits"></a>Varsayılan başlangıç sınırları

[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) varsayılan bir *StartupTimeLimit* 120 saniye ile yapılandırılır. Varsayılan değer olarak sol uygulama modülü bir işlem hatası oturum önce başlatmak için iki dakika sürebilir. Modülü yapılandırma hakkında daha fazla bilgi için bkz. [aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Azure App Service sorunlarını giderme

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Uygulama olay günlüğü (Azure App Service)

Uygulama olay günlüğüne erişmek için Azure portal **sorunları Tanıla ve çöz** dikey penceresini kullanın:

1. Azure portal uygulama **Hizmetleri**' nde uygulamayı açın.
1. **Tanıla ve sorunları çöz '** ü seçin.
1. **Tanılama araçları** başlığını seçin.
1. **Destek Araçları**' nın altında, **uygulama olayları** düğmesini seçin.
1. **Kaynak** sütununda *IIS AspNetCoreModule* veya *IIS Aspnetcoremodule v2* girişi tarafından belirtilen en son hatayı inceleyin.

**Sorunları Tanıla ve çöz** dikey penceresini kullanmanın bir alternatifi, uygulama olay günlüğü dosyasını doğrudan [kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:

1. **Gelişmiş araçları** **geliştirme araçları** alanında açın. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.
1. **LogFiles** klasörünü açın.
1. *EventLog. xml* dosyasının yanındaki kurşun kalem simgesini seçin.
1. Günlüğü inceleyin. En son olayları görmek için günlüğün en altına gidin.

### <a name="run-the-app-in-the-kudu-console"></a>Uygulamayı kudu konsolunda çalıştırma

Başlatma hataları birçok yararlı bilgiler uygulama olay günlüğü'ndeki üretmediği. Bu hatayı saptamak için, uygulamayı [kudu](https://github.com/projectkudu/kudu/wiki) uzaktan yürütme konsolu 'nda çalıştırabilirsiniz:

1. **Gelişmiş araçları** **geliştirme araçları** alanında açın. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.

#### <a name="test-a-32-bit-x86-app"></a>32 bit (x86) uygulamayı test etme

**Geçerli yayın**

1. `cd d:\home\site\wwwroot`
1. Uygulamayı çalıştırın:
   * Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:

     ```console
     {ASSEMBLY NAME}.exe
     ```

Uygulamadan alınan konsol çıktısı, tüm hataları gösteren kudu konsoluna gönderilir.

**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**

*ASP.NET Core {VERSION} (x86) çalışma zamanı site uzantısının yüklenmesini gerektirir.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` çalışma zamanı sürümüdür)
1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Uygulamadan alınan konsol çıktısı, tüm hataları gösteren kudu konsoluna gönderilir.

#### <a name="test-a-64-bit-x64-app"></a>64 bit (x64) uygulamayı test etme

**Geçerli yayın**

* Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:
  1. `cd D:\Program Files\dotnet`
  1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:
  1. `cd D:\home\site\wwwroot`
  1. Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`

Uygulamadan alınan konsol çıktısı, tüm hataları gösteren kudu konsoluna gönderilir.

**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**

*ASP.NET Core {VERSION} (x64) çalışma zamanı site uzantısını yüklemeyi gerektirir.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` çalışma zamanı sürümüdür)
1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Uygulamadan alınan konsol çıktısı, tüm hataları gösteren kudu konsoluna gönderilir.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET Core modülü stdout günlüğü (Azure App Service)

ASP.NET Core Module stdout günlüğü genellikle uygulama olay günlüğünde bulunmayan yararlı hata iletilerini kaydeder. Stdout günlükleri görüntülemek ve etkinleştirmek için:

1. Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.
1. **Sorun kategorisini seçin**altında **Web uygulaması aşağı** düğmesini seçin.
1. **Önerilen çözümler** ' de **stdout günlük yeniden yönlendirmeyi etkinleştirmek**>, **Web. config dosyasını düzenlemek için kudu konsolunu açmak**üzere düğmeyi seçin.
1. Kudu **Tanılama konsolunda**, dosyaları **Wwwroot** > yol **sitesine** açın. Listenin altındaki *Web. config* dosyasını açığa çıkarmak için aşağı kaydırın.
1. *Web. config* dosyasının yanındaki kurşun kalem simgesine tıklayın.
1. **StdoutLogEnabled** olarak ayarlayın ve **stdoutLogFile** yolunu `true` olarak değiştirin: `\\?\%home%\LogFiles\stdout`.
1. Güncelleştirilmiş *Web. config* dosyasını kaydetmek için **Kaydet** ' i seçin.
1. Uygulamaya bir istek oluşturun.
1. Azure portalına dönün. **GELIŞTIRME araçları** alanında **Gelişmiş Araçlar** dikey penceresini seçin. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.
1. **LogFiles** klasörünü seçin.
1. **Değiştirilen** sütunu inceleyin ve son değiştirilme tarihiyle stdout günlüğünü düzenlemek için kalem simgesini seçin.
1. Günlük dosyası açıldığında hata görüntülenir.

Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:

1. Kudu **Tanılama konsolunda**, *Web. config* dosyasını açığa çıkarmak için **Wwwroot** > yolu **sitesine** dönün. Kalem simgesini seçerek **Web. config** dosyasını tekrar açın.
1. `false`için **stdoutLogEnabled** ayarlayın.
1. Dosyayı kaydetmek için **Kaydet** ' i seçin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Uygulama veya sunucu başarısızlığı için hata stdout günlüğünü devre dışı bırakmak için yol açabilir. Günlük dosyası boyutunu sınırlama yok veya oluşturulan günlük dosyası sayısı yoktur. Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlüğünü kullanın.
>
> Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>ASP.NET Core modülü hata ayıklama günlüğü (Azure App Service)

ASP.NET Core Module hata ayıklama günlüğü, ASP.NET Core modülünden daha ayrıntılı günlük kaydı sağlar. Stdout günlükleri görüntülemek ve etkinleştirmek için:

1. Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini yapın:
   * Uygulamayı gelişmiş tanılama günlüğü için yapılandırmak üzere [Gelişmiş tanılama günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) bölümündeki yönergeleri izleyin. Uygulamayı yeniden dağıtın.
   * [Gelişmiş tanılama günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilen `<handlerSettings>` kudu konsolunu kullanarak canlı uygulamanın *Web. config* dosyasına ekleyin:
     1. **Gelişmiş araçları** **geliştirme araçları** alanında açın. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
     1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.
     1. Dosyaları **wwwroot** > yol **sitesine** açın. *Web. config* dosyasını, kurşun kalem düğmesini seçerek düzenleyin. `<handlerSettings>` bölümünü, [Gelişmiş tanılama günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi ekleyin. **Kaydet** düğmesini seçin.
1. **Gelişmiş araçları** **geliştirme araçları** alanında açın. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.
1. Dosyaları **wwwroot** > yol **sitesine** açın. *Aspnetcore-Debug. log* dosyası için bir yol sağlamadıysanız dosya listede görüntülenir. Bir yol sağladıysanız, günlük dosyasının konumuna gidin.
1. Dosya adının yanındaki kurşun kalem düğmesiyle günlük dosyasını açın.

Sorun giderme tamamlandığında hata ayıklama günlüğünü devre dışı bırak:

Gelişmiş hata ayıklama günlüğünü devre dışı bırakmak için aşağıdakilerden birini yapın:

* *Web. config* dosyasından `<handlerSettings>` yerel olarak kaldırın ve uygulamayı yeniden dağıtın.
* *Web. config* dosyasını düzenlemek ve `<handlerSettings>` bölümünü kaldırmak Için kudu konsolunu kullanın. Dosyayı kaydedin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Hata ayıklama günlüğünü devre dışı bırakma hatası, uygulama veya sunucu hatasına yol açabilir. Günlük dosyası boyutunda sınır yoktur. Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğünü kullanın.
>
> Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Yavaş veya askıda olan uygulama (Azure App Service)

Bir uygulama bir istek üzerinde yavaş bir şekilde yanıt verdiğinde veya Kilitlenmelerinde, aşağıdaki makalelere bakın:

* [Azure App Service web uygulamasında yavaş performans sorunlarını giderme](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure Web uygulamasında aralıklı özel durum sorunları veya performans sorunları için döküm yakalamak üzere kilitlenme tanılayıcı site uzantısı 'nı kullanın](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>İzleme kanatları

İzleme dikey pencereleri, konusunda daha önce açıklanan yöntemlere alternatif bir sorun giderme deneyimi sağlar. Bu kanatlar 500 serisi hataları tanılamak için kullanılabilir.

ASP.NET Core uzantılarının yüklü olduğunu doğrulayın. Uzantılar yüklü değilse, bunları el ile yükleyebilirsiniz:

1. **GELIŞTIRME araçları** dikey penceresinde **Uzantılar** dikey penceresini seçin.
1. **ASP.NET Core uzantıları** listede görünmelidir.
1. Uzantılar yüklü değilse, **Ekle** düğmesini seçin.
1. Listeden **ASP.NET Core uzantılarını** seçin.
1. Yasal koşulları kabul etmek için **Tamam ' ı** seçin.
1. **Uzantı Ekle** dikey penceresinde **Tamam ' ı** seçin.
1. Bilgilendirici bir açılan ileti, uzantıların başarıyla yüklenip yüklenmediğini gösterir.

Stdout günlüğü etkinleştirilmemişse, şu adımları izleyin:

1. Azure portal, **GELIŞTIRME araçları** alanındaki **Gelişmiş Araçlar** dikey penceresini seçin. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.
1. Dosya yolu > **sitesindeki** klasörleri **açın ve listenin** altındaki *Web. config* dosyasını açığa çıkarmak için aşağı kaydırın.
1. *Web. config* dosyasının yanındaki kurşun kalem simgesine tıklayın.
1. **StdoutLogEnabled** olarak ayarlayın ve **stdoutLogFile** yolunu `true` olarak değiştirin: `\\?\%home%\LogFiles\stdout`.
1. Güncelleştirilmiş *Web. config* dosyasını kaydetmek için **Kaydet** ' i seçin.

Tanılama günlüğünü etkinleştirmek için ilerleyin:

1. Azure portal **tanılama günlükleri** dikey penceresini seçin.
1. **Uygulama günlüğü (dosya sistemi)** ve **ayrıntılı hata iletileri**için **bir anahtar seçin** . Dikey pencerenin üst kısmındaki **Kaydet** düğmesini seçin.
1. Başarısız istek izlemeyi, başarısız Istek olayı arabelleğe alma (FREB) günlüğü olarak da bilinen bir şekilde eklemek için **,** **başarısız istek izleme**anahtarını seçin.
1. Portalda **tanılama günlükleri** dikey penceresinde hemen listelenen **günlük akışı** dikey penceresini seçin.
1. Uygulamaya bir istek oluşturun.
1. Günlük akışı verileri içinde hatanın nedeni belirtilir.

Sorun giderme tamamlandığında stdout günlüğünü devre dışı bıraktığınızdan emin olun.

Başarısız istek izleme günlüklerini görüntülemek için (FREB günlükleri):

1. Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.
1. Kenar çubuğunun **Destek Araçları** alanından **başarısız istek izleme günlüklerini** seçin.

[Azure App Service konusundaki Web uygulamaları için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure 'Daki Web Apps Için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) bölümündeki başarısız istek izlemeleri bölümüne bakın: daha fazla bilgi için nasıl yaparım? başarısız istek izlemeyi açın.

Daha fazla bilgi için bkz. [Azure App Service Web Apps için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Uygulama veya sunucu başarısızlığı için hata stdout günlüğünü devre dışı bırakmak için yol açabilir. Günlük dosyası boyutunu sınırlama yok veya oluşturulan günlük dosyası sayısı yoktur.
>
> ASP.NET Core uygulamanızı rutin günlüğü için günlük dosyası boyutunu sınırlar ve günlükleri döndürür bir günlük kitaplığını kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>IIS 'de sorun giderme

### <a name="application-event-log-iis"></a>Uygulama olay günlüğü (IIS)

Uygulama olay günlüğüne erişemedi:

1. Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.
1. **Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.
1. Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.
1. Başarısız olan uygulama ile ilişkili hataları arayın. Hataların, *kaynak* sütununda *IIS aspnetcore modülünün* veya *IIS Express aspnetcore modülünün* bir değeri vardır.

### <a name="run-the-app-at-a-command-prompt"></a>Uygulamayı bir komut isteminde aşağıdakini çalıştırın

Başlatma hataları birçok yararlı bilgiler uygulama olay günlüğü'ndeki üretmediği. Bazı hataların nedeni, barındıran sistemde bir komut isteminde uygulamayı çalıştırarak bulabilirsiniz.

#### <a name="framework-dependent-deployment"></a>Framework bağımlı dağıtım

Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:

1. Bir komut isteminde, dağıtım klasörüne gidin ve uygulamanın derlemesini *DotNet. exe*ile yürüterek uygulamayı çalıştırın. Aşağıdaki komutta, \<assembly_name >: `dotnet .\<assembly_name>.dll`için uygulama derlemesinin adını yerine koyun.
1. Konsol çıkışını herhangi bir hata gösteren uygulamadan konsol penceresine yazılır.
1. Uygulamaya bir istek yaparken, hataları meydana gelirse, burada Kestrel dinlediği bağlantı noktası ve ana bilgisayar için istekte bulunmak. Varsayılan konak ve gönderi kullanarak `http://localhost:5000/`bir istek yapın. Uygulamayı, normalde Kestrel uç nokta adresindeki yanıt verirse, sorun barındırma yapılandırmasında ve büyük olasılıkla daha az uygulama içinde ilgili daha yüksektir.

#### <a name="self-contained-deployment"></a>Kendi içinde dağıtım

Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:

1. Bir komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir dosyayı çalıştırın. Aşağıdaki komutta, \<assembly_name >: `<assembly_name>.exe`için uygulama derlemesinin adını yerine koyun.
1. Konsol çıkışını herhangi bir hata gösteren uygulamadan konsol penceresine yazılır.
1. Uygulamaya bir istek yaparken, hataları meydana gelirse, burada Kestrel dinlediği bağlantı noktası ve ana bilgisayar için istekte bulunmak. Varsayılan konak ve gönderi kullanarak `http://localhost:5000/`bir istek yapın. Uygulamayı, normalde Kestrel uç nokta adresindeki yanıt verirse, sorun barındırma yapılandırmasında ve büyük olasılıkla daha az uygulama içinde ilgili daha yüksektir.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET Core Module stdout günlüğü (IIS)

Stdout günlükleri görüntülemek ve etkinleştirmek için:

1. Barındıran sistemde sitenin dağıtım klasörüne gidin.
1. *Günlükler* klasörü yoksa, klasörü oluşturun. MSBuild 'in dağıtımdaki *Günlükler* klasörünü otomatik olarak oluşturmak üzere nasıl etkinleştirileceği hakkında yönergeler için, bkz. [Dizin yapısı](xref:host-and-deploy/directory-structure) konusu.
1. *Web. config* dosyasını düzenleyin. **StdoutLogEnabled** öğesini `true` olarak ayarlayın ve **stdoutLogFile** yolunu *Günlükler* klasörünü işaret etmek üzere değiştirin (örneğin, `.\logs\stdout`). yoldaki `stdout` günlük dosyası adı önekidir. Oturum oluşturulduğunda bir zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir. Dosya adı ön eki olarak `stdout` kullanarak, tipik bir günlük dosyası, *stdout_20180205184032_5412. log*olarak adlandırılır.
1. Uygulama havuzunuzun kimliğinin *Günlükler* klasörü için yazma izinlerine sahip olduğundan emin olun.
1. Güncelleştirilmiş *Web. config* dosyasını kaydedin.
1. Uygulamaya bir istek oluşturun.
1. *Günlükler* klasörüne gidin. Bulun ve en son stdout günlüğü'nü açın.
1. Hatalar için günlüğü inceleyin.

Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:

1. *Web. config* dosyasını düzenleyin.
1. `false`için **stdoutLogEnabled** ayarlayın.
1. Dosyayı kaydedin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Uygulama veya sunucu başarısızlığı için hata stdout günlüğünü devre dışı bırakmak için yol açabilir. Günlük dosyası boyutunu sınırlama yok veya oluşturulan günlük dosyası sayısı yoktur.
>
> ASP.NET Core uygulamanızı rutin günlüğü için günlük dosyası boyutunu sınırlar ve günlükleri döndürür bir günlük kitaplığını kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-iis"></a>ASP.NET Core modülü hata ayıklama günlüğü (IIS)

ASP.NET Core modülü hata ayıklama günlüğünü etkinleştirmek için aşağıdaki işleyici ayarlarını uygulamanın *Web. config* dosyasına ekleyin:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Günlüğü için belirtilen yolun var olduğundan ve uygulama havuzu kimliğinin konumuna yazma izinlerine sahip olduğunu doğrulayın.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Geliştirici özel durumu sayfasını etkinleştir

`ASPNETCORE_ENVIRONMENT` ortam değişkeni, uygulamayı geliştirme ortamında çalıştırmak için [Web. config dosyasına eklenebilir](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) . Ortam, ana bilgisayar tasarımcısında `UseEnvironment` tarafından uygulama başlangıcında geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında [Geliştirici özel durum sayfasının](xref:fundamentals/error-handling) görünmesine izin verir.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

`ASPNETCORE_ENVIRONMENT` için ortam değişkenini ayarlamak yalnızca Internet 'e açık olmayan hazırlama ve test etme sunucularında kullanılması önerilir. Sorun giderme işleminden sonra *Web. config* dosyasından ortam değişkenini kaldırın. *Web. config*'de ortam değişkenlerini ayarlama hakkında daha fazla bilgi Için, [Aspnetcore 'un EnvironmentVariables alt öğesi](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)bölümüne bakın.

### <a name="obtain-data-from-an-app"></a>Bir uygulamadan veri alın

Bir uygulama isteklerini yanıtlayabileceği ise, istek, bağlantı ve ek veri terminal satır içi ara yazılımın kullanılması uygulamayı edinin. Daha fazla bilgi ve örnek kod için bkz. <xref:test/troubleshoot#obtain-data-from-an-app>.

### <a name="slow-or-hanging-app-iis"></a>Yavaş veya askıda olan uygulama (IIS)

*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.

#### <a name="app-crashes-or-encounters-an-exception"></a>Uygulama kilitleniyor veya bir özel durumla karşılaşırsa

Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):

1. Kilitlenme döküm dosyalarını `c:\dumps`tutmak için bir klasör oluşturun. Uygulama havuzunun klasöre yazma erişimi olmalıdır.
1. [Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:
   * Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *W3wp. exe*için betiği çalıştırın:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *DotNet. exe*için betiği çalıştırın:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.
1. Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:
   * Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *W3wp. exe*için betiği çalıştırın:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *DotNet. exe*için betiği çalıştırın:

     ```console
     .\DisableDumps dotnet.exe
     ```

Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir. PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.

> [!WARNING]
> Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor

Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.

#### <a name="analyze-the-dump"></a>Dökümü çözümle

Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir. Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Paket önbelleklerini temizle

Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir. Bazı durumlarda, ana yükseltme yaparken, bir uygulama tutarsız paketleri kesilebilir. Bu sorunların çoğu, bu yönergeleri izleyerek düzeltilebilir:

1. *Bin* ve *obj* klasörlerini silin.
1. Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.

   Paket önbelleklerini Temizleme, [NuGet. exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komut `nuget locals all -clear`yürütülebilir. *NuGet. exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.

1. Geri yükle ve projeyi yeniden derleyin.
1. Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Azure belgeleri

* [ASP.NET Core için Application Insights](/azure/application-insights/app-insights-asp-net-core)
* [Visual Studio 'Yu kullanarak Azure App Service Web uygulamasının sorunlarını giderme bölümünde uzaktan hata ayıklama Web Apps bölümü](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Azure App Service tanılamada genel bakış](/azure/app-service/app-service-diagnostics)
* [Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme](/azure/app-service/web-sites-monitor)
* [Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure Web uygulamalarınızda "502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" HTTP hatalarında sorun giderme](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Azure App Service web uygulamasında yavaş performans sorunlarını giderme](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure 'da Web Apps için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web uygulaması korumalı alanı (App Service çalışma zamanı yürütme sınırlamaları)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Cuma: Azure App Service tanılama ve sorun giderme deneyimi (12 dakikalık video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Visual Studio belgeleri

* [Visual Studio 2017 ' de Azure 'da IIS 'de uzaktan hata ayıklama ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)
* [Visual Studio 2017 ' de uzak IIS bilgisayarında uzaktan hata ayıklama ASP.NET Core](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Visual Studio kullanarak hata ayıklamayı öğrenin](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Visual Studio Code belgeleri

* [Visual Studio Code ile hata ayıklama](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Bu makalede, bir uygulama Azure App Service veya IIS 'ye dağıtıldığında hataların nasıl tanılanacağı hakkında genel uygulama başlatma hataları ve yönergeleri hakkında bilgi verilmektedir:

[Uygulama başlatma hataları](#app-startup-errors)  
Ortak Başlangıç HTTP durum kodu senaryolarını açıklar.

[Azure App Service sorunlarını giderme](#troubleshoot-on-azure-app-service)  
Azure App Service dağıtılan uygulamalar için sorun giderme önerisi sağlar.

[IIS üzerinde sorun giderme](#troubleshoot-on-iis)  
IIS 'ye dağıtılan veya IIS Express yerel olarak çalışan uygulamalar için sorun giderme önerisi sağlar. Bu kılavuz hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.

[Paket önbelleklerini temizle](#clear-package-caches)  
Önemli güncelleştirmeler gerçekleştirirken veya paket sürümlerini değiştirirken ne yapmanız gerektiğini açıklar.

[Ek Kaynaklar](#additional-resources)  
Ek sorun giderme konularını listeler.

## <a name="app-startup-errors"></a>Uygulama başlatma hataları

Visual Studio 'da bir ASP.NET Core projesi, hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırmak için varsayılan değerdir. *502,5-Işlem hatası* veya yerel olarak hata ayıklarken oluşan *500,30-başlatma hatası* , bu konudaki öneri kullanılarak tanılanabilir.

### <a name="40314-forbidden"></a>403,14 yasak

Uygulama başlatılamıyor. Aşağıdaki hata günlüğe kaydedilir:

```
The Web server is configured to not list the contents of this directory.
```

Hata genellikle barındırma sisteminde, aşağıdaki senaryolardan birini içeren bozuk bir dağıtım nedeniyle oluşur:

* Uygulama, barındırma sisteminde yanlış klasöre dağıtılır.
* Dağıtım işlemi, uygulamanın tüm dosyalarını ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.
* *Web. config* dosyası dağıtımda yok veya *Web. config* dosyası içerikleri hatalı biçimlendirilmiş.

Aşağıdaki adımları uygulayın:

1. Tüm dosya ve klasörleri barındırma sistemindeki dağıtım klasöründen silin.
1. Visual Studio, PowerShell veya el ile dağıtım gibi normal dağıtım yönteminizi kullanarak, uygulamanın *Yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:
   * *Web. config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.
   * Azure App Service barındırırken, uygulamanın `D:\home\site\wwwroot` klasörüne dağıtıldığını doğrulayın.
   * Uygulama IIS tarafından barındırılıyorsa, uygulamanın **IIS yöneticisinin** **temel ayarlarında**gösterilen IIS **fiziksel yoluna** dağıtıldığını doğrulayın.
1. Barındırma sistemindeki dağıtımı projenin *Yayımla* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtıldığını doğrulayın.

Yayımlanan ASP.NET Core uygulamasının düzeni hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/directory-structure>. *Web. config* dosyası hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.

### <a name="500-internal-server-error"></a>500 İç Sunucu Hatası

Uygulamayı başlatır, ancak bir hata sunucu isteği yerine getirmesini önler.

Bu hata, başlatma sırasında veya bir yanıt oluşturulurken uygulamanın kod içinde oluşur. Yanıtta içerik yok olabilir veya Yanıt, tarayıcıda *500 Iç sunucu hatası* olarak görünebilir. Uygulama olay günlüğü, genellikle uygulama normal şekilde çalışmaya belirtir. Sunucunun açısından bakıldığında, doğru olmasıdır. Uygulama başladı, ancak geçerli bir yanıt oluşturulamıyor. Uygulamayı sunucuda bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Core modülü stdout günlüğünü etkinleştirin.

### <a name="5000-in-process-handler-load-failure"></a>500.0 işlem içi işleyici yükleme hatası

Çalışan işlemi başarısız olur. Uygulama başlamaz.

[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR 'yi bulamıyor ve işlem içi istek işleyicisini (*aspnetcorev2_inprocess. dll*) bulamıyor. Kontrol edin:

* Uygulama [Microsoft. AspNetCore. Server. IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet paketini ya da [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'i hedefler.
* ASP.NET Core paylaşılan framework'ün hedefliyorsa hedef makinede yüklü sürümü.

### <a name="5000-out-of-process-handler-load-failure"></a>500.0 giden işlem işleyicisi yükleme hatası

Çalışan işlemi başarısız olur. Uygulama başlamaz.

[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) işlem dışı barındırma isteği işleyicisini bulamıyor. *Aspnetcorev2_outofprocess. dll* ' nin *aspnetcorev2. dll*' nin yanındaki bir alt klasörde bulunduğundan emin olun.

### <a name="5025-process-failure"></a>502.5 işlem hatası

Çalışan işlemi başarısız olur. Uygulama başlamaz.

[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) çalışan işlemini başlatmaya çalışır, ancak başlatılamıyor. İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.

Ortak bir hata durumu, uygulamanın mevcut olmayan ASP.NET Core paylaşılan framework sürümü hedefleme nedeniyle yanlış yapılandırılmış ' dir. Hangi sürümlerinin bir ASP.NET Core paylaşılan çerçeve hedef makinede yüklü olduğunu denetleyin. *Paylaşılan çerçeve* , makinede yüklü olan ve `Microsoft.AspNetCore.App`gibi bir metapackage tarafından başvurulan derleme ( *. dll* dosyaları) kümesidir. Metapackage başvurusu, gerekli en düşük sürümü belirtebilir. Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Bir barındırma veya uygulamanın yanlış yapılandırılması, çalışan işleminin başarısız olmasına neden olduğunda, *502,5 Işlem hata* hatası sayfası döndürülür:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Uygulama (hata kodu: '0x800700c1') başlatılamadı.

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Uygulamanın derlemesi ( *. dll*) yüklenemediğinden uygulama başlatılamadı.

W3wp/ıısexpress işlemi ile yayımlanan uygulama arasındaki bir bit genişliği uyuşmazlığı olduğunda bu hata oluşur.

Uygulama havuzunun 32-bit ayarının doğru olduğundan emin olun:

1. IIS yöneticisinin **uygulama havuzlarında**uygulama havuzunu seçin.
1. **Eylemler** panelinde **uygulama havuzunu Düzenle** altında **Gelişmiş ayarlar** ' ı seçin.
1. **Enable 32 bit uygulamalarını**ayarla:
   * 32-bit (x86) bir uygulama dağıtıyorsanız, değeri `True`olarak ayarlayın.
   * 64 bit (x64) uygulaması dağıtıyorsanız, değeri `False`olarak ayarlayın.

Proje dosyasındaki `<Platform>` MSBuild özelliği ile uygulamanın yayınlanan bit durumuyla ilgili bir çakışma olmadığını doğrulayın.

### <a name="connection-reset"></a>Bağlantı sıfırlama

Üstbilgiler gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Iç sunucu hatası** gönderebilmesi için çok geç olur. Bu durum, genellikle bir yanıt için karmaşık nesne serileştirme sırasında bir hata oluştuğunda gerçekleşir. Bu tür bir hata, istemcide bir *bağlantı sıfırlama* hatası olarak görüntülenir. [Uygulama günlüğü](xref:fundamentals/logging/index) bu tür hataların giderilmesine yardımcı olabilir.

### <a name="default-startup-limits"></a>Varsayılan başlangıç sınırları

[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) varsayılan bir *StartupTimeLimit* 120 saniye ile yapılandırılır. Varsayılan değer olarak sol uygulama modülü bir işlem hatası oturum önce başlatmak için iki dakika sürebilir. Modülü yapılandırma hakkında daha fazla bilgi için bkz. [aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Azure App Service sorunlarını giderme

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Uygulama olay günlüğü (Azure App Service)

Uygulama olay günlüğüne erişmek için Azure portal **sorunları Tanıla ve çöz** dikey penceresini kullanın:

1. Azure portal uygulama **Hizmetleri**' nde uygulamayı açın.
1. **Tanıla ve sorunları çöz '** ü seçin.
1. **Tanılama araçları** başlığını seçin.
1. **Destek Araçları**' nın altında, **uygulama olayları** düğmesini seçin.
1. **Kaynak** sütununda *IIS AspNetCoreModule* veya *IIS Aspnetcoremodule v2* girişi tarafından belirtilen en son hatayı inceleyin.

**Sorunları Tanıla ve çöz** dikey penceresini kullanmanın bir alternatifi, uygulama olay günlüğü dosyasını doğrudan [kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:

1. **Gelişmiş araçları** **geliştirme araçları** alanında açın. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.
1. **LogFiles** klasörünü açın.
1. *EventLog. xml* dosyasının yanındaki kurşun kalem simgesini seçin.
1. Günlüğü inceleyin. En son olayları görmek için günlüğün en altına gidin.

### <a name="run-the-app-in-the-kudu-console"></a>Uygulamayı kudu konsolunda çalıştırma

Başlatma hataları birçok yararlı bilgiler uygulama olay günlüğü'ndeki üretmediği. Bu hatayı saptamak için, uygulamayı [kudu](https://github.com/projectkudu/kudu/wiki) uzaktan yürütme konsolu 'nda çalıştırabilirsiniz:

1. **Gelişmiş araçları** **geliştirme araçları** alanında açın. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.

#### <a name="test-a-32-bit-x86-app"></a>32 bit (x86) uygulamayı test etme

**Geçerli yayın**

1. `cd d:\home\site\wwwroot`
1. Uygulamayı çalıştırın:
   * Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:

     ```console
     {ASSEMBLY NAME}.exe
     ```

Uygulamadan alınan konsol çıktısı, tüm hataları gösteren kudu konsoluna gönderilir.

**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**

*ASP.NET Core {VERSION} (x86) çalışma zamanı site uzantısının yüklenmesini gerektirir.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` çalışma zamanı sürümüdür)
1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Uygulamadan alınan konsol çıktısı, tüm hataları gösteren kudu konsoluna gönderilir.

#### <a name="test-a-64-bit-x64-app"></a>64 bit (x64) uygulamayı test etme

**Geçerli yayın**

* Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:
  1. `cd D:\Program Files\dotnet`
  1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:
  1. `cd D:\home\site\wwwroot`
  1. Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`

Uygulamadan alınan konsol çıktısı, tüm hataları gösteren kudu konsoluna gönderilir.

**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**

*ASP.NET Core {VERSION} (x64) çalışma zamanı site uzantısını yüklemeyi gerektirir.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` çalışma zamanı sürümüdür)
1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Uygulamadan alınan konsol çıktısı, tüm hataları gösteren kudu konsoluna gönderilir.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET Core modülü stdout günlüğü (Azure App Service)

ASP.NET Core Module stdout günlüğü genellikle uygulama olay günlüğünde bulunmayan yararlı hata iletilerini kaydeder. Stdout günlükleri görüntülemek ve etkinleştirmek için:

1. Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.
1. **Sorun kategorisini seçin**altında **Web uygulaması aşağı** düğmesini seçin.
1. **Önerilen çözümler** ' de **stdout günlük yeniden yönlendirmeyi etkinleştirmek**>, **Web. config dosyasını düzenlemek için kudu konsolunu açmak**üzere düğmeyi seçin.
1. Kudu **Tanılama konsolunda**, dosyaları **Wwwroot** > yol **sitesine** açın. Listenin altındaki *Web. config* dosyasını açığa çıkarmak için aşağı kaydırın.
1. *Web. config* dosyasının yanındaki kurşun kalem simgesine tıklayın.
1. **StdoutLogEnabled** olarak ayarlayın ve **stdoutLogFile** yolunu `true` olarak değiştirin: `\\?\%home%\LogFiles\stdout`.
1. Güncelleştirilmiş *Web. config* dosyasını kaydetmek için **Kaydet** ' i seçin.
1. Uygulamaya bir istek oluşturun.
1. Azure portalına dönün. **GELIŞTIRME araçları** alanında **Gelişmiş Araçlar** dikey penceresini seçin. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.
1. **LogFiles** klasörünü seçin.
1. **Değiştirilen** sütunu inceleyin ve son değiştirilme tarihiyle stdout günlüğünü düzenlemek için kalem simgesini seçin.
1. Günlük dosyası açıldığında hata görüntülenir.

Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:

1. Kudu **Tanılama konsolunda**, *Web. config* dosyasını açığa çıkarmak için **Wwwroot** > yolu **sitesine** dönün. Kalem simgesini seçerek **Web. config** dosyasını tekrar açın.
1. `false`için **stdoutLogEnabled** ayarlayın.
1. Dosyayı kaydetmek için **Kaydet** ' i seçin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Uygulama veya sunucu başarısızlığı için hata stdout günlüğünü devre dışı bırakmak için yol açabilir. Günlük dosyası boyutunu sınırlama yok veya oluşturulan günlük dosyası sayısı yoktur. Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlüğünü kullanın.
>
> Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>ASP.NET Core modülü hata ayıklama günlüğü (Azure App Service)

ASP.NET Core Module hata ayıklama günlüğü, ASP.NET Core modülünden daha ayrıntılı günlük kaydı sağlar. Stdout günlükleri görüntülemek ve etkinleştirmek için:

1. Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini yapın:
   * Uygulamayı gelişmiş tanılama günlüğü için yapılandırmak üzere [Gelişmiş tanılama günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) bölümündeki yönergeleri izleyin. Uygulamayı yeniden dağıtın.
   * [Gelişmiş tanılama günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilen `<handlerSettings>` kudu konsolunu kullanarak canlı uygulamanın *Web. config* dosyasına ekleyin:
     1. **Gelişmiş araçları** **geliştirme araçları** alanında açın. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
     1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.
     1. Dosyaları **wwwroot** > yol **sitesine** açın. *Web. config* dosyasını, kurşun kalem düğmesini seçerek düzenleyin. `<handlerSettings>` bölümünü, [Gelişmiş tanılama günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi ekleyin. **Kaydet** düğmesini seçin.
1. **Gelişmiş araçları** **geliştirme araçları** alanında açın. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.
1. Dosyaları **wwwroot** > yol **sitesine** açın. *Aspnetcore-Debug. log* dosyası için bir yol sağlamadıysanız dosya listede görüntülenir. Bir yol sağladıysanız, günlük dosyasının konumuna gidin.
1. Dosya adının yanındaki kurşun kalem düğmesiyle günlük dosyasını açın.

Sorun giderme tamamlandığında hata ayıklama günlüğünü devre dışı bırak:

Gelişmiş hata ayıklama günlüğünü devre dışı bırakmak için aşağıdakilerden birini yapın:

* *Web. config* dosyasından `<handlerSettings>` yerel olarak kaldırın ve uygulamayı yeniden dağıtın.
* *Web. config* dosyasını düzenlemek ve `<handlerSettings>` bölümünü kaldırmak Için kudu konsolunu kullanın. Dosyayı kaydedin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Hata ayıklama günlüğünü devre dışı bırakma hatası, uygulama veya sunucu hatasına yol açabilir. Günlük dosyası boyutunda sınır yoktur. Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğünü kullanın.
>
> Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Yavaş veya askıda olan uygulama (Azure App Service)

Bir uygulama bir istek üzerinde yavaş bir şekilde yanıt verdiğinde veya Kilitlenmelerinde, aşağıdaki makalelere bakın:

* [Azure App Service web uygulamasında yavaş performans sorunlarını giderme](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure Web uygulamasında aralıklı özel durum sorunları veya performans sorunları için döküm yakalamak üzere kilitlenme tanılayıcı site uzantısı 'nı kullanın](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>İzleme kanatları

İzleme dikey pencereleri, konusunda daha önce açıklanan yöntemlere alternatif bir sorun giderme deneyimi sağlar. Bu kanatlar 500 serisi hataları tanılamak için kullanılabilir.

ASP.NET Core uzantılarının yüklü olduğunu doğrulayın. Uzantılar yüklü değilse, bunları el ile yükleyebilirsiniz:

1. **GELIŞTIRME araçları** dikey penceresinde **Uzantılar** dikey penceresini seçin.
1. **ASP.NET Core uzantıları** listede görünmelidir.
1. Uzantılar yüklü değilse, **Ekle** düğmesini seçin.
1. Listeden **ASP.NET Core uzantılarını** seçin.
1. Yasal koşulları kabul etmek için **Tamam ' ı** seçin.
1. **Uzantı Ekle** dikey penceresinde **Tamam ' ı** seçin.
1. Bilgilendirici bir açılan ileti, uzantıların başarıyla yüklenip yüklenmediğini gösterir.

Stdout günlüğü etkinleştirilmemişse, şu adımları izleyin:

1. Azure portal, **GELIŞTIRME araçları** alanındaki **Gelişmiş Araçlar** dikey penceresini seçin. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.
1. Dosya yolu > **sitesindeki** klasörleri **açın ve listenin** altındaki *Web. config* dosyasını açığa çıkarmak için aşağı kaydırın.
1. *Web. config* dosyasının yanındaki kurşun kalem simgesine tıklayın.
1. **StdoutLogEnabled** olarak ayarlayın ve **stdoutLogFile** yolunu `true` olarak değiştirin: `\\?\%home%\LogFiles\stdout`.
1. Güncelleştirilmiş *Web. config* dosyasını kaydetmek için **Kaydet** ' i seçin.

Tanılama günlüğünü etkinleştirmek için ilerleyin:

1. Azure portal **tanılama günlükleri** dikey penceresini seçin.
1. **Uygulama günlüğü (dosya sistemi)** ve **ayrıntılı hata iletileri**için **bir anahtar seçin** . Dikey pencerenin üst kısmındaki **Kaydet** düğmesini seçin.
1. Başarısız istek izlemeyi, başarısız Istek olayı arabelleğe alma (FREB) günlüğü olarak da bilinen bir şekilde eklemek için **,** **başarısız istek izleme**anahtarını seçin.
1. Portalda **tanılama günlükleri** dikey penceresinde hemen listelenen **günlük akışı** dikey penceresini seçin.
1. Uygulamaya bir istek oluşturun.
1. Günlük akışı verileri içinde hatanın nedeni belirtilir.

Sorun giderme tamamlandığında stdout günlüğünü devre dışı bıraktığınızdan emin olun.

Başarısız istek izleme günlüklerini görüntülemek için (FREB günlükleri):

1. Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.
1. Kenar çubuğunun **Destek Araçları** alanından **başarısız istek izleme günlüklerini** seçin.

[Azure App Service konusundaki Web uygulamaları için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure 'Daki Web Apps Için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) bölümündeki başarısız istek izlemeleri bölümüne bakın: daha fazla bilgi için nasıl yaparım? başarısız istek izlemeyi açın.

Daha fazla bilgi için bkz. [Azure App Service Web Apps için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Uygulama veya sunucu başarısızlığı için hata stdout günlüğünü devre dışı bırakmak için yol açabilir. Günlük dosyası boyutunu sınırlama yok veya oluşturulan günlük dosyası sayısı yoktur.
>
> ASP.NET Core uygulamanızı rutin günlüğü için günlük dosyası boyutunu sınırlar ve günlükleri döndürür bir günlük kitaplığını kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>IIS 'de sorun giderme

### <a name="application-event-log-iis"></a>Uygulama olay günlüğü (IIS)

Uygulama olay günlüğüne erişemedi:

1. Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.
1. **Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.
1. Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.
1. Başarısız olan uygulama ile ilişkili hataları arayın. Hataların, *kaynak* sütununda *IIS aspnetcore modülünün* veya *IIS Express aspnetcore modülünün* bir değeri vardır.

### <a name="run-the-app-at-a-command-prompt"></a>Uygulamayı bir komut isteminde aşağıdakini çalıştırın

Başlatma hataları birçok yararlı bilgiler uygulama olay günlüğü'ndeki üretmediği. Bazı hataların nedeni, barındıran sistemde bir komut isteminde uygulamayı çalıştırarak bulabilirsiniz.

#### <a name="framework-dependent-deployment"></a>Framework bağımlı dağıtım

Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:

1. Bir komut isteminde, dağıtım klasörüne gidin ve uygulamanın derlemesini *DotNet. exe*ile yürüterek uygulamayı çalıştırın. Aşağıdaki komutta, \<assembly_name >: `dotnet .\<assembly_name>.dll`için uygulama derlemesinin adını yerine koyun.
1. Konsol çıkışını herhangi bir hata gösteren uygulamadan konsol penceresine yazılır.
1. Uygulamaya bir istek yaparken, hataları meydana gelirse, burada Kestrel dinlediği bağlantı noktası ve ana bilgisayar için istekte bulunmak. Varsayılan konak ve gönderi kullanarak `http://localhost:5000/`bir istek yapın. Uygulamayı, normalde Kestrel uç nokta adresindeki yanıt verirse, sorun barındırma yapılandırmasında ve büyük olasılıkla daha az uygulama içinde ilgili daha yüksektir.

#### <a name="self-contained-deployment"></a>Kendi içinde dağıtım

Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:

1. Bir komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir dosyayı çalıştırın. Aşağıdaki komutta, \<assembly_name >: `<assembly_name>.exe`için uygulama derlemesinin adını yerine koyun.
1. Konsol çıkışını herhangi bir hata gösteren uygulamadan konsol penceresine yazılır.
1. Uygulamaya bir istek yaparken, hataları meydana gelirse, burada Kestrel dinlediği bağlantı noktası ve ana bilgisayar için istekte bulunmak. Varsayılan konak ve gönderi kullanarak `http://localhost:5000/`bir istek yapın. Uygulamayı, normalde Kestrel uç nokta adresindeki yanıt verirse, sorun barındırma yapılandırmasında ve büyük olasılıkla daha az uygulama içinde ilgili daha yüksektir.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET Core Module stdout günlüğü (IIS)

Stdout günlükleri görüntülemek ve etkinleştirmek için:

1. Barındıran sistemde sitenin dağıtım klasörüne gidin.
1. *Günlükler* klasörü yoksa, klasörü oluşturun. MSBuild 'in dağıtımdaki *Günlükler* klasörünü otomatik olarak oluşturmak üzere nasıl etkinleştirileceği hakkında yönergeler için, bkz. [Dizin yapısı](xref:host-and-deploy/directory-structure) konusu.
1. *Web. config* dosyasını düzenleyin. **StdoutLogEnabled** öğesini `true` olarak ayarlayın ve **stdoutLogFile** yolunu *Günlükler* klasörünü işaret etmek üzere değiştirin (örneğin, `.\logs\stdout`). yoldaki `stdout` günlük dosyası adı önekidir. Oturum oluşturulduğunda bir zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir. Dosya adı ön eki olarak `stdout` kullanarak, tipik bir günlük dosyası, *stdout_20180205184032_5412. log*olarak adlandırılır.
1. Uygulama havuzunuzun kimliğinin *Günlükler* klasörü için yazma izinlerine sahip olduğundan emin olun.
1. Güncelleştirilmiş *Web. config* dosyasını kaydedin.
1. Uygulamaya bir istek oluşturun.
1. *Günlükler* klasörüne gidin. Bulun ve en son stdout günlüğü'nü açın.
1. Hatalar için günlüğü inceleyin.

Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:

1. *Web. config* dosyasını düzenleyin.
1. `false`için **stdoutLogEnabled** ayarlayın.
1. Dosyayı kaydedin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Uygulama veya sunucu başarısızlığı için hata stdout günlüğünü devre dışı bırakmak için yol açabilir. Günlük dosyası boyutunu sınırlama yok veya oluşturulan günlük dosyası sayısı yoktur.
>
> ASP.NET Core uygulamanızı rutin günlüğü için günlük dosyası boyutunu sınırlar ve günlükleri döndürür bir günlük kitaplığını kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-iis"></a>ASP.NET Core modülü hata ayıklama günlüğü (IIS)

ASP.NET Core modülü hata ayıklama günlüğünü etkinleştirmek için aşağıdaki işleyici ayarlarını uygulamanın *Web. config* dosyasına ekleyin:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Günlüğü için belirtilen yolun var olduğundan ve uygulama havuzu kimliğinin konumuna yazma izinlerine sahip olduğunu doğrulayın.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Geliştirici özel durumu sayfasını etkinleştir

`ASPNETCORE_ENVIRONMENT` ortam değişkeni, uygulamayı geliştirme ortamında çalıştırmak için [Web. config dosyasına eklenebilir](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) . Ortam, ana bilgisayar tasarımcısında `UseEnvironment` tarafından uygulama başlangıcında geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında [Geliştirici özel durum sayfasının](xref:fundamentals/error-handling) görünmesine izin verir.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

`ASPNETCORE_ENVIRONMENT` için ortam değişkenini ayarlamak yalnızca Internet 'e açık olmayan hazırlama ve test etme sunucularında kullanılması önerilir. Sorun giderme işleminden sonra *Web. config* dosyasından ortam değişkenini kaldırın. *Web. config*'de ortam değişkenlerini ayarlama hakkında daha fazla bilgi Için, [Aspnetcore 'un EnvironmentVariables alt öğesi](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)bölümüne bakın.

### <a name="obtain-data-from-an-app"></a>Bir uygulamadan veri alın

Bir uygulama isteklerini yanıtlayabileceği ise, istek, bağlantı ve ek veri terminal satır içi ara yazılımın kullanılması uygulamayı edinin. Daha fazla bilgi ve örnek kod için bkz. <xref:test/troubleshoot#obtain-data-from-an-app>.

### <a name="slow-or-hanging-app-iis"></a>Yavaş veya askıda olan uygulama (IIS)

*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.

#### <a name="app-crashes-or-encounters-an-exception"></a>Uygulama kilitleniyor veya bir özel durumla karşılaşırsa

Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):

1. Kilitlenme döküm dosyalarını `c:\dumps`tutmak için bir klasör oluşturun. Uygulama havuzunun klasöre yazma erişimi olmalıdır.
1. [Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:
   * Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *W3wp. exe*için betiği çalıştırın:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *DotNet. exe*için betiği çalıştırın:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.
1. Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:
   * Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *W3wp. exe*için betiği çalıştırın:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *DotNet. exe*için betiği çalıştırın:

     ```console
     .\DisableDumps dotnet.exe
     ```

Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir. PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.

> [!WARNING]
> Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor

Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.

#### <a name="analyze-the-dump"></a>Dökümü çözümle

Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir. Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Paket önbelleklerini temizle

Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir. Bazı durumlarda, ana yükseltme yaparken, bir uygulama tutarsız paketleri kesilebilir. Bu sorunların çoğu, bu yönergeleri izleyerek düzeltilebilir:

1. *Bin* ve *obj* klasörlerini silin.
1. Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.

   Paket önbelleklerini Temizleme, [NuGet. exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komut `nuget locals all -clear`yürütülebilir. *NuGet. exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.

1. Geri yükle ve projeyi yeniden derleyin.
1. Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Azure belgeleri

* [ASP.NET Core için Application Insights](/azure/application-insights/app-insights-asp-net-core)
* [Visual Studio 'Yu kullanarak Azure App Service Web uygulamasının sorunlarını giderme bölümünde uzaktan hata ayıklama Web Apps bölümü](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Azure App Service tanılamada genel bakış](/azure/app-service/app-service-diagnostics)
* [Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme](/azure/app-service/web-sites-monitor)
* [Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure Web uygulamalarınızda "502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" HTTP hatalarında sorun giderme](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Azure App Service web uygulamasında yavaş performans sorunlarını giderme](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure 'da Web Apps için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web uygulaması korumalı alanı (App Service çalışma zamanı yürütme sınırlamaları)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Cuma: Azure App Service tanılama ve sorun giderme deneyimi (12 dakikalık video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Visual Studio belgeleri

* [Visual Studio 2017 ' de Azure 'da IIS 'de uzaktan hata ayıklama ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)
* [Visual Studio 2017 ' de uzak IIS bilgisayarında uzaktan hata ayıklama ASP.NET Core](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Visual Studio kullanarak hata ayıklamayı öğrenin](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Visual Studio Code belgeleri

* [Visual Studio Code ile hata ayıklama](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Bu makalede, bir uygulama Azure App Service veya IIS 'ye dağıtıldığında hataların nasıl tanılanacağı hakkında genel uygulama başlatma hataları ve yönergeleri hakkında bilgi verilmektedir:

[Uygulama başlatma hataları](#app-startup-errors)  
Ortak Başlangıç HTTP durum kodu senaryolarını açıklar.

[Azure App Service sorunlarını giderme](#troubleshoot-on-azure-app-service)  
Azure App Service dağıtılan uygulamalar için sorun giderme önerisi sağlar.

[IIS üzerinde sorun giderme](#troubleshoot-on-iis)  
IIS 'ye dağıtılan veya IIS Express yerel olarak çalışan uygulamalar için sorun giderme önerisi sağlar. Bu kılavuz hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.

[Paket önbelleklerini temizle](#clear-package-caches)  
Önemli güncelleştirmeler gerçekleştirirken veya paket sürümlerini değiştirirken ne yapmanız gerektiğini açıklar.

[Ek Kaynaklar](#additional-resources)  
Ek sorun giderme konularını listeler.

## <a name="app-startup-errors"></a>Uygulama başlatma hataları

Visual Studio 'da bir ASP.NET Core projesi, hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırmak için varsayılan değerdir. Yerel olarak hata ayıklamada oluşan *502,5 Işlem hatası* , bu konudaki öneri kullanılarak tanılanabilir.

### <a name="40314-forbidden"></a>403,14 yasak

Uygulama başlatılamıyor. Aşağıdaki hata günlüğe kaydedilir:

```
The Web server is configured to not list the contents of this directory.
```

Hata genellikle barındırma sisteminde, aşağıdaki senaryolardan birini içeren bozuk bir dağıtım nedeniyle oluşur:

* Uygulama, barındırma sisteminde yanlış klasöre dağıtılır.
* Dağıtım işlemi, uygulamanın tüm dosyalarını ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.
* *Web. config* dosyası dağıtımda yok veya *Web. config* dosyası içerikleri hatalı biçimlendirilmiş.

Aşağıdaki adımları uygulayın:

1. Tüm dosya ve klasörleri barındırma sistemindeki dağıtım klasöründen silin.
1. Visual Studio, PowerShell veya el ile dağıtım gibi normal dağıtım yönteminizi kullanarak, uygulamanın *Yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:
   * *Web. config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.
   * Azure App Service barındırırken, uygulamanın `D:\home\site\wwwroot` klasörüne dağıtıldığını doğrulayın.
   * Uygulama IIS tarafından barındırılıyorsa, uygulamanın **IIS yöneticisinin** **temel ayarlarında**gösterilen IIS **fiziksel yoluna** dağıtıldığını doğrulayın.
1. Barındırma sistemindeki dağıtımı projenin *Yayımla* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtıldığını doğrulayın.

Yayımlanan ASP.NET Core uygulamasının düzeni hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/directory-structure>. *Web. config* dosyası hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.

### <a name="500-internal-server-error"></a>500 İç Sunucu Hatası

Uygulamayı başlatır, ancak bir hata sunucu isteği yerine getirmesini önler.

Bu hata, başlatma sırasında veya bir yanıt oluşturulurken uygulamanın kod içinde oluşur. Yanıtta içerik yok olabilir veya Yanıt, tarayıcıda *500 Iç sunucu hatası* olarak görünebilir. Uygulama olay günlüğü, genellikle uygulama normal şekilde çalışmaya belirtir. Sunucunun açısından bakıldığında, doğru olmasıdır. Uygulama başladı, ancak geçerli bir yanıt oluşturulamıyor. Uygulamayı sunucuda bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Core modülü stdout günlüğünü etkinleştirin.

### <a name="5025-process-failure"></a>502.5 işlem hatası

Çalışan işlemi başarısız olur. Uygulama başlamaz.

[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) çalışan işlemini başlatmaya çalışır, ancak başlatılamıyor. İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.

Ortak bir hata durumu, uygulamanın mevcut olmayan ASP.NET Core paylaşılan framework sürümü hedefleme nedeniyle yanlış yapılandırılmış ' dir. Hangi sürümlerinin bir ASP.NET Core paylaşılan çerçeve hedef makinede yüklü olduğunu denetleyin. *Paylaşılan çerçeve* , makinede yüklü olan ve `Microsoft.AspNetCore.App`gibi bir metapackage tarafından başvurulan derleme ( *. dll* dosyaları) kümesidir. Metapackage başvurusu, gerekli en düşük sürümü belirtebilir. Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Bir barındırma veya uygulamanın yanlış yapılandırılması, çalışan işleminin başarısız olmasına neden olduğunda, *502,5 Işlem hata* hatası sayfası döndürülür:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Uygulama (hata kodu: '0x800700c1') başlatılamadı.

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Uygulamanın derlemesi ( *. dll*) yüklenemediğinden uygulama başlatılamadı.

W3wp/ıısexpress işlemi ile yayımlanan uygulama arasındaki bir bit genişliği uyuşmazlığı olduğunda bu hata oluşur.

Uygulama havuzunun 32-bit ayarının doğru olduğundan emin olun:

1. IIS yöneticisinin **uygulama havuzlarında**uygulama havuzunu seçin.
1. **Eylemler** panelinde **uygulama havuzunu Düzenle** altında **Gelişmiş ayarlar** ' ı seçin.
1. **Enable 32 bit uygulamalarını**ayarla:
   * 32-bit (x86) bir uygulama dağıtıyorsanız, değeri `True`olarak ayarlayın.
   * 64 bit (x64) uygulaması dağıtıyorsanız, değeri `False`olarak ayarlayın.

Proje dosyasındaki `<Platform>` MSBuild özelliği ile uygulamanın yayınlanan bit durumuyla ilgili bir çakışma olmadığını doğrulayın.

### <a name="connection-reset"></a>Bağlantı sıfırlama

Üstbilgiler gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Iç sunucu hatası** gönderebilmesi için çok geç olur. Bu durum, genellikle bir yanıt için karmaşık nesne serileştirme sırasında bir hata oluştuğunda gerçekleşir. Bu tür bir hata, istemcide bir *bağlantı sıfırlama* hatası olarak görüntülenir. [Uygulama günlüğü](xref:fundamentals/logging/index) bu tür hataların giderilmesine yardımcı olabilir.

### <a name="default-startup-limits"></a>Varsayılan başlangıç sınırları

[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) varsayılan bir *StartupTimeLimit* 120 saniye ile yapılandırılır. Varsayılan değer olarak sol uygulama modülü bir işlem hatası oturum önce başlatmak için iki dakika sürebilir. Modülü yapılandırma hakkında daha fazla bilgi için bkz. [aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Azure App Service sorunlarını giderme

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Uygulama olay günlüğü (Azure App Service)

Uygulama olay günlüğüne erişmek için Azure portal **sorunları Tanıla ve çöz** dikey penceresini kullanın:

1. Azure portal uygulama **Hizmetleri**' nde uygulamayı açın.
1. **Tanıla ve sorunları çöz '** ü seçin.
1. **Tanılama araçları** başlığını seçin.
1. **Destek Araçları**' nın altında, **uygulama olayları** düğmesini seçin.
1. **Kaynak** sütununda *IIS AspNetCoreModule* veya *IIS Aspnetcoremodule v2* girişi tarafından belirtilen en son hatayı inceleyin.

**Sorunları Tanıla ve çöz** dikey penceresini kullanmanın bir alternatifi, uygulama olay günlüğü dosyasını doğrudan [kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:

1. **Gelişmiş araçları** **geliştirme araçları** alanında açın. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.
1. **LogFiles** klasörünü açın.
1. *EventLog. xml* dosyasının yanındaki kurşun kalem simgesini seçin.
1. Günlüğü inceleyin. En son olayları görmek için günlüğün en altına gidin.

### <a name="run-the-app-in-the-kudu-console"></a>Uygulamayı kudu konsolunda çalıştırma

Başlatma hataları birçok yararlı bilgiler uygulama olay günlüğü'ndeki üretmediği. Bu hatayı saptamak için, uygulamayı [kudu](https://github.com/projectkudu/kudu/wiki) uzaktan yürütme konsolu 'nda çalıştırabilirsiniz:

1. **Gelişmiş araçları** **geliştirme araçları** alanında açın. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.

#### <a name="test-a-32-bit-x86-app"></a>32 bit (x86) uygulamayı test etme

**Geçerli yayın**

1. `cd d:\home\site\wwwroot`
1. Uygulamayı çalıştırın:
   * Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:

     ```console
     {ASSEMBLY NAME}.exe
     ```

Uygulamadan alınan konsol çıktısı, tüm hataları gösteren kudu konsoluna gönderilir.

**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**

*ASP.NET Core {VERSION} (x86) çalışma zamanı site uzantısının yüklenmesini gerektirir.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` çalışma zamanı sürümüdür)
1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Uygulamadan alınan konsol çıktısı, tüm hataları gösteren kudu konsoluna gönderilir.

#### <a name="test-a-64-bit-x64-app"></a>64 bit (x64) uygulamayı test etme

**Geçerli yayın**

* Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:
  1. `cd D:\Program Files\dotnet`
  1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:
  1. `cd D:\home\site\wwwroot`
  1. Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`

Uygulamadan alınan konsol çıktısı, tüm hataları gösteren kudu konsoluna gönderilir.

**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**

*ASP.NET Core {VERSION} (x64) çalışma zamanı site uzantısını yüklemeyi gerektirir.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` çalışma zamanı sürümüdür)
1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Uygulamadan alınan konsol çıktısı, tüm hataları gösteren kudu konsoluna gönderilir.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET Core modülü stdout günlüğü (Azure App Service)

ASP.NET Core Module stdout günlüğü genellikle uygulama olay günlüğünde bulunmayan yararlı hata iletilerini kaydeder. Stdout günlükleri görüntülemek ve etkinleştirmek için:

1. Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.
1. **Sorun kategorisini seçin**altında **Web uygulaması aşağı** düğmesini seçin.
1. **Önerilen çözümler** ' de **stdout günlük yeniden yönlendirmeyi etkinleştirmek**>, **Web. config dosyasını düzenlemek için kudu konsolunu açmak**üzere düğmeyi seçin.
1. Kudu **Tanılama konsolunda**, dosyaları **Wwwroot** > yol **sitesine** açın. Listenin altındaki *Web. config* dosyasını açığa çıkarmak için aşağı kaydırın.
1. *Web. config* dosyasının yanındaki kurşun kalem simgesine tıklayın.
1. **StdoutLogEnabled** olarak ayarlayın ve **stdoutLogFile** yolunu `true` olarak değiştirin: `\\?\%home%\LogFiles\stdout`.
1. Güncelleştirilmiş *Web. config* dosyasını kaydetmek için **Kaydet** ' i seçin.
1. Uygulamaya bir istek oluşturun.
1. Azure portalına dönün. **GELIŞTIRME araçları** alanında **Gelişmiş Araçlar** dikey penceresini seçin. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.
1. **LogFiles** klasörünü seçin.
1. **Değiştirilen** sütunu inceleyin ve son değiştirilme tarihiyle stdout günlüğünü düzenlemek için kalem simgesini seçin.
1. Günlük dosyası açıldığında hata görüntülenir.

Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:

1. Kudu **Tanılama konsolunda**, *Web. config* dosyasını açığa çıkarmak için **Wwwroot** > yolu **sitesine** dönün. Kalem simgesini seçerek **Web. config** dosyasını tekrar açın.
1. `false`için **stdoutLogEnabled** ayarlayın.
1. Dosyayı kaydetmek için **Kaydet** ' i seçin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Uygulama veya sunucu başarısızlığı için hata stdout günlüğünü devre dışı bırakmak için yol açabilir. Günlük dosyası boyutunu sınırlama yok veya oluşturulan günlük dosyası sayısı yoktur. Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlüğünü kullanın.
>
> Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Yavaş veya askıda olan uygulama (Azure App Service)

Bir uygulama bir istek üzerinde yavaş bir şekilde yanıt verdiğinde veya Kilitlenmelerinde, aşağıdaki makalelere bakın:

* [Azure App Service web uygulamasında yavaş performans sorunlarını giderme](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure Web uygulamasında aralıklı özel durum sorunları veya performans sorunları için döküm yakalamak üzere kilitlenme tanılayıcı site uzantısı 'nı kullanın](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>İzleme kanatları

İzleme dikey pencereleri, konusunda daha önce açıklanan yöntemlere alternatif bir sorun giderme deneyimi sağlar. Bu kanatlar 500 serisi hataları tanılamak için kullanılabilir.

ASP.NET Core uzantılarının yüklü olduğunu doğrulayın. Uzantılar yüklü değilse, bunları el ile yükleyebilirsiniz:

1. **GELIŞTIRME araçları** dikey penceresinde **Uzantılar** dikey penceresini seçin.
1. **ASP.NET Core uzantıları** listede görünmelidir.
1. Uzantılar yüklü değilse, **Ekle** düğmesini seçin.
1. Listeden **ASP.NET Core uzantılarını** seçin.
1. Yasal koşulları kabul etmek için **Tamam ' ı** seçin.
1. **Uzantı Ekle** dikey penceresinde **Tamam ' ı** seçin.
1. Bilgilendirici bir açılan ileti, uzantıların başarıyla yüklenip yüklenmediğini gösterir.

Stdout günlüğü etkinleştirilmemişse, şu adımları izleyin:

1. Azure portal, **GELIŞTIRME araçları** alanındaki **Gelişmiş Araçlar** dikey penceresini seçin. **Git&rarr;** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.
1. Dosya yolu > **sitesindeki** klasörleri **açın ve listenin** altındaki *Web. config* dosyasını açığa çıkarmak için aşağı kaydırın.
1. *Web. config* dosyasının yanındaki kurşun kalem simgesine tıklayın.
1. **StdoutLogEnabled** olarak ayarlayın ve **stdoutLogFile** yolunu `true` olarak değiştirin: `\\?\%home%\LogFiles\stdout`.
1. Güncelleştirilmiş *Web. config* dosyasını kaydetmek için **Kaydet** ' i seçin.

Tanılama günlüğünü etkinleştirmek için ilerleyin:

1. Azure portal **tanılama günlükleri** dikey penceresini seçin.
1. **Uygulama günlüğü (dosya sistemi)** ve **ayrıntılı hata iletileri**için **bir anahtar seçin** . Dikey pencerenin üst kısmındaki **Kaydet** düğmesini seçin.
1. Başarısız istek izlemeyi, başarısız Istek olayı arabelleğe alma (FREB) günlüğü olarak da bilinen bir şekilde eklemek için **,** **başarısız istek izleme**anahtarını seçin.
1. Portalda **tanılama günlükleri** dikey penceresinde hemen listelenen **günlük akışı** dikey penceresini seçin.
1. Uygulamaya bir istek oluşturun.
1. Günlük akışı verileri içinde hatanın nedeni belirtilir.

Sorun giderme tamamlandığında stdout günlüğünü devre dışı bıraktığınızdan emin olun.

Başarısız istek izleme günlüklerini görüntülemek için (FREB günlükleri):

1. Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.
1. Kenar çubuğunun **Destek Araçları** alanından **başarısız istek izleme günlüklerini** seçin.

[Azure App Service konusundaki Web uygulamaları için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure 'Daki Web Apps Için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) bölümündeki başarısız istek izlemeleri bölümüne bakın: daha fazla bilgi için nasıl yaparım? başarısız istek izlemeyi açın.

Daha fazla bilgi için bkz. [Azure App Service Web Apps için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Uygulama veya sunucu başarısızlığı için hata stdout günlüğünü devre dışı bırakmak için yol açabilir. Günlük dosyası boyutunu sınırlama yok veya oluşturulan günlük dosyası sayısı yoktur.
>
> ASP.NET Core uygulamanızı rutin günlüğü için günlük dosyası boyutunu sınırlar ve günlükleri döndürür bir günlük kitaplığını kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>IIS 'de sorun giderme

### <a name="application-event-log-iis"></a>Uygulama olay günlüğü (IIS)

Uygulama olay günlüğüne erişemedi:

1. Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.
1. **Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.
1. Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.
1. Başarısız olan uygulama ile ilişkili hataları arayın. Hataların, *kaynak* sütununda *IIS aspnetcore modülünün* veya *IIS Express aspnetcore modülünün* bir değeri vardır.

### <a name="run-the-app-at-a-command-prompt"></a>Uygulamayı bir komut isteminde aşağıdakini çalıştırın

Başlatma hataları birçok yararlı bilgiler uygulama olay günlüğü'ndeki üretmediği. Bazı hataların nedeni, barındıran sistemde bir komut isteminde uygulamayı çalıştırarak bulabilirsiniz.

#### <a name="framework-dependent-deployment"></a>Framework bağımlı dağıtım

Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:

1. Bir komut isteminde, dağıtım klasörüne gidin ve uygulamanın derlemesini *DotNet. exe*ile yürüterek uygulamayı çalıştırın. Aşağıdaki komutta, \<assembly_name >: `dotnet .\<assembly_name>.dll`için uygulama derlemesinin adını yerine koyun.
1. Konsol çıkışını herhangi bir hata gösteren uygulamadan konsol penceresine yazılır.
1. Uygulamaya bir istek yaparken, hataları meydana gelirse, burada Kestrel dinlediği bağlantı noktası ve ana bilgisayar için istekte bulunmak. Varsayılan konak ve gönderi kullanarak `http://localhost:5000/`bir istek yapın. Uygulamayı, normalde Kestrel uç nokta adresindeki yanıt verirse, sorun barındırma yapılandırmasında ve büyük olasılıkla daha az uygulama içinde ilgili daha yüksektir.

#### <a name="self-contained-deployment"></a>Kendi içinde dağıtım

Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:

1. Bir komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir dosyayı çalıştırın. Aşağıdaki komutta, \<assembly_name >: `<assembly_name>.exe`için uygulama derlemesinin adını yerine koyun.
1. Konsol çıkışını herhangi bir hata gösteren uygulamadan konsol penceresine yazılır.
1. Uygulamaya bir istek yaparken, hataları meydana gelirse, burada Kestrel dinlediği bağlantı noktası ve ana bilgisayar için istekte bulunmak. Varsayılan konak ve gönderi kullanarak `http://localhost:5000/`bir istek yapın. Uygulamayı, normalde Kestrel uç nokta adresindeki yanıt verirse, sorun barındırma yapılandırmasında ve büyük olasılıkla daha az uygulama içinde ilgili daha yüksektir.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET Core Module stdout günlüğü (IIS)

Stdout günlükleri görüntülemek ve etkinleştirmek için:

1. Barındıran sistemde sitenin dağıtım klasörüne gidin.
1. *Günlükler* klasörü yoksa, klasörü oluşturun. MSBuild 'in dağıtımdaki *Günlükler* klasörünü otomatik olarak oluşturmak üzere nasıl etkinleştirileceği hakkında yönergeler için, bkz. [Dizin yapısı](xref:host-and-deploy/directory-structure) konusu.
1. *Web. config* dosyasını düzenleyin. **StdoutLogEnabled** öğesini `true` olarak ayarlayın ve **stdoutLogFile** yolunu *Günlükler* klasörünü işaret etmek üzere değiştirin (örneğin, `.\logs\stdout`). yoldaki `stdout` günlük dosyası adı önekidir. Oturum oluşturulduğunda bir zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir. Dosya adı ön eki olarak `stdout` kullanarak, tipik bir günlük dosyası, *stdout_20180205184032_5412. log*olarak adlandırılır.
1. Uygulama havuzunuzun kimliğinin *Günlükler* klasörü için yazma izinlerine sahip olduğundan emin olun.
1. Güncelleştirilmiş *Web. config* dosyasını kaydedin.
1. Uygulamaya bir istek oluşturun.
1. *Günlükler* klasörüne gidin. Bulun ve en son stdout günlüğü'nü açın.
1. Hatalar için günlüğü inceleyin.

Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:

1. *Web. config* dosyasını düzenleyin.
1. `false`için **stdoutLogEnabled** ayarlayın.
1. Dosyayı kaydedin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Uygulama veya sunucu başarısızlığı için hata stdout günlüğünü devre dışı bırakmak için yol açabilir. Günlük dosyası boyutunu sınırlama yok veya oluşturulan günlük dosyası sayısı yoktur.
>
> ASP.NET Core uygulamanızı rutin günlüğü için günlük dosyası boyutunu sınırlar ve günlükleri döndürür bir günlük kitaplığını kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="enable-the-developer-exception-page"></a>Geliştirici özel durumu sayfasını etkinleştir

`ASPNETCORE_ENVIRONMENT` ortam değişkeni, uygulamayı geliştirme ortamında çalıştırmak için [Web. config dosyasına eklenebilir](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) . Ortam, ana bilgisayar tasarımcısında `UseEnvironment` tarafından uygulama başlangıcında geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında [Geliştirici özel durum sayfasının](xref:fundamentals/error-handling) görünmesine izin verir.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

`ASPNETCORE_ENVIRONMENT` için ortam değişkenini ayarlamak yalnızca Internet 'e açık olmayan hazırlama ve test etme sunucularında kullanılması önerilir. Sorun giderme işleminden sonra *Web. config* dosyasından ortam değişkenini kaldırın. *Web. config*'de ortam değişkenlerini ayarlama hakkında daha fazla bilgi Için, [Aspnetcore 'un EnvironmentVariables alt öğesi](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)bölümüne bakın.

### <a name="obtain-data-from-an-app"></a>Bir uygulamadan veri alın

Bir uygulama isteklerini yanıtlayabileceği ise, istek, bağlantı ve ek veri terminal satır içi ara yazılımın kullanılması uygulamayı edinin. Daha fazla bilgi ve örnek kod için bkz. <xref:test/troubleshoot#obtain-data-from-an-app>.

### <a name="slow-or-hanging-app-iis"></a>Yavaş veya askıda olan uygulama (IIS)

*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.

#### <a name="app-crashes-or-encounters-an-exception"></a>Uygulama kilitleniyor veya bir özel durumla karşılaşırsa

Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):

1. Kilitlenme döküm dosyalarını `c:\dumps`tutmak için bir klasör oluşturun. Uygulama havuzunun klasöre yazma erişimi olmalıdır.
1. [Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:
   * Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *W3wp. exe*için betiği çalıştırın:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *DotNet. exe*için betiği çalıştırın:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.
1. Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:
   * Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *W3wp. exe*için betiği çalıştırın:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *DotNet. exe*için betiği çalıştırın:

     ```console
     .\DisableDumps dotnet.exe
     ```

Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir. PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.

> [!WARNING]
> Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor

Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.

#### <a name="analyze-the-dump"></a>Dökümü çözümle

Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir. Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Paket önbelleklerini temizle

Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir. Bazı durumlarda, ana yükseltme yaparken, bir uygulama tutarsız paketleri kesilebilir. Bu sorunların çoğu, bu yönergeleri izleyerek düzeltilebilir:

1. *Bin* ve *obj* klasörlerini silin.
1. Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.

   Paket önbelleklerini Temizleme, [NuGet. exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komut `nuget locals all -clear`yürütülebilir. *NuGet. exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.

1. Geri yükle ve projeyi yeniden derleyin.
1. Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Azure belgeleri

* [ASP.NET Core için Application Insights](/azure/application-insights/app-insights-asp-net-core)
* [Visual Studio 'Yu kullanarak Azure App Service Web uygulamasının sorunlarını giderme bölümünde uzaktan hata ayıklama Web Apps bölümü](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Azure App Service tanılamada genel bakış](/azure/app-service/app-service-diagnostics)
* [Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme](/azure/app-service/web-sites-monitor)
* [Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure Web uygulamalarınızda "502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" HTTP hatalarında sorun giderme](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Azure App Service web uygulamasında yavaş performans sorunlarını giderme](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure 'da Web Apps için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web uygulaması korumalı alanı (App Service çalışma zamanı yürütme sınırlamaları)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Cuma: Azure App Service tanılama ve sorun giderme deneyimi (12 dakikalık video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Visual Studio belgeleri

* [Visual Studio 2017 ' de Azure 'da IIS 'de uzaktan hata ayıklama ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)
* [Visual Studio 2017 ' de uzak IIS bilgisayarında uzaktan hata ayıklama ASP.NET Core](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Visual Studio kullanarak hata ayıklamayı öğrenin](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Visual Studio Code belgeleri

* [Visual Studio Code ile hata ayıklama](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
