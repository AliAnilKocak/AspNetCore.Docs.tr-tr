---
title: ASP.NET Core Nginx ile Linux ana bilgisayar
description: "Ubuntu Kestrel üzerinde çalışan bir ASP.NET Core web uygulaması HTTP trafiği iletmek için 16.04 üzerinde ters Ara sunucu Nginx Kurulum açıklar."
author: rick-anderson
ms.author: riande
manager: wpickett
ms.custom: mvc
ms.date: 08/21/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 465f1391ef4ff9492d9aed48cb32da0659ceda41
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/24/2018
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>ASP.NET Core Nginx ile Linux ana bilgisayar

Tarafından [Sourabh Shirhatti](https://twitter.com/sshirhatti)

Bu kılavuz bir Ubuntu 16.04 sunucusunda üretime hazır ASP.NET Core ortamını ayarlama açıklar.

**Not:** Ubuntu 14.04 için *supervisord* Kestrel işlem izleme için bir çözüm olarak önerilir. *systemd* Ubuntu 14.04 üzerinde kullanılamaz. [Bu belgenin önceki sürümünü bakın](https://github.com/aspnet/Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)

Bu Kılavuzu:

* Var olan bir ASP.NET Core uygulamaya bir ters Ara sunucu arkasındaki yerleştirir.
* Kestrel web sunucusu isteklerini iletmek için ters proxy sunucuyu ayarlar.
* Web uygulaması başlangıçta bir arka plan programı olarak gerçekleştirilmesini sağlar.
* Web uygulaması yeniden yardımcı olmak için bir işlem yönetimini yapılandırır.

## <a name="prerequisites"></a>Önkoşullar

1. Sudo ayrıcalığa sahip standart kullanıcı hesabı ile bir Ubuntu 16.04 sunucuya erişimi
1. Mevcut bir ASP.NET Core uygulama

## <a name="copy-over-the-app"></a>Uygulama kopyalama

Çalıştırma `dotnet publish` bir uygulama sunucusunda çalışabilecek kendi içinde bulunan bir dizine paketlemek için geliştirme ortamı'ndan.

Kopya ne olursa olsun aracını kullanarak sunucu ASP.NET Core uygulamaya kuruluşun akışına (örneğin, SCP, FTP) tümleştirir. Uygulama, örneğin test edin:

* Komut satırından çalıştırma `dotnet <app_assembly>.dll`.
* Bir tarayıcıda gidin `http://<serveraddress>:<port>` uygulama Linux üzerinde çalıştığını doğrulayın. 
 
## <a name="configure-a-reverse-proxy-server"></a>Ters proxy sunucusunu yapılandırın

Ters proxy hizmet veren dinamik web uygulamaları için ortak bir kurulur. Ters proxy HTTP isteği sonlandırır ve ASP.NET Core uygulamaya iletir.

### <a name="why-use-a-reverse-proxy-server"></a>Ters proxy sunucusu neden kullanılır?

Kestrel, dinamik içerik ASP.NET çekirdek hizmet vermek için harikadır; Bununla birlikte, IIS, Apache veya Nginx gibi sunucuları olarak özellik zengin olarak web hizmet bölümleri değil. Ters proxy sunucusu, statik içerik sunan, istekleri önbelleğe alma, istekleri ve HTTP sunucusundan SSL sonlandırma sıkıştırma gibi iş boşaltabilir. Ters proxy sunucusu adanmış bir makinede bulunabilir veya bir HTTP sunucusu dağıtılabilir.

Bu kılavuzun amaçları Nginx tek bir örneğini kullanılır. HTTP sunucusu yanında aynı sunucu üzerinde çalışır. Gereksinimlerine bağlı olarak, farklı kurulum tarihte olabilir.

İstekleri tarafından ters proxy iletilir olduğundan `ForwardedHeaders` Ara `Microsoft.AspNetCore.HttpOverrides` paket. Bu ara yazılım güncelleştirmeleri `Request.Scheme`kullanarak `X-Forwarded-Proto` , yeniden yönlendirme URI'ler ve diğer güvenlik ilkelerini doğru çalışması için üstbilgi.

Ters proxy sunucusu kurma, kimlik doğrulaması ara yazılımı gereken `UseForwardedHeaders` ilk önce çalışmalıdır. Bu sıralama, kimlik doğrulaması ara yazılımı etkilenen değerleri kullanabilir ve doğru yeniden yönlendirme URI oluşturmak sağlar.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Çağırma `UseForwardedHeaders` yöntemi (içinde `Configure` yöntemi *haline*) çağırmadan önce `UseAuthentication` veya benzer kimlik doğrulama düzeni ara:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Çağırma `UseForwardedHeaders` yöntemi (içinde `Configure` yöntemi *haline*) çağırmadan önce `UseIdentity` ve `UseFacebookAuthentication` veya benzer kimlik doğrulama düzeni ara:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseIdentity();
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

### <a name="install-nginx"></a>Nginx yükleyin

```bash
sudo apt-get install nginx
```

> [!NOTE]
> İsteğe bağlı Nginx modülleri yüklü değilse, Nginx kaynağından derleme gerekli olabilir.

Kullanım `apt-get` Nginx yüklemek için. Yükleyici Nginx arka plan programı gibi sistem başlangıcında çalışan bir sistem V init betiği oluşturur. Nginx ilk kez yüklendiğinden bu yana, açıkça başlatılsın çalıştırarak:

```bash
sudo service nginx start
```

Giriş sayfasında Nginx için varsayılan bir tarayıcı görüntüler doğrulayın.

### <a name="configure-nginx"></a>Nginx yapılandırın

Ters Ara sunucu istekleri iletmek üzere ASP.NET Core uygulamamıza Nginx yapılandırmak için değiştirin `/etc/nginx/sites-available/default`. Bir metin düzenleyicisinde açın ve içeriği aşağıdakiyle değiştirin:

```
server {
    listen 80;
    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host $http_host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Bu Nginx yapılandırma dosyası gelen ortak bağlantı noktasından trafiğini `80` bağlantı noktasına `5000`.

Nginx yapılandırma kurulduktan sonra çalıştırmak `sudo nginx -t` yapılandırma dosyalarını söz dizimini doğrulayın. Yapılandırma dosyası sınaması başarılı olursa, çalıştırarak değişiklikleri almak için Nginx zorla `sudo nginx -s reload`.

## <a name="monitoring-the-app"></a>Uygulama izleme

Yapılan isteklerini iletmek için Kurulum sunucusudur `http://<serveraddress>:80` Kestrel çalışan ASP.NET Core uygulama açın `http://127.0.0.1:5000`. Ancak, Nginx Kestrel işlemini yönetmek için ayarlanmamış. *systemd* başlatmak ve temel web uygulaması izleme için bir hizmet dosyasını oluşturmak için kullanılabilir. *systemd* , başlatma, durdurma ve işlemlerini yönetme için çok güçlü özellikler sağlayan bir init sistemidir. 

### <a name="create-the-service-file"></a>Hizmet dosyası oluşturma

Hizmet tanımı dosyası oluşturun:

```bash
sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

Uygulama için bir örnek hizmet dosyası verilmiştir:

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/aspnetcore/hellomvc
ExecStart=/usr/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
Restart=always
RestartSec=10  # Restart service after 10 seconds if dotnet service crashes
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

**Not:** , kullanıcı *www veri* kullanılmaz yapılandırma tarafından burada tanımlanan kullanıcı ilk oluşturulmalı ve dosyaları için doğru sahipliği verilen.
**Not:** Linux büyük küçük harfe duyarlı dosya sistemine sahiptir. Yapılandırma dosyası için arama sonuçlarındaki "Üretim" ASPNETCORE_ENVIRONMENT ayarlanması *appsettings. Production.JSON*değil *appsettings.production.json*.

Dosyayı kaydedin ve hizmeti etkinleştirin.

```bash
systemctl enable kestrel-hellomvc.service
```

Hizmeti başlatın ve çalıştığından emin olun.

```
systemctl start kestrel-hellomvc.service
systemctl status kestrel-hellomvc.service

● kestrel-hellomvc.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-hellomvc.service
            └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

Ters proxy yapılandırılmış ve systemd yönetilen Kestrel ile web uygulaması tam olarak yapılandırılmamış ve yerel makinede bir tarayıcıdan erişilebilir `http://localhost`. Ayrıca, engelliyor olabilir herhangi bir güvenlik duvarını engelleme uzak bir makineden de erişilebilir. Yanıt Üstbilgileri incelemek `Server` üstbilgisi tarafından Kestrel sunulmasını ASP.NET Core uygulama gösterir.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a>Günlükleri görüntüleme

Web uygulaması bu yana Kestrel kullanarak kullanılarak yönetilir `systemd`, tüm olaylar ve işlemleri merkezi bir günlüğe kaydedilir. Ancak, bu günlük tüm hizmetleri ve işlemleri tarafından yönetilen tüm girişleri içerir `systemd`. Görüntülemek için `kestrel-hellomvc.service`-belirli öğeleri, aşağıdaki komutu kullanın:

```bash
sudo journalctl -fu kestrel-hellomvc.service
```

Daha fazla filtrelemek için zaman seçenekleri gibi `--since today`, `--until 1 hour ago` veya bunların bir birleşimi döndürülen girişleri azaltmak.

```bash
sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-the-app"></a>Uygulama güvenliğini sağlama

### <a name="enable-apparmor"></a>AppArmor etkinleştir

Linux güvenlik modülleri (LSM) itibaren Linux 2.6 Linux çekirdek parçası olan bir çerçevedir. LSM güvenlik modüllerin farklı uygulamaları destekler. [AppArmor](https://wiki.ubuntu.com/AppArmor) kaynakları sınırlı sayıda programına sınırlandırma sağlayan bir zorunlu erişim denetimi sisteminde uygulayan bir LSM değil. AppArmor etkin olduğundan ve doğru yapılandırıldığından emin olun.

### <a name="configuring-the-firewall"></a>Güvenlik duvarını yapılandırma

Kullanılmayan tüm dış bağlantı noktalarını kapatmak kapatın. Eylemlerin Güvenlik Duvarı (ufw) sağlayan bir ön uç için `iptables` güvenlik duvarı yapılandırması için bir komut satırı arabirimi sağlayarak. Doğrulayın `ufw` gereken herhangi bir bağlantı trafiğine izin verecek şekilde yapılandırılmış.

```bash
sudo apt-get install ufw
sudo ufw enable

sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

### <a name="securing-nginx"></a>Nginx güvenliğini sağlama

Nginx varsayılan dağıtımını SSL sağlamaz. Ek güvenlik özellikleri etkinleştirmek için kaynak sunucudan oluşturun.

#### <a name="download-the-source-and-install-the-build-dependencies"></a>Kaynak yükleyip derleme bağımlılıkları

```bash
# Install the build dependencies
sudo apt-get update
sudo apt-get install build-essential zlib1g-dev libpcre3-dev libssl-dev libxslt1-dev libxml2-dev libgd2-xpm-dev libgeoip-dev libgoogle-perftools-dev libperl-dev

# Download Nginx 1.10.0 or latest
wget http://www.nginx.org/download/nginx-1.10.0.tar.gz
tar zxf nginx-1.10.0.tar.gz
```

#### <a name="change-the-nginx-response-name"></a>Nginx yanıt adını değiştirin

Edit *src/http/ngx_http_header_filter_module.c*:

```c
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-the-options-and-build"></a>Seçenekleri yapılandırmak ve derleme

Normal ifadeler için PCRE kitaplığı gereklidir. Normal ifadeler konumu yönergesinde ngx_http_rewrite_module için kullanılır. Http_ssl_module HTTPS protokolü desteği ekler.

Bir web uygulaması güvenlik duvarı gibi kullanmayı *ModSecurity* uygulama sağlamlaştırmak için.

```bash
./configure
--with-pcre=../pcre-8.38
--with-zlib=../zlib-1.2.8
--with-http_ssl_module
--with-stream
--with-mail=dynamic
```

#### <a name="configure-ssl"></a>SSL yapılandırma

* Bağlantı noktasında HTTPS trafiğini dinlemek üzere yapılandırmak `443` bir güvenilen sertifika yetkilisi (CA) tarafından verilen geçerli bir sertifika belirterek.

* Aşağıda gösterilen uygulamaları kullanan güvenliğini sağlamlaştırmak */etc/nginx/nginx.conf* dosya. Örnek daha güçlü şifreleme seçme ve HTTPS için HTTP üzerinden tüm trafiği yönlendirerek verilebilir.

* Ekleme bir `HTTP Strict-Transport-Security` (HSTS) üstbilgisi sağlar, HTTPS üzerinden yalnızca istemci tarafından yapılan tüm sonraki istekleri.

* Strict Aktarım güvenlik üstbilgisi ekleme veya uygun bir seçtiğiniz `max-age` SSL gelecekte devre dışıysa.

Ekleme */etc/nginx/proxy.conf* yapılandırma dosyası:

[!code-nginx[Main](linux-nginx/proxy.conf)]

Düzen */etc/nginx/nginx.conf* yapılandırma dosyası. Örnek içeren `http` ve `server` bölümler bir yapılandırma dosyası.

[!code-nginx[Main](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a>Clickjacking öğesini gelen güvenli Nginx
Clickjacking öğesini etkilenen kullanıcının tıklama toplamak için kötü amaçlı bir tekniktir. Clickjacking öğesini, etkilenen bir sitede tıklamak (ziyaretçi) kaybeden püf noktaları. Kullanım X-FRAME-site güvenli hale getirmek için OPTIONS.

Düzen *nginx.conf* dosyası:

```bash
sudo nano /etc/nginx/nginx.conf
```

Satırı ekleyin `add_header X-Frame-Options "SAMEORIGIN";` ve dosyayı kaydedin, sonra Nginx yeniden başlatın.

#### <a name="mime-type-sniffing"></a>MIME türü algılaması

Üstbilgi yanıt içerik türü geçersiz tarayıcıyı yönlendirir gibi bu başlığı MIME algılaması çoğu tarayıcılarından yanıt içerik türü, yöntemi bir kenara bırakarak engeller. İle `nosniff` seçeneği, sunucunun içeriktir "text/html" diyorsa, tarayıcı, "text/html" işler.

Düzen *nginx.conf* dosyası:

```bash
sudo nano /etc/nginx/nginx.conf
```

Satırı ekleyin `add_header X-Content-Type-Options "nosniff";` ve dosyayı kaydedin, sonra Nginx yeniden başlatın.
