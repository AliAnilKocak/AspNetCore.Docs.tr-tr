---
title: ASP.NET Core Facebook, Google ve dış sağlayıcı kimlik doğrulaması
author: rick-anderson
description: Bu öğreticide, dış kimlik doğrulama sağlayıcılarıyla OAuth 2,0 kullanarak bir ASP.NET Core 2. x uygulamasının nasıl oluşturulacağı gösterilmektedir.
ms.author: riande
ms.custom: mvc
ms.date: 05/10/2019
uid: security/authentication/social/index
ms.openlocfilehash: edaf9eeaf02879b2f7816bab0eb373a7de640c05
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082512"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>ASP.NET Core Facebook, Google ve dış sağlayıcı kimlik doğrulaması

Tarafından [Valeriy Novytskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu öğreticide, kullanıcıların dış kimlik doğrulama sağlayıcılarından kimlik bilgileriyle OAuth 2,0 kullanarak oturum açmasını sağlayan ASP.NET Core 2,2 uygulamasının nasıl oluşturulacağı gösterilmektedir.

[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins)ve [Microsoft](xref:security/authentication/microsoft-logins) sağlayıcıları aşağıdaki bölümlerde ele alınmıştır. Diğer sağlayıcılar, [Aspnet. Security. OAuth. Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) ve [Aspnet. Security. OpenID. Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers)gibi üçüncü taraf paketlerinde kullanılabilir.

![Facebook, Twitter, Google Plus ve Windows için sosyal medya simgeleri](index/_static/social.png)

Kullanıcıların mevcut kimlik bilgileriyle oturum açmasını sağlama:
* Kullanıcılar için kullanışlıdır.
* Oturum açma işlemini üçüncü bir tarafa yönetmenin karmaşıklıklarını birçok kaydırır. 

Sosyal oturumların trafik ve müşteri dönüştürmelerini nasıl ve ne şekilde, [Facebook](https://www.facebook.com/unsupportedbrowser) ve [Twitter](https://dev.twitter.com/resources/case-studies)tarafından örnek olay incelemeleri hakkında örnekler için bkz.

## <a name="create-a-new-aspnet-core-project"></a>Yeni bir ASP.NET Core projesi oluştur

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Yeni bir proje oluşturun.
* **ASP.NET Core Web uygulaması** ' nı ve **İleri ' yi**seçin.
* Bir **Proje adı** girin ve **konumu**onaylayın veya değiştirin. **Oluştur**’u seçin.
* Açılan kutuda **ASP.NET Core 2,2** ' ı seçin. Şablon listesinden **Web uygulaması** ' nı seçin.
* **Kimlik doğrulaması**altında **Değiştir** ' i seçin ve kimlik doğrulamasını **bireysel kullanıcı hesapları**olarak ayarlayın. **Tamam**’ı seçin.
* **Yeni ASP.NET Core Web uygulaması oluştur** penceresinde **Oluştur**' u seçin.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Açık [tümleşik Terminalini](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Dizinleri (`cd`), projeyi içerecek bir klasöre değiştirin.

* Aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  code -r WebApp1
  ```

  * Komut WebApp1 klasöründe yeni bir Razor Pages projesi oluşturur. `dotnet new`
  * `-uld`SQLite yerine LocalDB kullanır. SQLite `-uld` kullanmak için atlayın.
  * `-au Individual`Tek kimlik doğrulaması için kodu oluşturur.
  * Komutu, Visual Studio Code yeni bir örneğinde WebApp1 klasörünü açar. `code`

* ' WebApp1 ' içinde derleme **ve hata ayıklama için gerekli varlıkların bulunduğu bir iletişim kutusu görünür. Bunları ekleyin mi?** **Evet**' i seçin.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Seçin **dosya** > **yeni çözüm**.
* Kenar çubuğunda **.NET Core** > **uygulaması** ' nı seçin. **Web uygulaması** şablonunu seçin. **İleri**’yi seçin.
* **Hedef Framework** açılan öğesini **.NET Core 2,2**olarak ayarlayın. **İleri**’yi seçin.
* Bir **Proje adı**girin. **Konumu**onaylayın veya değiştirin. **Oluştur**’u seçin.

---

## <a name="apply-migrations"></a>Geçişleri Uygula

* Uygulamayı çalıştırın ve **Kaydet** bağlantısını seçin.
* Yeni hesap için e-posta ve parolayı girip **Kaydet**' i seçin.
* Geçişleri uygulamak için yönergeleri izleyin.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>Oturum açma sağlayıcıları tarafından atanan belirteçleri depolamak için SecretManager kullanın

Sosyal oturum açma sağlayıcıları, kayıt işlemi sırasında **uygulama kimliği** ve **uygulama gizli** belirteçleri atar. Tam belirteç adları sağlayıcıya göre farklılık gösterir. Bu belirteçler, uygulamanızın API 'lerine erişmek için kullandığı kimlik bilgilerini temsil eder. Belirteçler, [gizli yöneticinin](xref:security/app-secrets#secret-manager)yardımıyla uygulama yapılandırmanızla bağlantılı olabilecek "gizli dizileri" oluşturur. Gizli dizi, belirteçleri *appSettings. JSON*gibi bir yapılandırma dosyasında depolamanın daha güvenli bir alternatifidir.

> [!IMPORTANT]
> Gizli yönetici yalnızca geliştirme amaçlıdır. [Azure Key Vault yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)Azure test ve üretim gizli dizilerini saklayabilir ve koruyabilirsiniz.

Aşağıdaki her bir oturum açma sağlayıcısı tarafından atanan belirteçleri depolamak için [ASP.NET Core konusundaki geliştirme sırasında uygulama gizli dizileri Için güvenli depolama](xref:security/app-secrets) bölümündeki adımları izleyin.

## <a name="setup-login-providers-required-by-your-application"></a>Uygulamanız için gereken oturum açma sağlayıcılarını ayarlama

Uygulamanızı ilgili sağlayıcıları kullanacak şekilde yapılandırmak için aşağıdaki konuları kullanın:

* [Facebook](xref:security/authentication/facebook-logins) yönergeleri
* [Twitter](xref:security/authentication/twitter-logins) yönergeleri
* [Google](xref:security/authentication/google-logins) yönergeleri
* [Microsoft](xref:security/authentication/microsoft-logins) yönergeleri
* [Diğer sağlayıcı](xref:security/authentication/otherlogins) yönergeleri

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>İsteğe bağlı olarak parola ayarla

Bir dış oturum açma sağlayıcısına kaydolduysanız, uygulamaya kayıtlı bir parolanız yok demektir. Bu, site için bir parola oluşturup hatırlamanızı konuma almayı azaltır, ancak aynı zamanda dış oturum açma sağlayıcısına bağımlı hale getirir. Dış oturum açma sağlayıcısı kullanılamıyorsa, Web sitesinde oturum açamazsınız.

Bir parola oluşturmak ve dış sağlayıcılar ile oturum açma işlemi sırasında ayarladığınız e-postanızı kullanarak oturum açmak için:

* **Yönet** görünümüne gitmek için sağ üst köşedeki **Merhaba &lt;e-&gt; posta diğer adı** bağlantısını seçin.

![Web uygulaması yönetme görünümü](index/_static/pass1a.png)

* **Oluştur**’u seçin

![Parola sayfanızı ayarlama](index/_static/pass2a.png)

* Geçerli bir parola ayarlayın ve bunu kullanarak e-postalarınız ile oturum açabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Bu makalede dış kimlik doğrulaması tanıtılmıştır ve ASP.NET Core uygulamanıza dış oturum açma bilgileri eklemek için gereken önkoşullar açıklanacaktır.

* Uygulamanız için gereken sağlayıcıları için oturum açma işlemlerini yapılandırmak üzere sağlayıcıya özgü sayfalara başvurun.

* Kullanıcı ve bunların erişim ve yenileme belirteçleriyle ilgili ek verileri kalıcı hale getirmek isteyebilirsiniz. Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.
