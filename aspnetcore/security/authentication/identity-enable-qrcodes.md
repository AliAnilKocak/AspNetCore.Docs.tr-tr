---
title: ASP.NET Core 'daki TOTP Authenticator uygulamaları için QR kodu oluşturmayı etkinleştirme
author: rick-anderson
description: ASP.NET Core iki öğeli kimlik doğrulamasıyla çalışan TOTP Authenticator uygulamaları için QR kod üretimini nasıl etkinleştireceğinizi öğrenin.
ms.author: riande
ms.date: 08/14/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: 42ddddeaa329ac5ff5b2b40cbf9ebffa68f6d4cf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774437"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>ASP.NET Core 'daki TOTP Authenticator uygulamaları için QR kodu oluşturmayı etkinleştirme

::: moniker range="<= aspnetcore-2.0"

QR kodları ASP.NET Core 2,0 veya üstünü gerektirir.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core, bireysel kimlik doğrulama için kimlik doğrulayıcı uygulamaları desteğiyle birlikte gönderilir. Zamana bağlı bir kerelik parola algoritması (TOTP) kullanan iki öğeli kimlik doğrulama (2FA) Authenticator uygulaması, 2FA için önerilen sektördür. 2. TOTP kullanan 2FA, SMS 2FA için tercih edilir. Bir Authenticator uygulaması, kullanıcıların kullanıcı adını ve parolasını onayladıktan sonra girmesi gereken 6 ' dan 8 basamaklı bir kod sağlar. Genellikle bir akıllı telefona bir kimlik doğrulayıcı uygulaması yüklenir.

ASP.NET Core Web uygulaması şablonları, kimlik doğrulayıcılar destekler, ancak QRCode üretimi için destek sağlamaz. QRCode üreteçleri, 2FA kurulumunu kolaylaştırır. Bu belge, 2FA yapılandırma sayfasına [QR kod](https://wikipedia.org/wiki/QR_code) üretimi ekleme konusunda size kılavuzluk eder.

İki öğeli kimlik doğrulaması, [Google](xref:security/authentication/google-logins) veya [Facebook](xref:security/authentication/facebook-logins)gibi bir dış kimlik doğrulama sağlayıcısı kullanılarak gerçekleşmez. Dış oturumlar, dış oturum açma sağlayıcısının sağladığı mekanizmaya karşı korunur. Örneğin, [Microsoft](xref:security/authentication/microsoft-logins) kimlik doğrulama sağlayıcısı bir donanım anahtarı ya da başka bir 2FA yaklaşımı gerektirdiğini göz önünde bulundurun. Varsayılan Şablonlar "yerel" 2FA uyguladığında, kullanıcıların yaygın olarak kullanılan bir senaryo olmayan iki 2FA yaklaşımının yerine getirmek gerekir.

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>2FA yapılandırma sayfasına QR kodları ekleme

Bu yönergeler depodaki https://davidshimjs.github.io/qrcodejs/ *QRCode. js* ' i kullanır.

* [QRCode. js JavaScript kitaplığını](https://davidshimjs.github.io/qrcodejs/) projenizdeki `wwwroot\lib` klasöre indirin.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* */Areas/Identity/Pages/Account/Manage/enabledoğru* [ Identity ](xref:security/authentication/scaffold-identity)
* */Areas/Identity/Pages/Account/Manage/enabledoğrulayıcısı Tor.exe*içinde, dosyanın sonundaki `Scripts` bölümü bulun:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* *Sayfalar/hesap/Yönet/enableauthenticator. cshtml* (Razor sayfalar) veya *Görünümler/Yönet/enableauthenticator. cshtml* (MVC) içinde, dosyanın sonundaki `Scripts` bölümü bulun:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* Eklediğiniz `qrcodejs` kitaplığa `Scripts` bir başvuru ve QR kodu oluşturma çağrısı eklemek için bölümü güncelleştirin. Aşağıdaki gibi görünmelidir:

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* Bu yönergelere bağlanan paragrafı silin.

Uygulamanızı çalıştırın ve QR kodunu taramanızı ve kimlik doğrulayıcı ile ilgili kodu doğrulayabilmek için emin olun.

## <a name="change-the-site-name-in-the-qr-code"></a>QR kodundaki site adını değiştirme

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

QR kodundaki site adı, projenizi ilk kez oluştururken seçtiğiniz proje adından alınır. `GenerateQrCodeUri(string email, string unformattedKey)` */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*içindeki yöntemi arayarak bunu değiştirebilirsiniz.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

QR kodundaki site adı, projenizi ilk kez oluştururken seçtiğiniz proje adından alınır. `GenerateQrCodeUri(string email, string unformattedKey)` Bunu, *Pages/Account/Manage/enableauthenticator. cshtml. cs* (Razor Pages) dosyasında ya da *Controllers/managecontroller. cs* (MVC) dosyasındaki yöntemine bakarak değiştirebilirsiniz.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

Şablondaki varsayılan kod aşağıdaki gibi görünür:

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

Çağrısındaki `string.Format` ikinci parametre, çözüm adından alınan sitenizin adıdır. Herhangi bir değere değiştirilebilir, ancak her zaman URL kodlamalı olmalıdır.

## <a name="using-a-different-qr-code-library"></a>Farklı bir QR kod kitaplığı kullanma

QR kod kitaplığı 'nı tercih ettiğiniz kitaplıkla değiştirebilirsiniz. HTML, kitaplığınızın sağladığı `qrCode` MEKANIZMAYA bir QR kodu yerleştirebileceğiniz bir öğesi içerir.

QR kodu için doğru şekilde biçimlendirilen URL şu şekilde kullanılabilir:

* `AuthenticatorUri`Model özelliği.
* `data-url``qrCodeData` öğesinde özelliği.

## <a name="totp-client-and-server-time-skew"></a>TOTP istemci ve sunucu saati eğriltme

TOTP (zaman tabanlı bir kerelik parola) kimlik doğrulaması, hem sunucu hem de Doğrulayıcı cihazına doğru bir zamana sahip olur. Belirteçler yalnızca en az 30 saniye için geçerlidir. TOTP 2FA oturum açma işlemleri başarısız olursa, sunucu saatinin doğru olduğundan ve tercihen doğru bir NTP hizmeti ile eşitlendiğinden emin olun.

::: moniker-end
