---
title: ASP.NET Core Facebook dış oturum açma Kurulumu
author: rick-anderson
description: Bu öğretici, var olan bir ASP.NET Core uygulamaya Facebook hesabı kullanıcı kimlik doğrulaması tümleştirmesini gösterir.
ms.author: riande
ms.date: 08/01/2017
uid: security/authentication/facebook-logins
ms.openlocfilehash: 53e5fa3ccee44451646c84e58260db23e59d6cbd
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36273405"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NET Core Facebook dış oturum açma Kurulumu

Tarafından [Valeriy Novytskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu öğreticide, kullanıcılarınızın üzerinde oluşturulmuş bir örnek ASP.NET Core 2.0 projeyi kullanarak kendi Facebook hesabıyla oturum olanak tanımak nasıl gösterilir [önceki sayfaya](xref:security/authentication/social/index). Facebook kimlik doğrulaması gerektiren [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet paketi. İzleyerek bir Facebook uygulama kimliği oluşturarak başlangıç [resmi adımları](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>İçinde Facebook uygulaması oluşturma

* Gidin [Facebook geliştiricilerin uygulama](https://developers.facebook.com/apps/) sayfasında ve oturum açın. Bir Facebook hesabıyla zaten yoksa kullanmak **Facebook için kaydolun** bağlantısı oluşturmak için oturum açma sayfası.

* Dokunun **yeni bir uygulama eklemek** yeni bir uygulama kimliği oluşturmak için sağ üst köşesinde düğmesi

   ![Geliştiriciler portalı için Facebook Microsoft Edge'de açın](index/_static/FBMyApps.png)

* Formu doldurun ve dokunun **uygulama kimliği oluşturma** düğmesi.

   ![Yeni uygulama kimliği formu oluşturma](index/_static/FBNewAppId.png)

* Üzerinde **bir ürün seçmek** sayfasında, **Ayarla** üzerinde **Facebook oturum açma** kart.

   ![Ürün kurulum sayfası](index/_static/FBProductSetup.png)

* **Hızlı Başlangıç** Sihirbazı ile başlar **bir Platform seçin** ilk sayfası. Sihirbazı'nı tıklatarak şimdilik atlama **ayarları** soldaki menüde bağlantı:

   ![Atla hızlı başlangıç](index/_static/FBSkipQuickStart.png)

* İle sunulan **istemci OAuth ayarları** sayfa:

![İstemci OAuth Ayarları sayfası](index/_static/FBOAuthSetup.png)

* Geliştirme URI girin ile */signin-facebook* içine eklenen **geçerli OAuth yeniden yönlendirme URI'ler** alan (örneğin: `https://localhost:44320/signin-facebook`). Daha sonra Bu öğreticide yapılandırılmış Facebook kimlik doğrulama istekleri otomatik olarak işleyecek */signin-facebook* OAuth akış uygulamak için rota.

> [!NOTE]
> URI */signin-facebook* Facebook kimlik doğrulama sağlayıcısı varsayılan geri dönüş ayarlanır. Facebook kimlik doğrulaması ara yazılımı üzerinden devralınan yapılandırılırken, varsayılan geri çağırma URI değiştirebilirsiniz [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) sınıf.

* Tıklatın **değişiklikleri kaydetmek**.

* Tıklatın **Pano** sol gezinti bağlantısı. 

    Bu sayfada, Not, `App ID` ve `App Secret`. ASP.NET Core uygulamanıza hem de sonraki bölümde ekleyecek:

   ![Facebook Geliştirici Panosu](index/_static/FBDashboard.png)

* Site dağıtırken yeniden ziyaret etmeniz gereken **Facebook oturum açma** Kurulum sayfasında ve yeni bir ortak URI kaydedin.

## <a name="store-facebook-app-id-and-app-secret"></a>Facebook uygulama kimliği ve uygulama gizli anahtarı depolar

Bağlantı Facebook gibi hassas ayarları `App ID` ve `App Secret` kullanarak uygulamanızı yapılandırma için [gizli Yöneticisi](xref:security/app-secrets). Bu öğreticinin amaçları doğrultusunda belirteçleri ad `Authentication:Facebook:AppId` ve `Authentication:Facebook:AppSecret`.

Güvenli bir şekilde depolamak için aşağıdaki komutları yürütün `App ID` ve `App Secret` gizli Yöneticisi'ni kullanarak:

```console
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a>Facebook kimlik doğrulamasını yapılandırma

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x/)

Facebook hizmetinde eklemek `ConfigureServices` yönteminde *haline* dosyası:

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](~/includes/chain-auth-providers.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x/)

Yükleme [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) paket.

* Visual Studio 2017 ile bu paketi yüklemek için projeyi sağ tıklatın ve **NuGet paketlerini Yönet**.
* .NET Core CLI ile yüklemek için proje dizininde aşağıdakileri yürütün:

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

Facebook Ara yazılımında eklemek `Configure` yönteminde *haline* dosyası:

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

Bkz: [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) Facebook kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için API Başvurusu. Yapılandırma seçenekleri için kullanılabilir:

* Farklı kullanıcı bilgilerini ister.
* Oturum açma deneyimini özelleştirmesini sorgu dizesi bağımsız değişkenleri ekleyin.

## <a name="sign-in-with-facebook"></a>Facebook ile oturum açma

Uygulamanızı çalıştırın ve tıklatın **oturum**. Facebook ile oturum için bir seçenek görürsünüz.

![Web uygulaması: doğrulanmayan kullanıcı](index/_static/DoneFacebook.png)

Tıkladığınızda **Facebook**, kimlik doğrulaması için Facebook için yönlendirilir:

![Facebook kimlik doğrulaması sayfası](index/_static/FBLogin.png)

Facebook kimlik doğrulaması varsayılan olarak genel profil ve e-posta adresi ister:

![Facebook kimlik doğrulaması sayfası](index/_static/FBLoginDone.png)

Facebook kimlik bilgilerinizi girdikten sonra e-posta ayarlayabileceğiniz geri sitenize yönlendirilir.

Facebook kimlik bilgilerinizi kullanarak şimdi kaydedilir:

![Web uygulaması: kimliği doğrulanmış kullanıcı](index/_static/Done.png)

## <a name="troubleshooting"></a>Sorun giderme

* **ASP.NET Core 2.x yalnızca:** ise kimlik çağırarak yapılandırılmamış `services.AddIdentity` içinde `ConfigureServices`, kimlik doğrulaması gerçekleştirmeye neden olur *ArgumentException: 'SignInScheme' seçeneği belirtilmelidir*. Bu öğreticide kullanılan proje şablonu, bu yapılır sağlar.
* Site veritabanı, ilk geçiş uygulayarak oluşturulmadı, alırsınız *bir veritabanı işlemi başarısız oldu istek işlenirken* hata. Dokunun **uygulamak geçişler** veritabanını oluşturmak ve hata devam etmek için yenileyin.

## <a name="next-steps"></a>Sonraki adımlar

* Bu makalede, Facebook ile nasıl doğrulayabilir gösterdi. Listelenen diğer sağlayıcıları, kimlik doğrulaması için benzer bir yaklaşım izleyebilirsiniz [önceki sayfaya](xref:security/authentication/social/index).

* Azure web uygulaması için web sitenizi yayımladıktan sonra sıfırlamalıdır `AppSecret` Facebook Geliştirici Portalı'nda.

* Ayarlama `Authentication:Facebook:AppId` ve `Authentication:Facebook:AppSecret` olarak Azure portalında uygulama ayarları. Yapılandırma sistemi ortam değişkenlerinin anahtarları okumak için ayarlanır.
