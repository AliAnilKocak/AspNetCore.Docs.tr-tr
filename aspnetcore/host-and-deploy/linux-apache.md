---
title: Apache ile Linux üzerinde ASP.NET Core barındırma
author: guardrex
description: HTTP trafiğini Kestrel üzerinde çalışan bir ASP.NET Core Web uygulamasına yönlendirmek için CentOS üzerinde bir ters proxy sunucusu olarak Apache 'yi ayarlamayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 11/05/2019
uid: host-and-deploy/linux-apache
ms.openlocfilehash: fce91db736908e433ba6803319aa8984bb68a554
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659879"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Apache ile Linux üzerinde ASP.NET Core barındırma

Sağlayan- [Shayne Boyer](https://github.com/spboyer)

Bu kılavuzu kullanarak, HTTP trafiğinin [Kestrel](xref:fundamentals/servers/kestrel) Server üzerinde çalışan bir ASP.NET Core Web uygulamasına yönlendirilmesini sağlamak Için [CentOS 7](https://www.centos.org/) ' de bir ters proxy sunucusu olarak [Apache](https://httpd.apache.org/) 'yi ayarlamayı öğrenin. [Mod_proxy uzantısı](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) ve ilgili Modüller sunucunun ters proxy 'sini oluşturur.

## <a name="prerequisites"></a>Önkoşullar

* Sudo ayrıcalığına sahip standart bir kullanıcı hesabıyla CentOS 7 çalıştıran sunucu.
* .NET Core çalışma zamanını sunucuya yükler.
   1. [.NET Core tüm indirmeler sayfasını](https://www.microsoft.com/net/download/all)ziyaret edin.
   1. **Çalışma zamanı**altındaki listeden en son önizleme dışı çalışma zamanını seçin.
   1. CentOS/Oracle yönergelerini seçin ve izleyin.
* Mevcut bir ASP.NET Core uygulaması.

## <a name="publish-and-copy-over-the-app"></a>Uygulama üzerinde Yayımla ve Kopyala

Uygulamayı [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)için yapılandırın.

Uygulama yerel olarak çalıştırılır ve güvenli bağlantı (HTTPS) yapmak üzere yapılandırılmamışsa aşağıdaki yaklaşımlardan birini benimseyin:

* Uygulamayı güvenli yerel bağlantıları işleyecek şekilde yapılandırın. Daha fazla bilgi için [https yapılandırma](#https-configuration) bölümüne bakın.
* *Properties/launchSettings. JSON* dosyasındaki `applicationUrl` özelliğinden `https://localhost:5001` (varsa) kaldırın.

Bir uygulamayı sunucuda çalışabilecek bir dizine (örneğin, *bin/Release/&lt;target_framework_moniker&gt;/Publish*) paketlemek için geliştirme ortamından [DotNet Publish](/dotnet/core/tools/dotnet-publish) çalıştırın:

```dotnetcli
dotnet publish --configuration Release
```

Uygulama, sunucuda .NET Core çalışma zamanının bakımını yapmayı tercih ediyorsanız, [kendi kendine içerilen bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) olarak da yayımlanabilir.

ASP.NET Core uygulamasını, kuruluşun iş akışını (örneğin, SCP, SFTP) tümleştiren bir aracı kullanarak sunucuya kopyalayın. *Var* dizini altında Web uygulamalarının (örneğin, *var/www/HelloApp*) yerini bulmak yaygındır.

> [!NOTE]
> Bir üretim dağıtım senaryosunda, sürekli tümleştirme iş akışı, uygulamayı yayımlama ve varlıkları sunucuya kopyalama işini yapar.

## <a name="configure-a-proxy-server"></a>Proxy sunucusu yapılandırma

Ters proxy, dinamik Web uygulamaları sunmak için ortak bir kurulumtir. Ters proxy, HTTP isteğini sonlandırır ve ASP.NET uygulamasına iletir.

Proxy sunucusu, istemci isteklerini istekleri yerine başka bir sunucuya ileten bir sunucu olur. Ters proxy, genellikle rastgele istemciler adına sabit bir hedefe iletilir. Bu kılavuzda Apache, Kestrel 'in ASP.NET Core uygulamasına hizmet veren aynı sunucuda çalışan ters proxy olarak yapılandırılmıştır.

İstekler ters proxy tarafından iletileceği için, [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki [Iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer) kullanın. Ara yazılım, `X-Forwarded-Proto` üst bilgisini kullanarak `Request.Scheme`güncelleştirir, böylece yeniden yönlendirme URI 'Leri ve diğer güvenlik ilkeleri doğru çalışır.

Bir şemaya bağlı kimlik doğrulama, bağlantı oluşturma, yeniden yönlendirme ve coğrafi konum gibi herhangi bir bileşen, Iletilen üstbilgiler ara yazılımı çağrıldıktan sonra yerleştirilmelidir. Genel bir kural olarak, Iletilen üstbilgiler ara yazılımı, tanılama ve hata işleme ara yazılımı dışında diğer ara yazılım ile önce çalışmalıdır. Bu sıralama, iletilen üst bilgi bilgilerine bağlı olan ara yazılımın işleme için üst bilgi değerlerini kullanmasını sağlar.

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> veya benzer kimlik doğrulama düzeni ara yazılımını çağırmadan önce `Startup.Configure` <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> yöntemi çağırın. `X-Forwarded-For` ve `X-Forwarded-Proto` üstbilgilerini iletmek için ara yazılımı yapılandırın:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Ara yazılım için <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> belirtilmemişse, iletmek için varsayılan üstbilgiler `None`.

Standart localhost adresi (127.0.0.1) dahil olmak üzere geri döngü adreslerinde çalışan proxy 'ler (127.0.0.0/8, [:: 1]), varsayılan olarak güvenilirdir. Kuruluş içindeki diğer güvenilir proxy 'ler veya ağlar, Internet ve Web sunucusu arasında istekleri ele alıyorsa, bunları <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> veya <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> listesine ekleyin. Aşağıdaki örnek, alana 10.0.0.100 adresindeki Iletilen üstbilgiler ara sunucusuna `Startup.ConfigureServices``KnownProxies` IP adresinde bir güvenilen ara sunucu ekler:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-apache"></a>Apache 'yi yükler

CentOS paketlerini en son kararlı sürümlerine güncelleştirin:

```bash
sudo yum update -y
```

Tek bir `yum` komutuyla, CentOS 'a Apache Web sunucusunu yükler:

```bash
sudo yum -y install httpd mod_ssl
```

Komutu çalıştırdıktan sonra örnek çıkış:

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> Bu örnekte, CentOS 7 sürümü 64 bit olduğundan çıkış httpd. 86_64 ' i yansıtır. Apache 'nin yüklendiğini doğrulamak için, bir komut isteminden `whereis httpd` çalıştırın.

### <a name="configure-apache"></a>Apache yapılandırma

Apache için yapılandırma dosyaları `/etc/httpd/conf.d/` dizininde bulunur. *. Conf* uzantısına sahip herhangi bir dosya, modülleri yüklemek için gereken yapılandırma dosyalarını içeren `/etc/httpd/conf.modules.d/`içindeki modül yapılandırma dosyalarına ek olarak alfabetik sırada işlenir.

Uygulama için *HelloApp. conf*adlı bir yapılandırma dosyası oluşturun:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

`VirtualHost` bloğu, sunucuda bir veya daha fazla dosyada birden çok kez görünebilir. Yukarıdaki yapılandırma dosyasında Apache, 80 numaralı bağlantı noktasında genel trafiği kabul eder. Etki alanı `www.example.com` sunulmakta ve `*.example.com` diğer adı aynı Web sitesine çözümleniyor. Daha fazla bilgi için bkz. [ad tabanlı sanal konak desteği](https://httpd.apache.org/docs/current/vhosts/name-based.html) . İstekler, kökte, sunucunun bağlantı noktası 5000 ' den 127.0.0.1 ' de sunucu üzerinden alınır. İki yönlü iletişim için `ProxyPass` ve `ProxyPassReverse` gereklidir. Kestrel 'in IP/bağlantı noktasını değiştirmek için bkz. [Kestrel: Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> **VirtualHost** bloğunda uygun bir [ServerName yönergesi](https://httpd.apache.org/docs/current/mod/core.html#servername) belirtmemesi, uygulamanızı güvenlik açıklarına karşı kullanıma sunar. Alt etki alanı joker karakteri bağlama (örneğin, `*.example.com`), tüm üst etki alanını (güvenlik açığı olan `*.com`aksine) kontrol ediyorsanız bu güvenlik riskini ortadan yapmaz. Bkz: [rfc7230 bölümü-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) daha fazla bilgi için.

Günlüğe kaydetme, `ErrorLog` ve `CustomLog` yönergeleri kullanılarak `VirtualHost` başına yapılandırılabilir. `ErrorLog`, sunucunun hataları günlüğe kaydettiği konumdur ve `CustomLog` dosya adını ve günlük dosyasının biçimini ayarlar. Bu durumda istek bilgileri günlüğe kaydedilir. Her istek için bir satır vardır.

Dosyayı kaydedin ve yapılandırmayı test edin. Her şey geçerse yanıt `Syntax [OK]`olmalıdır.

```bash
sudo service httpd configtest
```

Apache 'i yeniden Başlat:

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>Uygulamayı izleme

Apache, artık `http://localhost:80` yapılan istekleri, `http://127.0.0.1:5000`'de Kestrel üzerinde çalışan ASP.NET Core uygulamasına iletmek üzere kuruldu. Ancak, Kestrel işlemini yönetmek için Apache ayarlanmamış. Temel Web uygulamasını başlatmak ve izlemek için *systemd* ve hizmet dosyası oluşturma ' yı kullanın. *systemd* , işlem başlatmak, durdurmak ve yönetmek için birçok güçlü özellik sağlayan bir init sistemidir.

### <a name="create-the-service-file"></a>Hizmet dosyasını oluşturma

Hizmet tanımı dosyasını oluşturun:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Uygulama için örnek bir hizmet dosyası:

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

Kullanıcı *Apache* yapılandırması tarafından kullanılmıyorsa, önce kullanıcının oluşturulması ve dosyaların uygun sahipliğinin verilmesi gerekir.

Uygulamanın ilk kesme sinyalini aldıktan sonra kapanması için bekleyeceği süreyi yapılandırmak için `TimeoutStopSec` kullanın. Uygulama bu dönemde kapanmazsa, uygulamayı sonlandırmak için SIGKıLL çıkarılır. Değeri unitless saniyeler (örneğin, `150`), zaman aralığı değeri (örneğin, `2min 30s`) veya `infinity` zaman aşımını devre dışı bırakmak için girin. `TimeoutStopSec` varsayılan değer olan yönetici yapılandırma dosyasında (*systemd-System. conf*, *System. conf. d*, *systemd-User. conf*, *User. conf. d*) `DefaultTimeoutStopSec` değerini alır. Çoğu dağıtım için varsayılan zaman aşımı 90 saniyedir.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Yapılandırma sağlayıcılarının ortam değişkenlerini okuyabilmesi için bazı değerler (örneğin, SQL bağlantı dizeleri) kaçışmalıdır. Yapılandırma dosyasında kullanılmak üzere uygun bir kaçış değeri oluşturmak için aşağıdaki komutu kullanın:

```console
systemd-escape "<value-to-escape>"
```

İki nokta (`:`) ayırıcı, ortam değişkeni adlarında desteklenmez. İki nokta üst üste yerine çift alt çizgi (`__`) kullanın. Ortam değişkenleri [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables-configuration-provider) , ortam değişkenleri yapılandırmaya okurken çift alt çizgileri iki nokta üst üste dönüştürür. Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` `ConnectionStrings__DefaultConnection`olarak hizmet tanımı dosyasına ayarlanır:

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Dosyayı kaydedin ve hizmeti etkinleştirin:

```bash
sudo systemctl enable kestrel-helloapp.service
```

Hizmeti başlatın ve çalıştığını doğrulayın:

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Ters proxy yapılandırılmış ve *systemd*üzerinden yönetilen Kestrel, Web uygulaması tam olarak yapılandırılır ve `http://localhost`adresindeki yerel makinedeki bir tarayıcıdan erişilebilir. Yanıt üst bilgilerini inceleyerek **sunucu** üst bilgisi, ASP.NET Core uygulamasının Kestrel tarafından sunulduğunu belirtir:

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Günlükleri görüntüleme

Kestrel kullanan Web uygulaması *systemd*kullanılarak yönetildiğinden, olaylar ve süreçler merkezi bir günlüğe kaydedilir. Ancak, bu günlük *systemd*tarafından yönetilen tüm hizmet ve işlemlere ait girişleri içerir. `kestrel-helloapp.service`özgü öğeleri görüntülemek için aşağıdaki komutu kullanın:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Zaman filtreleme için komutuyla saat seçeneklerini belirtin. Örneğin, geçerli güne filtre uygulamak veya önceki saatin girişlerini görmek için `--until 1 hour ago` `--since today` kullanın. Daha fazla bilgi için bkz. [journalctl için man sayfası](https://www.unix.com/man-page/centos/1/journalctl/).

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Veri koruma

[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulama ara yazılımı (örneğin, tanımlama bilgisi ara yazılımı) ve siteler arası istek sahteciliğini önleme (CSRF) korumaları dahil olmak üzere birkaç ASP.NET Core [middlewares](xref:fundamentals/middleware/index)tarafından kullanılır. Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, veri korumasının kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturacak şekilde yapılandırılması gerekir. Veri koruma yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılan.

Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:

* Tüm tanımlama bilgisi tabanlı kimlik doğrulama belirteçlerini geçersiz kılınır.
* Kullanıcıların, bir sonraki istekte tekrar oturum açmanız gerekir.
* Anahtar halkası ile korunan tüm veriler artık şifresi çözülebilir. Bu içerebilir [CSRF belirteçleri](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData tanımlama bilgilerini](xref:fundamentals/app-state#tempdata).

Veri korumayı, anahtar halkasını sürdürmek ve şifrelemek üzere yapılandırmak için, bkz.:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a>Uygulamanın güvenliğini sağlama

### <a name="configure-firewall"></a>Güvenlik duvarını yapılandırma

*Firewallld* , ağ bölgeleri desteğiyle güvenlik duvarını yönetmek için dinamik bir Daemon. Bağlantı noktaları ve paket filtrelemesi, Iptables tarafından hala yönetilebilir. *Firewalld* , varsayılan olarak yüklenmelidir. `yum`, paketi yüklemek veya yüklendiğini doğrulamak için kullanılabilir.

```bash
sudo yum install firewalld -y
```

Yalnızca uygulama için gerekli olan bağlantı noktalarını açmak için `firewalld` kullanın. Bu durumda, 80 ve 443 numaralı bağlantı noktası kullanılır. Aşağıdaki komutlar şunları açmak için 80 ve 443 bağlantı noktalarını kalıcı olarak ayarlar:

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Güvenlik Duvarı ayarlarını yeniden yükleyin. Varsayılan bölgedeki kullanılabilir hizmetleri ve bağlantı noktalarını kontrol edin. Seçenekler `firewall-cmd -h`inceleyerek kullanılabilir.

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a>HTTPS yapılandırması

**Uygulamayı güvenli (HTTPS) yerel bağlantılar için yapılandırma**

[DotNet Run](/dotnet/core/tools/dotnet-run) komutu uygulamanın *Özellikler/launchsettings. JSON* dosyasını kullanır, bu da uygulamayı `applicationUrl` özelliği tarafından belirtilen URL 'lerde dinlemek üzere yapılandırır (örneğin, `https://localhost:5001; http://localhost:5000`).

Aşağıdaki yaklaşımlardan birini kullanarak uygulamayı `dotnet run` komutu veya geliştirme ortamı için geliştirme sırasında (F5 veya CTRL + Visual Studio Code F5) bir sertifikayı kullanacak şekilde yapılandırın:

* [Varsayılan sertifikayı yapılandırmadan Değiştir](xref:fundamentals/servers/kestrel#configuration) (*önerilir*)
* [KestrelServerOptions. ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Güvenli (HTTPS) istemci bağlantıları için ters proxy 'yi yapılandırma**

HTTPS için Apache 'yi yapılandırmak için *mod_ssl* modülü kullanılır. *Httpd* modülü yüklendiğinde *mod_ssl* modülü de yüklendi. Yüklenmemişse, yapılandırmaya eklemek için `yum` kullanın.

```bash
sudo yum install mod_ssl
```

HTTPS 'yi zorlamak için `mod_rewrite` modülünü yükleyerek URL yeniden yazmayı etkinleştirin:

```bash
sudo yum install mod_rewrite
```

Bağlantı noktası 443 üzerinde URL yeniden yazma ve güvenli iletişim sağlamak için *HelloApp. conf* dosyasını değiştirin:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> Bu örnek, yerel olarak üretilmiş bir sertifika kullanıyor. **Sslcertificatefile** , etki alanı adı için birincil sertifika dosyası olmalıdır. **Sslcertificatekeyfile** , CSR oluşturulduğunda oluşturulan anahtar dosyası olmalıdır. **Sslcertificatechainfile** , sertifika yetkilisi tarafından sağlanan ara sertifika dosyası (varsa) olmalıdır.

Dosyayı kaydedin ve yapılandırmayı test edin:

```bash
sudo service httpd configtest
```

Apache 'i yeniden Başlat:

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Ek Apache önerileri

### <a name="additional-headers"></a>Ek üstbilgiler

Kötü amaçlı saldırılara karşı korumak için, değiştirilmesi veya eklenmesi gereken birkaç üstbilgi vardır. `mod_headers` modülünün yüklü olduğundan emin olun:

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Tıklama ve tıklama saldırılarına karşı güvenli Apache

*UI redki saldırısı*olarak da bilinen [tıklama](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), bir Web sitesi ziyaretçisinin bir bağlantı veya düğmeye Şu anda ziyaret ettiğinden farklı bir sayfada tıklanması zor olan kötü amaçlı bir saldırıya neden olur. Siteyi güvenli hale getirmek için `X-FRAME-OPTIONS` kullanın.

Tıklama saldırılarını azaltmak için:

1. *Httpd. conf* dosyasını düzenleyin:

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   Satırı `Header append X-FRAME-OPTIONS "SAMEORIGIN"`ekleyin.
1. Dosyayı kaydedin.
1. Apache 'i yeniden başlatın.

#### <a name="mime-type-sniffing"></a>MIME türü algılaması

`X-Content-Type-Options` üstbilgisi, Internet Explorer 'ın *MIME algılaması* (dosyanın içeriğinden bir dosyanın `Content-Type` belirleme) engeller. Sunucu `Content-Type` üst bilgisini `nosniff` seçenek kümesiyle `text/html` olarak ayarladıysanız, Internet Explorer içeriği dosyanın içeriğinden bağımsız olarak `text/html` olarak işler.

*Httpd. conf* dosyasını düzenleyin:

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Satırı `Header set X-Content-Type-Options "nosniff"`ekleyin. Dosyayı kaydedin. Apache 'i yeniden başlatın.

### <a name="load-balancing"></a>YükDengeleme

Bu örnek, CentOS 7 ve Kestrel üzerinde Apache 'in aynı örnek makinede nasıl ayarlanacağını ve yapılandırılacağını gösterir. Tek bir hata noktası olmaması için; *mod_proxy_balancer* kullanmak ve **VirtualHost** 'u değiştirmek, Apache proxy sunucusunun arkasındaki Web uygulamalarının birden çok örneğini yönetmeye olanak tanır.

```bash
sudo yum install mod_proxy_balancer
```

Aşağıda gösterilen yapılandırma dosyasında, `helloapp` ek bir örneği 5001 numaralı bağlantı noktasında çalışacak şekilde ayarlanır. *Proxy* bölümü, Yük Dengeleme *istekleri*için iki üyeli bir dengeleyici yapılandırması ile ayarlanır.

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>Oran limitleri

*Httpd* modülüne eklenen *mod_ratelimit*kullanarak, istemcilerin bant genişliği sınırlandırılabilir:

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

Örnek dosya, bant genişliğini kök konumu altında 600 KB/sn olarak sınırlandırır:

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>Uzun istek üst bilgisi alanları

Proxy sunucusu varsayılan ayarları, istek üst bilgisi alanlarını genellikle 8.190 bayt ile sınırlar. Bir uygulama, varsayılan değerden daha uzun bir süre gerektirebilir (örneğin, [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)kullanan uygulamalar). Daha uzun alanlar gerekliyse, proxy sunucusunun [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) yönergesi ayarlamayı gerektirir. Uygulanacak değer senaryoya bağlıdır. Daha fazla bilgi için sunucunuzun belgelerine bakın.

> [!WARNING]
> Gerekli olmadığı takdirde `LimitRequestFieldSize` varsayılan değerini artırmaz. Değerin artırılması, kötü amaçlı kullanıcılar tarafından arabellek taşması (taşma) ve hizmet reddi (DoS) saldırıları riskini artırır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Linux üzerinde .NET Core önkoşulları](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
