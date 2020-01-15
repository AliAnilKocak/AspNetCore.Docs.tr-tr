---
title: ASP.NET Core ile IIS modülleri
author: guardrex
description: ASP.NET Core uygulamaları ve IIS modüllerini yönetmek nasıl etkin ve etkin olmayan IIS modülleri keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/iis/modules
ms.openlocfilehash: ca6cf349aa05db97e145f1cd0cae97a107761fd8
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75951805"
---
# <a name="iis-modules-with-aspnet-core"></a>ASP.NET Core ile IIS modülleri

Tarafından [Luke Latham](https://github.com/guardrex)

Yerel IIS modüllerinden bazıları ve tüm IIS yönetilen modülleri, ASP.NET Core Apps isteklerini işleyemez. Birçok durumda ASP.NET Core, IIS yerel ve yönetilen modüller tarafından ele alınan senaryolara bir alternatif sağlar.

## <a name="native-modules"></a>Yerel modüller

Tablo, ASP.NET Core uygulamalar ve ASP.NET Core modülü ile işlevsel yerel IIS modüllerini gösterir.

| Modülü | ASP.NET Core uygulamalarla işlevsel | ASP.NET Core seçeneği |
| --- | :---: | --- |
| **Anonim Kimlik Doğrulaması**<br>`AnonymousAuthenticationModule`                                  | Evet | |
| **Temel Kimlik Doğrulaması**<br>`BasicAuthenticationModule`                                          | Evet | |
| **İstemci sertifikası eşleme kimlik doğrulaması**<br>`CertificateMappingAuthenticationModule`      | Evet | |
| **CGI**<br>`CgiModule`                                                                           | Hayır  | |
| **Yapılandırma doğrulaması**<br>`ConfigurationValidationModule`                                  | Evet | |
| **HTTP Hataları**<br>`CustomErrorModule`                                                           | Hayır  | [Durum kodu sayfaları ara yazılımı](xref:fundamentals/error-handling#usestatuscodepages) |
| **Özel günlüğe kaydetme**<br>`CustomLoggingModule`                                                      | Evet | |
| **Varsayılan Belge**<br>`DefaultDocumentModule`                                                  | Hayır  | [Varsayılan dosyalar ara yazılımı](xref:fundamentals/static-files#serve-a-default-document) |
| **Özet kimlik doğrulaması**<br>`DigestAuthenticationModule`                                        | Evet | |
| **Dizin Tarama**<br>`DirectoryListingModule`                                               | Hayır  | [Dizin tarama ara yazılımı](xref:fundamentals/static-files#enable-directory-browsing) |
| **Dinamik sıkıştırma**<br>`DynamicCompressionModule`                                            | Evet | [Yanıt Sıkıştırma Ara Yazılımı](xref:performance/response-compression) |
| **Başarısız Istek Izleme**<br>`FailedRequestsTracingModule`                                     | Evet | [Günlüğe kaydetme ASP.NET Core](xref:fundamentals/logging/index#tracesource-provider) |
| **Dosya önbelleğe alma**<br>`FileCacheModule`                                                            | Hayır  | [Yanıtları Önbelleğe Alma Ara Yazılımı](xref:performance/caching/middleware) |
| **HTTP önbelleği**<br>`HttpCacheModule`                                                            | Hayır  | [Yanıtları Önbelleğe Alma Ara Yazılımı](xref:performance/caching/middleware) |
| **HTTP Günlüğü**<br>`HttpLoggingModule`                                                          | Evet | [Günlüğe kaydetme ASP.NET Core](xref:fundamentals/logging/index) |
| **HTTP Yeniden Yönlendirmesi**<br>`HttpRedirectionModule`                                                  | Evet | [URL Yeniden Yazma Ara Yazılımı](xref:fundamentals/url-rewriting) |
| **HTTP Izleme**<br>`TracingModule`                                                              | Evet | |
| **IIS Istemci sertifikası eşleme kimlik doğrulaması**<br>`IISCertificateMappingAuthenticationModule` | Evet | |
| **IP ve Etki Alanı Kısıtlamaları**<br>`IpRestrictionModule`                                          | Evet | |
| **ISAPI Filtreleri**<br>`IsapiFilterModule`                                                         | Evet | [Ara Yazılım](xref:fundamentals/middleware/index) |
| **I**<br>`IsapiModule`                                                                       | Evet | [Ara Yazılım](xref:fundamentals/middleware/index) |
| **Protokol desteği**<br>`ProtocolSupportModule`                                                  | Evet | |
| **İstek Filtreleme**<br>`RequestFilteringModule`                                                | Evet | [URL yeniden yazma ara yazılımı `IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **İstek İzleyicisi**<br>`RequestMonitorModule`                                                    | Evet | |
| **URL yeniden yazma**&#8224;<br>`RewriteModule`                                                      | Evet | [URL Yeniden Yazma Ara Yazılımı](xref:fundamentals/url-rewriting) |
| **Sunucu tarafı eklemeleri**<br>`ServerSideIncludeModule`                                            | Hayır  | |
| **Statik sıkıştırma**<br>`StaticCompressionModule`                                              | Hayır  | [Yanıt Sıkıştırma Ara Yazılımı](xref:performance/response-compression) |
| **Statik İçerik**<br>`StaticFileModule`                                                         | Hayır  | [Statik dosya ara yazılımı](xref:fundamentals/static-files) |
| **Belirteç önbelleğe alma**<br>`TokenCacheModule`                                                          | Evet | |
| **URI önbelleğe alma**<br>`UriCacheModule`                                                              | Evet | |
| **URL Yetkilendirmesi**<br>`UrlAuthorizationModule`                                                | Evet | [ASP.NET Core kimliği](xref:security/authentication/identity) |
| **Windows Kimlik Doğrulaması**<br>`WindowsAuthenticationModule`                                      | Evet | |

&#8224;[Dizin yapısındaki](xref:host-and-deploy/directory-structure)değişiklikler nedenıyle, URL yeniden yazma modülünün `isFile` ve `isDirectory` eşleşme türleri ASP.NET Core uygulamalarla çalışmıyor.

## <a name="managed-modules"></a>Yönetilen modüller

Yönetilen modüller, uygulama havuzunun .NET CLR sürümü **yönetilen kod olmadan**ayarlandığında barındırılan ASP.NET Core uygulamalarıyla işlevsel *değildir* . ASP.NET Core çeşitli durumlarda ara yazılım alternatifleri sunmaktadır.

| Modülü                  | ASP.NET Core seçeneği |
| ----------------------- | ------------------- |
| AnonymousIdentification | |
| DefaultAuthentication   | |
| Dosya yetkilendirmesi       | |
| FormsAuthentication     | [Tanımlama bilgisi kimlik doğrulama ara yazılımı](xref:security/authentication/cookie) |
| OutputCache             | [Yanıtları Önbelleğe Alma Ara Yazılımı](xref:performance/caching/middleware) |
| Profil                 | |
| 'Da             | |
| ScriptModule-4,0        | |
| Oturum                 | [Oturum ara yazılımı](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModule       | [URL Yeniden Yazma Ara Yazılımı](xref:fundamentals/url-rewriting) |
| UrlRoutingModule-4,0    | [ASP.NET Core kimliği](xref:security/authentication/identity) |
| WindowsAuthentication   | |

## <a name="iis-manager-application-changes"></a>IIS Yöneticisi uygulama değişiklikleri

Ayarları yapılandırmak için IIS Yöneticisi kullanılırken, uygulamanın *Web. config* dosyası değiştirilir. Bir uygulamayı dağıtırken ve *Web. config*dahil olmak üzere, IIS Yöneticisi ile yapılan tüm değişikliklerin üzerine dağıtılan *Web. config* dosyası tarafından üzerine yazılır. Sunucunun *Web. config* dosyasında değişiklik yapılırsa, sunucudaki güncelleştirilmiş *Web. config* dosyasını hemen yerel projeye kopyalayın.

## <a name="disabling-iis-modules"></a>IIS modüllerini devre dışı bırakma

Bir uygulama için devre dışı bırakılması gereken sunucu düzeyinde bir IIS modülü yapılandırılırsa, uygulamanın *Web. config* dosyasına ek olarak modülü devre dışı bırakabilirsiniz. Modülü yerinde bırakın ve bir yapılandırma ayarı (varsa) kullanarak devre dışı bırakın ya da modülü uygulamadan kaldırın.

### <a name="module-deactivation"></a>Modül devre dışı bırakma

Birçok modül, uygulamanın modül uygulamadan kaldırılmadan devre dışı olmasına izin veren bir yapılandırma ayarı sunar. Bu, bir modülün devre dışı bırakılması için en basit ve en hızlı yoldur. Örneğin, HTTP yeniden yönlendirme modülü *Web. config*'deki `<httpRedirect>` öğesiyle devre dışı bırakılabilir:

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Yapılandırma ayarları ile modülleri devre dışı bırakma hakkında daha fazla bilgi için [ııs \<System. webServer >](/iis/configuration/system.webServer/)'ın *alt öğeler* bölümündeki bağlantıları izleyin.

### <a name="module-removal"></a>Modül kaldırma

Bir modülü *Web. config*'deki bir ayarla kaldırmak istiyorsanız modülün kilidini açın ve önce *Web. config* dosyasının `<modules>` bölümünün kilidini açın:

1. Modülün kilidini sunucu düzeyinde açın. IIS Yöneticisi **bağlantıları** kenar çubuğundan IIS sunucusunu seçin. **IIS** alanında **modüller** ' i açın. Listeden modülü seçin. Sağdaki **Eylemler** kenar çubuğunda, **Aç**' ı seçin. Modülün eylem girdisi **kilit**olarak görünürse, modülün kilidi zaten açık olur ve herhangi bir eylem gerekmez. *Web. config* 'den daha sonra kaldırmayı planladığınız sayıda modülün kilidini açın.

2. Uygulamayı, *Web. config*içinde `<modules>` bölümü olmadan dağıtın. Bir uygulama, IIS Yöneticisi 'nde ilk olarak bölümün kilidini açmadan `<modules>` bölümünü içeren bir *Web. config* ile dağıtılırsa, Configuration Manager bölümün kilidini açmaya çalışırken bir özel durum oluşturur. Bu nedenle uygulamayı `<modules>` bir bölüm olmadan dağıtın.

3. *Web. config*dosyasının `<modules>` bölümünün kilidini açın. **Bağlantılar** kenar çubuğunda **sitelerde**Web sitesini seçin. **Yönetim** alanında, **yapılandırma düzenleyicisini**açın. `system.webServer/modules` bölümünü seçmek için gezinti denetimlerini kullanın. Sağdaki **Eylemler** kenar çubuğunda, bölümün **kilidini açmak** için öğesini seçin. Modül bölümü için eylem girişi **kilit bölümü**olarak görünürse modül bölümünün kilidi zaten açık olur ve herhangi bir eylem gerekmez.

4. Modülün uygulamayı kaldırmak için bir `<remove>` öğesi ile uygulamanın yerel *Web. config* dosyasına bir `<modules>` bölümü ekleyin. Birden çok modülü kaldırmak için birden çok `<remove>` öğesi ekleyin. Sunucuda *Web. config* değişiklikleri yapılırsa, projenin *Web. config* dosyasında bu değişiklikleri hemen yerel olarak yapın. Bu yaklaşımı kullanarak bir modülün kaldırılması, modülün sunucu üzerindeki diğer uygulamalarla kullanımını etkilemez.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

*Web. config*kullanarak IIS Express modül eklemek veya kaldırmak Için, *ApplicationHost. config dosyasını* `<modules>` bölümünün kilidini açmak üzere değiştirin:

1. *{APPLICATION root}\\. vs\config\applicationhost,config*dosyasını açın.

1. IIS modülleri için `<section>` öğesini bulun ve `Deny` `overrideModeDefault` `Allow`olarak değiştirin:

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. `<location path="" overrideMode="Allow"><system.webServer><modules>` bölümünü bulun. Kaldırmak istediğiniz modüller için `true` `lockItem` `false`olarak ayarlayın. Aşağıdaki örnekte, CGI modülünün kilidi açıldı:

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. `<modules>` bölümü ve bağımsız modüllerin kilidi açıldıktan sonra, uygulamayı IIS Express çalıştırmak için uygulamanın *Web. config* dosyasını kullanarak IIS modülleri ekleme veya kaldırma ücretsizdir.

Ayrıca, *Appcmd. exe*Ile bir IIS modülü de kaldırılabilir. Komutta `MODULE_NAME` ve `APPLICATION_NAME` sağlayın:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Örneğin, `DynamicCompressionModule` varsayılan Web sitesinden kaldırın:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>En düşük modül yapılandırması

ASP.NET Core bir uygulamayı çalıştırmak için gereken tek modüller, anonim kimlik doğrulama modülüdür ve ASP.NET Core modülüdür.

URI önbelleğe alma modülü (`UriCacheModule`), IIS 'nin URL düzeyinde Web sitesi yapılandırmasını önbelleğe almasına izin verir. Bu modül olmadan, aynı URL sürekli olarak istendiği zaman bile IIS her istekte yapılandırmayı okumalı ve ayrıştıramalıdır. Yapılandırma ayrıştırılırken her istek önemli bir performans cezasına neden olur. *Barındırılan bir ASP.NET Core uygulamasının çalışması için URI önbelleğe alma modülü kesinlikle gerekli olmasa da, tüm ASP.NET Core dağıtımları için URI önbelleğe alma modülünün etkinleştirilmesini öneririz.*

HTTP önbelleğe alma modülü (`HttpCacheModule`), IIS çıkış önbelleğini ve ayrıca, HTTP. sys önbelleğindeki öğeleri önbelleğe alma mantığını uygular. Bu modül olmadan içerik artık çekirdek modunda önbelleğe alınmaz ve önbellek profilleri yok sayılır. HTTP önbelleğe alma modülünün kaldırılması genellikle performans ve kaynak kullanımındaki olumsuz etkileri vardır. *Barındırılan bir ASP.NET Core uygulamasının çalışması için HTTP önbelleğe alma modülü kesinlikle gerekli olmasa da, tüm ASP.NET Core dağıtımları için HTTP önbelleğe alma modülünün etkinleştirilmesini öneririz.*

## <a name="additional-resources"></a>Ek kaynaklar

* [IIS mimarilerine giriş: IIS 'deki modüller](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [IIS modüllerine genel bakış](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [IIS 7,0 rollerini ve modüllerini özelleştirme](https://technet.microsoft.com/library/cc627313.aspx)
* [System. webServer > IIS \<](/iis/configuration/system.webServer/)
