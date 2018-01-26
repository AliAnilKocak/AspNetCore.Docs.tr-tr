---
title: ASP.NET Core Apache ile Linux ana bilgisayar
description: "Apache CentOS üzerinde ters proxy sunucu olarak Kestrel üzerinde çalışan bir ASP.NET Core web uygulaması için HTTP trafiği yönlendirmek için nasıl ayarlanacağını öğrenin."
author: spboyer
ms.author: spboyer
manager: wpickett
ms.custom: mvc
ms.date: 10/19/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: host-and-deploy/linux-apache
ms.openlocfilehash: caa8cce53b3982815f1eab75792a10ec390f3257
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/24/2018
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="d9cff-103">ASP.NET Core Apache ile Linux ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="d9cff-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="d9cff-104">Tarafından [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="d9cff-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="d9cff-105">Bu kılavuz kullanılarak nasıl ayarlanacağını öğrenin [Apache](https://httpd.apache.org/) ters proxy sunucusu olarak [CentOS 7](https://www.centos.org/) üzerinde çalışan bir ASP.NET Core web uygulaması için HTTP trafiği yönlendirmek için [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="d9cff-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="d9cff-106">[Mod_proxy uzantısı](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) ve ilgili modüller sunucunun ters proxy oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d9cff-106">The [mod_proxy extension](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d9cff-107">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="d9cff-107">Prerequisites</span></span>

1. <span data-ttu-id="d9cff-108">Sudo ayrıcalığa sahip standart kullanıcı hesabı ile CentOS 7'yi çalıştıran sunucu</span><span class="sxs-lookup"><span data-stu-id="d9cff-108">Server running CentOS 7 with a standard user account with sudo privilege</span></span>
2. <span data-ttu-id="d9cff-109">ASP.NET Core uygulama</span><span class="sxs-lookup"><span data-stu-id="d9cff-109">ASP.NET Core app</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="d9cff-110">Uygulama yayımlama</span><span class="sxs-lookup"><span data-stu-id="d9cff-110">Publish the app</span></span>

<span data-ttu-id="d9cff-111">Bir uygulama olarak yayımlama bir [müstakil dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) CentOS 7 çalışma zamanı için sürüm yapılandırmasında (`centos.7-x64`).</span><span class="sxs-lookup"><span data-stu-id="d9cff-111">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) in Release configuration for the CentOS 7 runtime (`centos.7-x64`).</span></span> <span data-ttu-id="d9cff-112">İçeriğini kopyalayın *bin/Release/netcoreapp2.0/centos.7-x64/publish* SCP, FTP veya başka bir dosya aktarım yöntemi kullanarak sunucu klasörüne.</span><span class="sxs-lookup"><span data-stu-id="d9cff-112">Copy the contents of the *bin/Release/netcoreapp2.0/centos.7-x64/publish* folder to the server using SCP, FTP, or other file transfer method.</span></span>

> [!NOTE]
> <span data-ttu-id="d9cff-113">Bir üretim dağıtım senaryosunda sürekli tümleştirme iş akışı uygulama yayımlama ve varlıkları sunucuya kopyalama işlemlerini yapar.</span><span class="sxs-lookup"><span data-stu-id="d9cff-113">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span> 

## <a name="configure-a-proxy-server"></a><span data-ttu-id="d9cff-114">Bir proxy sunucusunu yapılandırın</span><span class="sxs-lookup"><span data-stu-id="d9cff-114">Configure a proxy server</span></span>

<span data-ttu-id="d9cff-115">Ters proxy hizmet veren dinamik web uygulamaları için ortak bir kurulur.</span><span class="sxs-lookup"><span data-stu-id="d9cff-115">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="d9cff-116">Ters proxy HTTP isteği sonlandırır ve ASP.NET uygulamasına iletir.</span><span class="sxs-lookup"><span data-stu-id="d9cff-116">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="d9cff-117">Bir proxy sunucusu, istemci isteklerini yerine bunları kendisi yerine getiren başka bir sunucuya iletir biridir.</span><span class="sxs-lookup"><span data-stu-id="d9cff-117">A proxy server is one which forwards client requests to another server instead of fulfilling them itself.</span></span> <span data-ttu-id="d9cff-118">Ters proxy genellikle rastgele istemcileri adına sabit bir hedef iletir.</span><span class="sxs-lookup"><span data-stu-id="d9cff-118">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="d9cff-119">Bu kılavuzda, Apache Kestrel ASP.NET Core uygulama hizmet ettiğini aynı sunucu üzerinde çalışan ters proxy yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="d9cff-119">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

### <a name="install-apache"></a><span data-ttu-id="d9cff-120">Apache yükleyin</span><span class="sxs-lookup"><span data-stu-id="d9cff-120">Install Apache</span></span>

<span data-ttu-id="d9cff-121">CentOS paketleri en son kararlı sürümlerine güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="d9cff-121">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="d9cff-122">Apache web sunucusu üzerinde CentOS tek bir yükleme `yum` komutu:</span><span class="sxs-lookup"><span data-stu-id="d9cff-122">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="d9cff-123">Komutu çalıştırdıktan sonra çıktı örneği:</span><span class="sxs-lookup"><span data-stu-id="d9cff-123">Sample output after running the command:</span></span>

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
> <span data-ttu-id="d9cff-124">Bu örnekte, CentOS 7 sürümü 64-bit olduğundan çıktı httpd.86_64 yansıtır.</span><span class="sxs-lookup"><span data-stu-id="d9cff-124">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="d9cff-125">Apache yüklendiği doğrulamak için çalıştırın `whereis httpd` bir komut isteminden.</span><span class="sxs-lookup"><span data-stu-id="d9cff-125">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span> 

### <a name="configure-apache-for-reverse-proxy"></a><span data-ttu-id="d9cff-126">Apache için ters proxy ayarlarını yapılandır</span><span class="sxs-lookup"><span data-stu-id="d9cff-126">Configure Apache for reverse proxy</span></span>

<span data-ttu-id="d9cff-127">İçinde Apache için yapılandırma dosyalarının bulunduğu `/etc/httpd/conf.d/` dizin.</span><span class="sxs-lookup"><span data-stu-id="d9cff-127">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="d9cff-128">Herhangi dosya ile *.conf* uzantısı modül yapılandırma dosyalarında yanı sıra alfabetik sırada işlenir `/etc/httpd/conf.modules.d/`, herhangi bir yapılandırma içeren modüllerini yüklemek gerekli dosyaları.</span><span class="sxs-lookup"><span data-stu-id="d9cff-128">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="d9cff-129">Adlı uygulama için bir yapılandırma dosyası oluşturma `hellomvc.conf`:</span><span class="sxs-lookup"><span data-stu-id="d9cff-129">Create a configuration file for the app named `hellomvc.conf`:</span></span>

```
<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
</VirtualHost>
```

<span data-ttu-id="d9cff-130">**VirtualHost** düğümü, bir sunucu üzerindeki bir veya daha fazla dosyalarda birden çok kez görüntülenebilir.</span><span class="sxs-lookup"><span data-stu-id="d9cff-130">The **VirtualHost** node can appear multiple times in one or more files on a server.</span></span> <span data-ttu-id="d9cff-131">**VirtualHost** 80 numaralı bağlantı noktasını kullanarak tüm IP adresini dinlemek üzere ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d9cff-131">**VirtualHost** is set to listen on any IP address using port 80.</span></span> <span data-ttu-id="d9cff-132">Sonraki iki satır varsayılan olarak, bağlantı noktası 5000 127.0.0.1 sunucuda kök proxy istekleri için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="d9cff-132">The next two lines are set to proxy requests at the root to the server at 127.0.0.1 on port 5000.</span></span> <span data-ttu-id="d9cff-133">Çift yönlü iletişimi için *ProxyPass* ve *ProxyPassReverse* gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d9cff-133">For bi-directional communication, *ProxyPass* and *ProxyPassReverse* are required.</span></span>

<span data-ttu-id="d9cff-134">Günlüğe kaydetme, başına yapılandırılabilir **VirtualHost** kullanarak **hata günlüğüne** ve **CustomLog** yönergeleri.</span><span class="sxs-lookup"><span data-stu-id="d9cff-134">Logging can be configured per **VirtualHost** using **ErrorLog** and **CustomLog** directives.</span></span> <span data-ttu-id="d9cff-135">**Hata günlüğü** burada sunucusu günlüklerini hataları, konumu ve **CustomLog** filename ve günlük dosyası biçimini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d9cff-135">**ErrorLog** is the location where the server logs errors, and **CustomLog** sets the filename and format of log file.</span></span> <span data-ttu-id="d9cff-136">Bu durumda, istek bilgilerini günlüğe nerede budur.</span><span class="sxs-lookup"><span data-stu-id="d9cff-136">In this case, this is where request information is logged.</span></span> <span data-ttu-id="d9cff-137">Her istek için bir satır vardır.</span><span class="sxs-lookup"><span data-stu-id="d9cff-137">There's one line for each request.</span></span>

<span data-ttu-id="d9cff-138">Dosyayı kaydedin ve yapılandırmayı test etme.</span><span class="sxs-lookup"><span data-stu-id="d9cff-138">Save the file and test the configuration.</span></span> <span data-ttu-id="d9cff-139">Her şeyi geçerse, yanıt olmalıdır `Syntax [OK]`.</span><span class="sxs-lookup"><span data-stu-id="d9cff-139">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="d9cff-140">Apache yeniden başlatın:</span><span class="sxs-lookup"><span data-stu-id="d9cff-140">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitoring-the-app"></a><span data-ttu-id="d9cff-141">Uygulama izleme</span><span class="sxs-lookup"><span data-stu-id="d9cff-141">Monitoring the app</span></span>

<span data-ttu-id="d9cff-142">Apache olan şimdi yapılan isteklerini iletmek için kurulumu `http://localhost:80` Kestrel çalışan ASP.NET Core App `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="d9cff-142">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span>  <span data-ttu-id="d9cff-143">Ancak, Apache Kestrel işlemini yönetmek için ayarlanmamış.</span><span class="sxs-lookup"><span data-stu-id="d9cff-143">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="d9cff-144">Kullanım *systemd* ve Başlat ve temel web uygulaması izlemek için bir hizmet dosyası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d9cff-144">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="d9cff-145">*systemd* , başlatma, durdurma ve işlemlerini yönetme için çok güçlü özellikler sağlayan bir init sistemidir.</span><span class="sxs-lookup"><span data-stu-id="d9cff-145">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 


### <a name="create-the-service-file"></a><span data-ttu-id="d9cff-146">Hizmet dosyası oluşturma</span><span class="sxs-lookup"><span data-stu-id="d9cff-146">Create the service file</span></span>

<span data-ttu-id="d9cff-147">Hizmet tanımı dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="d9cff-147">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

<span data-ttu-id="d9cff-148">Uygulama için bir örnek hizmet dosyası:</span><span class="sxs-lookup"><span data-stu-id="d9cff-148">An example service file for the app:</span></span>

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/aspnetcore/hellomvc
ExecStart=/usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
Restart=always
# Restart service after 10 seconds if dotnet service crashes
RestartSec=10
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

> [!NOTE]
> <span data-ttu-id="d9cff-149">**Kullanıcı** &mdash; , kullanıcı *apache* kullanılmaz yapılandırmanın, kullanıcı ilk oluşturulmalı ve doğru sahipliği dosyaları için verilen.</span><span class="sxs-lookup"><span data-stu-id="d9cff-149">**User** &mdash; If the user *apache* isn't used by the configuration, the user must be created first and given proper ownership for files.</span></span>

<span data-ttu-id="d9cff-150">Dosyayı kaydedin ve hizmeti etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="d9cff-150">Save the file and enable the service:</span></span>

```bash
systemctl enable kestrel-hellomvc.service
```

<span data-ttu-id="d9cff-151">Hizmeti başlatın ve çalıştığından emin olun:</span><span class="sxs-lookup"><span data-stu-id="d9cff-151">Start the service and verify that it's running:</span></span>

```bash
systemctl start kestrel-hellomvc.service
systemctl status kestrel-hellomvc.service

● kestrel-hellomvc.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-hellomvc.service
            └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

<span data-ttu-id="d9cff-152">Ters proxy yapılandırılmış ve üzerinden yönetilen Kestrel *systemd*, web uygulaması tam olarak yapılandırılmamış ve yerel makinede bir tarayıcıdan erişilebilir `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="d9cff-152">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="d9cff-153">Yanıt Üstbilgileri incelemek **Server** üstbilgi gösterir ASP.NET Core uygulama tarafından Kestrel sunulur:</span><span class="sxs-lookup"><span data-stu-id="d9cff-153">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a><span data-ttu-id="d9cff-154">Günlükleri görüntüleme</span><span class="sxs-lookup"><span data-stu-id="d9cff-154">Viewing logs</span></span>

<span data-ttu-id="d9cff-155">Web uygulaması bu yana Kestrel kullanarak kullanılarak yönetilir *systemd*, olaylar ve işlemleri merkezi bir günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d9cff-155">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="d9cff-156">Ancak, bu günlük tüm işlemler tarafından yönetilen ve Hizmetleri için girişleri içerir *systemd*.</span><span class="sxs-lookup"><span data-stu-id="d9cff-156">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="d9cff-157">Görüntülemek için `kestrel-hellomvc.service`-belirli öğeleri, aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="d9cff-157">To view the `kestrel-hellomvc.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-hellomvc.service
```

<span data-ttu-id="d9cff-158">Zaman filtresi uygulamak için komutuyla zaman seçeneklerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="d9cff-158">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="d9cff-159">Örneğin, `--since today` geçerli gün için filtre uygulamak için veya `--until 1 hour ago` önceki saatlik girişlerini görmek için.</span><span class="sxs-lookup"><span data-stu-id="d9cff-159">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="d9cff-160">Daha fazla bilgi için bkz: [journalctl adam sayfa](https://www.unix.com/man-page/centos/1/journalctl/).</span><span class="sxs-lookup"><span data-stu-id="d9cff-160">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-the-app"></a><span data-ttu-id="d9cff-161">Uygulama güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="d9cff-161">Securing the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="d9cff-162">Güvenlik duvarını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d9cff-162">Configure firewall</span></span>

<span data-ttu-id="d9cff-163">*Firewalld* ağ bölgeleri için destek ile Güvenlik Duvarı'nı yönetmek için bir dinamik arka plan programı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d9cff-163">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="d9cff-164">Bağlantı noktaları ve paket filtreleme hala iptables tarafından yönetilebilir.</span><span class="sxs-lookup"><span data-stu-id="d9cff-164">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="d9cff-165">*Firewalld* varsayılan olarak yüklü olması.</span><span class="sxs-lookup"><span data-stu-id="d9cff-165">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="d9cff-166">`yum`paketi yüklemek ya da yüklü doğrulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d9cff-166">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="d9cff-167">Kullanım `firewalld` yalnızca uygulama için gerekli bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="d9cff-167">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="d9cff-168">Bu durumda, bağlantı noktası 80 ve 443 kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d9cff-168">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="d9cff-169">Aşağıdaki komutlar, 80 ve 443'ü açmak için bağlantı noktalarını kalıcı olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="d9cff-169">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="d9cff-170">Güvenlik Duvarı ayarlarını yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="d9cff-170">Reload the firewall settings.</span></span> <span data-ttu-id="d9cff-171">Kullanılabilir hizmetleri ve bağlantı noktalarını varsayılan bölgede denetleyin.</span><span class="sxs-lookup"><span data-stu-id="d9cff-171">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="d9cff-172">Seçenekleri kullanılabilir inceleyerek `firewall-cmd -h`.</span><span class="sxs-lookup"><span data-stu-id="d9cff-172">Options are available by inspecting `firewall-cmd -h`.</span></span>

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

### <a name="ssl-configuration"></a><span data-ttu-id="d9cff-173">SSL yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d9cff-173">SSL configuration</span></span>

<span data-ttu-id="d9cff-174">SSL, Apache yapılandırmak için *mod_ssl* modülü kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d9cff-174">To configure Apache for SSL, the *mod_ssl* module is used.</span></span> <span data-ttu-id="d9cff-175">Zaman *httpd* modülü yüklendi, *mod_ssl* modülü de yüklendi.</span><span class="sxs-lookup"><span data-stu-id="d9cff-175">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="d9cff-176">Yüklü değildi kullanırsanız `yum` yapılandırmasına eklemek için.</span><span class="sxs-lookup"><span data-stu-id="d9cff-176">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```
<span data-ttu-id="d9cff-177">SSL zorlamak için yükleme `mod_rewrite` modülü URL yeniden yazma işlemi etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="d9cff-177">To enforce SSL, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="d9cff-178">Değiştirme *hellomvc.conf* dosya URL yeniden yazma işlemi etkinleştirmek ve bağlantı noktası 443 üzerinden iletişimi güvenli hale getirmek için:</span><span class="sxs-lookup"><span data-stu-id="d9cff-178">Modify the *hellomvc.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

```
<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/ [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> <span data-ttu-id="d9cff-179">Bu örnek, yerel olarak oluşturulan bir sertifika kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="d9cff-179">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="d9cff-180">**SSLCertificateFile** birincil sertifika dosyası için etki alanı adı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d9cff-180">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="d9cff-181">**SSLCertificateKeyFile** CSR oluşturulduğunda anahtar dosyası oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d9cff-181">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="d9cff-182">**SSLCertificateChainFile** ara sertifika dosyası (varsa) olmalıdır sertifika yetkilisi tarafından sağlandı.</span><span class="sxs-lookup"><span data-stu-id="d9cff-182">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="d9cff-183">Dosyayı kaydedin ve test yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="d9cff-183">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="d9cff-184">Apache yeniden başlatın:</span><span class="sxs-lookup"><span data-stu-id="d9cff-184">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="d9cff-185">Ek Apache öneriler</span><span class="sxs-lookup"><span data-stu-id="d9cff-185">Additional Apache suggestions</span></span>

### <a name="additional-headers"></a><span data-ttu-id="d9cff-186">Ek üstbilgileri</span><span class="sxs-lookup"><span data-stu-id="d9cff-186">Additional headers</span></span>

<span data-ttu-id="d9cff-187">Kötü amaçlı saldırılara karşı güvenli hale getirmek için ya da değiştirildiğinde veya eklendiğinde birkaç üstbilgileri vardır.</span><span class="sxs-lookup"><span data-stu-id="d9cff-187">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="d9cff-188">Emin `mod_headers` modülü yüklenir:</span><span class="sxs-lookup"><span data-stu-id="d9cff-188">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="d9cff-189">Clickjacking öğesini saldırılarına karşı güvenli Apache</span><span class="sxs-lookup"><span data-stu-id="d9cff-189">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="d9cff-190">[Clickjacking öğesini](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), olarak da bilinen bir *UI redress saldırı*, bir Web sitesine ziyaretçiyi yere sağladı şu anda ziyaret ettiğiniz daha bir bağlantı veya başka bir sayfaya düğmesine tıklamak amaçlı bir saldırı aracıdır.</span><span class="sxs-lookup"><span data-stu-id="d9cff-190">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="d9cff-191">Kullanım `X-FRAME-OPTIONS` sitesi güvenliğini sağlamak için.</span><span class="sxs-lookup"><span data-stu-id="d9cff-191">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="d9cff-192">Düzen *httpd.conf* dosyası:</span><span class="sxs-lookup"><span data-stu-id="d9cff-192">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="d9cff-193">Satırı ekleyin `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span><span class="sxs-lookup"><span data-stu-id="d9cff-193">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span> <span data-ttu-id="d9cff-194">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d9cff-194">Save the file.</span></span> <span data-ttu-id="d9cff-195">Apache yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="d9cff-195">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="d9cff-196">MIME türü algılaması</span><span class="sxs-lookup"><span data-stu-id="d9cff-196">MIME-type sniffing</span></span>

<span data-ttu-id="d9cff-197">`X-Content-Type-Options` Üstbilgi engeller Internet Explorer'dan *MIME algılaması* (bir dosyanın belirleneceği `Content-Type` dosyanın içerikten).</span><span class="sxs-lookup"><span data-stu-id="d9cff-197">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determing a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="d9cff-198">Sunucu ayarlarsa `Content-Type` başlığına `text/html` ile `nosniff` seçenek kümesi, Internet Explorer işler içeriği olarak `text/html` dosyanın içeriği ne olursa olsun.</span><span class="sxs-lookup"><span data-stu-id="d9cff-198">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="d9cff-199">Düzen *httpd.conf* dosyası:</span><span class="sxs-lookup"><span data-stu-id="d9cff-199">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="d9cff-200">Satırı ekleyin `Header set X-Content-Type-Options "nosniff"`.</span><span class="sxs-lookup"><span data-stu-id="d9cff-200">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="d9cff-201">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d9cff-201">Save the file.</span></span> <span data-ttu-id="d9cff-202">Apache yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="d9cff-202">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="d9cff-203">YükDengeleme</span><span class="sxs-lookup"><span data-stu-id="d9cff-203">Load Balancing</span></span> 

<span data-ttu-id="d9cff-204">Bu örnekte, Kurulum ve Apache CentOS 7 ve Kestrel aynı örneği makinede yapılandırmak gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d9cff-204">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="d9cff-205">Tek bir hata noktası olmaması için; kullanarak *mod_proxy_balancer* ve değiştirme **VirtualHost** web uygulamaları Apache proxy sunucunun arkasında birden çok örneğini yönetmek için izin verir.</span><span class="sxs-lookup"><span data-stu-id="d9cff-205">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing mutliple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="d9cff-206">Yapılandırma dosyasında ek bir örneği aşağıda gösterilen `hellomvc` 5001 bağlantı noktası üzerinde çalıştırmak için Kurulum uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="d9cff-206">In the configuration file shown below, an additional instance of the `hellomvc` app is setup to run on port 5001.</span></span> <span data-ttu-id="d9cff-207">*Proxy* bölüm ayarlanmış iki üyeleriyle dengeleyici yapılandırmasına sahip Yük Dengelemesi *byrequests*.</span><span class="sxs-lookup"><span data-stu-id="d9cff-207">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

```
<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/ [R,L]
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
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="d9cff-208">Oran sınırları</span><span class="sxs-lookup"><span data-stu-id="d9cff-208">Rate Limits</span></span>
<span data-ttu-id="d9cff-209">Kullanarak *mod_ratelimit*, içinde bulunan *httpd* modülü, bant genişliği istemcilerin olabilir sınırlı:</span><span class="sxs-lookup"><span data-stu-id="d9cff-209">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```
<span data-ttu-id="d9cff-210">Örnek dosyası kök konumu altında 600 KB/sn olarak bant genişliği sınırları:</span><span class="sxs-lookup"><span data-stu-id="d9cff-210">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```
