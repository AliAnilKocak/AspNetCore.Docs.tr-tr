---
title: Azure Active Directory B2C ile bir ASP.NET Core Blazor Weelsembly barındırılan uygulaması güvenli hale getirme
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 12e09cf7e27f85473d84f42564d13e1c0ed5dff1
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434453"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Azure Active Directory B2C ile bir ASP.NET Core Blazor Weelsembly barındırılan uygulaması güvenli hale getirme

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Bu makalede, kimlik doğrulaması için [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir Blazor WebAssembly tek başına uygulamasının nasıl oluşturulacağı açıklanmaktadır.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Uygulamaları AAD B2C kaydetme ve çözüm oluşturma

### <a name="create-a-tenant"></a>Kiracı oluşturma

Öğreticideki yönergeleri izleyin: bir AAD B2C kiracı oluşturmak için [Azure Active Directory B2C kiracı oluşturun](/azure/active-directory-b2c/tutorial-create-tenant) ve aşağıdaki bilgileri kaydedin:

* AAD B2C örneği (örneğin, sonunda eğik çizgi içeren `https://contoso.b2clogin.com/`)
* AAD B2C kiracı etki alanı (örneğin, `contoso.onmicrosoft.com`)

### <a name="register-a-server-api-app"></a>Sunucu API 'SI uygulaması kaydetme

Eğitim bölümünde yer alan yönergeleri izleyin: *sunucu API 'si uygulamasına* yönelik AAD uygulamasını Azure portal **Azure Active Directory** > **uygulama kayıtları** alanına kaydetmek için [Azure Active Directory B2C bir uygulamayı kaydetme](/azure/active-directory-b2c/tutorial-register-applications) .

1. **Yeni kayıt**seçeneğini belirleyin.
1. Uygulama için bir **ad** sağlayın (örneğin, **Blazor sunucusu AAD B2C**).
1. **Desteklenen hesap türleri**için **herhangi bir kuruluş dizininde veya herhangi bir kimlik sağlayıcısında hesaplar ' ı seçin. Azure AD B2C kullanıcıları kimlik doğrulaması için.** Bu deneyim için (çok kiracılı).
1. *Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.
1. ** > ,** yönetici tarafından **OpenID 'ye uyum verildiğini ve offline_access izinlerinin** etkinleştirildiğini doğrulayın.
1. **Kaydol**’u seçin.

**API 'Yi kullanıma**sunma bölümünde:

1. **Kapsam ekle**’yi seçin.
1. **Kaydet ve devam et**’i seçin.
1. Bir **kapsam adı** sağlayın (örneğin, `API.Access`).
1. **Yönetici izni görünen adı** sağlayın (örneğin, `Access API`).
1. **Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.`).
1. **Durumun** **etkin**olarak ayarlandığını onaylayın.
1. **Kapsam Ekle**' yi seçin.

Aşağıdaki bilgileri kaydedin:

* *Sunucu API 'si uygulaması* Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111`)
* Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `222222222-2222-2222-2222-222222222222`)
* *Sunucu API 'si uygulaması* Uygulama KIMLIĞI URI 'SI (örneğin, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, Azure portal Istemci KIMLIĞI için varsayılan değer olarak değişebilir)
* Varsayılan kapsam (örneğin, `API.Access`)

### <a name="register-a-client-app"></a>İstemci uygulamasını kaydetme

Eğitim bölümünde yer alan yönergeleri izleyin: **Azure Active Directory** > **uygulama kayıtları** Azure Portal ALANıNA *istemci uygulaması* için AAD uygulaması kaydetmek üzere [bir uygulamayı yeniden Azure Active Directory B2C kaydedin](/azure/active-directory-b2c/tutorial-register-applications) :

1. **Yeni kayıt**seçeneğini belirleyin.
1. Uygulama için bir **ad** sağlayın (örneğin, **Blazor istemci AAD B2C**).
1. **Desteklenen hesap türleri**için **herhangi bir kuruluş dizininde veya herhangi bir kimlik sağlayıcısında hesaplar ' ı seçin. Azure AD B2C kullanıcıları kimlik doğrulaması için.** Bu deneyim için (çok kiracılı).
1. **Yeniden yönlendirme URI 'si** açılan listesini **Web**olarak ayarlayın ve `https://localhost:5001/authentication/login-callback`yeniden yönlendirme URI 'si sağlayın.
1. ** > ,** yönetici tarafından **OpenID 'ye uyum verildiğini ve offline_access izinlerinin** etkinleştirildiğini doğrulayın.
1. **Kaydol**’u seçin.

**Kimlik doğrulama** > **Platform yapılandırmalarında** **Web** > :

1. `https://localhost:5001/authentication/login-callback` **yeniden yönlendirme URI 'sinin** mevcut olduğunu onaylayın.
1. **Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.
1. Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.
1. **Kaydet** düğmesini seçin.

**API izinleri**:

1. Uygulamanın **Microsoft Graph** > **User. Read** iznine sahip olduğunu doğrulayın.
1. **Izin Ekle** ' yi ve ardından **API 'lerim**' i seçin.
1. **Ad** SÜTUNUNDAN *sunucu API uygulamasını* (örneğin, **Blazor sunucu AAD B2C**) seçin.
1. **API** listesini açın.
1. API 'ye erişimi etkinleştirin (örneğin, `API.Access`).
1. **Izin Ekle**' yi seçin.
1. **{Tenant Name} için yönetici Içeriği ver** düğmesini seçin. Onaylamak için **Evet**’i seçin.

**Giriş** > **Azure AD B2C** > **Kullanıcı akışları**:

[Kaydolma ve oturum açma Kullanıcı akışı oluşturma](/azure/active-directory-b2c/tutorial-create-user-flows)

En azından, `LoginDisplay` bileşenindeki (*Shared/LoginDisplay. Razor*) `context.User.Identity.Name` doldurmak için **uygulama talepleri** > **görünen ad** Kullanıcı özniteliği ' ni seçin.

Aşağıdaki bilgileri kaydedin:

* *İstemci* UYGULAMASı uygulama kimliğini (istemci kimliği) kaydedin (örneğin, `33333333-3333-3333-3333-333333333333`).
* Uygulama için oluşturulan kaydolma ve oturum açma Kullanıcı akış adını kaydedin (örneğin, `B2C_1_signupsignin`).

### <a name="create-the-app"></a>Uygulama oluşturma

Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, çıkış seçeneğini komuta bir yol (örneğin, `-o BlazorSample`) ile birlikte ekleyin. Klasör adı Ayrıca projenin adının bir parçası haline gelir.

## <a name="server-app-configuration"></a>Sunucu uygulaması yapılandırması

*Bu bölüm, çözümün **sunucu** uygulamasıyla ilgilidir.*

### <a name="authentication-package"></a>Kimlik doğrulama paketi

ASP.NET Core Web API 'Lerine yapılan kimlik doğrulama ve yetkilendirme desteği `Microsoft.AspNetCore.Authentication.AzureAD.UI`tarafından sağlanır:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

`AddAuthentication` yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır. `AddAzureADBearer` yöntemi, Azure Active Directory tarafından yayılan belirteçleri doğrulamak için gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication` ve `UseAuthorization` şunları doğrulayın:

* Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.
* Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a>Uygulama ayarları

*AppSettings. JSON* dosyası, erişim belirteçlerini doğrulamak IÇIN kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a>Hava tahmin denetleyicisi

Dalgalı tahmin denetleyicisi (*denetleyiciler/dalgalı) denetleyici. cs*), denetleyiciye uygulanan `[Authorize]` özniteliği ile korunan bir API sunar. Bunun anlaşılması **önemlidir** :

* Bu API denetleyicisindeki `[Authorize]` özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.
* Blazor WebAssembly uygulamasında kullanılan `[Authorize]` özniteliği yalnızca uygulamanın, uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiği konusunda bir ipucu işlevi görür.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>İstemci uygulama yapılandırması

*Bu bölüm, çözümün **istemci** uygulaması ile ilgilidir.*

### <a name="authentication-package"></a>Kimlik doğrulama paketi

Tek bir B2C hesabı (`IndividualB2C`) kullanmak üzere bir uygulama oluşturulduğunda, uygulama otomatik olarak [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`) için bir paket başvurusu alır. Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.

Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Önceki paket başvurusunda `{VERSION}`, <xref:blazor/get-started> makalesinde gösterilen `Microsoft.AspNetCore.Blazor.Templates` paketinin sürümü ile değiştirin.

`Microsoft.Authentication.WebAssembly.Msal` paketi, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paketini uygulamaya göre geçişli olarak ekler.

### <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısına `Microsoft.Authentication.WebAssembly.Msal` paketi tarafından sağlanmış `AddMsalAuthentication` uzantısı yöntemiyle kaydedilir. Bu yöntem, uygulamanın kimlik sağlayıcısıyla (IP) etkileşim kurması için gereken tüm hizmetleri ayarlar.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{DEFAULT SCOPE}");
});
```

`AddMsalAuthentication` yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder. Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.

Blazor WebAssembly şablonu, uygulamayı `dotnet new` komutuna (`{APP ID URI}/{DEFAULT SCOPE}`) sunulan varsayılan kapsam için güvenli bir API için bir erişim belirteci isteyecek şekilde otomatik olarak yapılandırır.

Varsayılan erişim belirteci kapsamları, erişim belirteci kapsamlarının listesini temsil eder:

* Oturum açma isteğine varsayılan olarak dahildir.
* Kimlik doğrulamasından hemen sonra bir erişim belirteci sağlamak için kullanılır.

Tüm kapsamlar Azure Active Directory kuralları başına aynı uygulamaya ait olmalıdır. Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a>Dizin sayfası

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a>Uygulama bileşeni

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin bileşeni

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay bileşeni

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Kimlik doğrulama bileşeni

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData bileşeni

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Uygulamayı sunucu projesinden çalıştırın. Visual Studio 'Yu kullanırken **Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authentication/azure-ad-b2c>
* [Öğretici: Azure Active Directory B2C kiracısı oluşturma](/azure/active-directory-b2c/tutorial-create-tenant)
