---
title: Güvenli ASP.NET Core Blazor WebAssembly
author: guardrex
description: Blazor WebAssemlby uygulamalarını tek sayfalı uygulamalar (maça 'Lar) olarak güvenli hale getirme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: a65d47e55960d6e7bfeb672c0a1e6a7a305ad7ee
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989482"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>Güvenli ASP.NET Core Blazor WebAssembly

Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor WebAssembly uygulamalarının tek sayfalı uygulamalarla (maça) aynı şekilde güvenliği sağlanır. Kullanıcıların maça üzerinde kimlik doğrulaması için birkaç yaklaşım vardır, ancak en yaygın ve kapsamlı yaklaşım, [Open ID Connect (OıDC)](https://openid.net/connect/)gibi [OAuth 2,0 protokolüne](https://oauth.net/)dayalı bir uygulama kullanmaktır.

## <a name="authentication-library"></a>Kimlik doğrulama kitaplığı

Blazor WebAssembly, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` kitaplığı aracılığıyla OıDC kullanarak uygulamaları kimlik doğrulama ve yetkilendirme işlemini destekler. Kitaplığı ASP.NET Core arka uçlara karşı sorunsuz kimlik doğrulama için bir dizi temel sağlar. Kitaplık, [kimlik sunucusu](https://identityserver.io/)ÜZERINDE oluşturulan API yetkilendirme desteğiyle ASP.NET Core kimliğini tümleştirir. Kitaplık, OpenID sağlayıcıları (OP) olarak adlandırılan OıDC 'yi destekleyen herhangi bir üçüncü taraf kimlik sağlayıcısında (IP) kimlik doğrulaması yapabilir.

Blazor WebAssembly ' deki kimlik doğrulama desteği, temeldeki kimlik doğrulama protokolü ayrıntılarını işlemek için kullanılan *oidc-Client. js* kitaplığının üzerine kurulmuştur.

Benzer site tanımlama bilgilerinin kullanımı gibi, maça 'Ları doğrulamak için diğer seçenekler. Ancak, Blazor WebAssembly 'nin mühendislik tasarımı, Blazor WebAssembly uygulamalarında kimlik doğrulaması için en iyi seçenek olarak oAuth ve OıDC üzerinde kapatılmıştır. JSON Web belirteçlerini temel alan [belirteç tabanlı kimlik doğrulaması](xref:security/anti-request-forgery#token-based-authentication) [(jwts)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) , işlevsel ve güvenlik nedenleriyle [tanımlama bilgisi tabanlı kimlik doğrulaması](xref:security/anti-request-forgery#cookie-based-authentication) üzerinden seçilmiştir:

* Belirteç tabanlı bir protokol kullanmak, belirteçler tüm isteklerde gönderilmediğinden daha küçük bir saldırı yüzeyi alanı sunar.
* Belirteçleri açıkça gönderildiğinden, sunucu uç noktaları [siteler arası Istek sahteciliği (CSRF)](xref:security/anti-request-forgery) için koruma gerektirmez. Bu, MVC veya Razor sayfaları uygulamalarıyla birlikte Blazor Weelsembly uygulamalarını barındırmanıza olanak tanır.
* Belirteçlerin tanımlama bilgilerinden daha dar izinleri vardır. Örneğin, belirteçler Kullanıcı hesabını yönetmek ya da bu işlevsellik açık bir şekilde uygulanmadığı takdirde kullanıcının parolasını değiştirmek için kullanılamaz.
* Belirteçler, varsayılan olarak bir saat, saldırı penceresini sınırlayan kısa bir yaşam süresine sahiptir. Belirteçler de herhangi bir zamanda iptal edilebilir.
* İstemci ve sunucu için kimlik doğrulama işlemiyle ilgili olarak kendi içinde bulunan JWTs teklifi garantisi. Örneğin, bir istemci, aldığı belirteçlerin meşru olduğunu ve belirli bir kimlik doğrulama işleminin bir parçası olarak yayıldığını tespit etmek ve doğrulamak anlamına gelir. Üçüncü bir taraf, kimlik doğrulama işleminin ortasında bir belirteci geçirmeyi denerse, istemci anahtarlamalı belirteci algılayabilir ve kullanmaktan kaçınabilir.
* OAuth ve OıDC içeren belirteçler, uygulamanın güvenli olduğundan emin olmak için doğru şekilde davranan kullanıcı aracısına güvenmeyin.
* OAuth ve OıDC gibi belirteç tabanlı protokoller aynı güvenlik özellikleriyle barındırılan ve tek başına uygulamaların kimliğini doğrulamaya ve yetkilendirmeye olanak tanır.

## <a name="authentication-process-with-oidc"></a>OıDC ile kimlik doğrulama işlemi

`Microsoft.AspNetCore.Components.WebAssembly.Authentication` kitaplığı, OıDC kullanarak kimlik doğrulaması ve yetkilendirme uygulamak için çeşitli temel olanaklar sunar. Büyük şartlar altında, kimlik doğrulaması aşağıdaki gibi çalışmaktadır:

* Anonim Kullanıcı oturum açma düğmesini seçtiğinde veya [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliği uygulanmış bir sayfa istediğinde, kullanıcı uygulamanın oturum açma sayfasına (`/authentication/login`) yönlendirilir.
* Oturum açma sayfasında, kimlik doğrulama kitaplığı yetkilendirme uç noktasına yeniden yönlendirme hazırlar. Yetkilendirme uç noktası, Blazor WebAssembly uygulamasının dışındadır ve ayrı bir kaynak üzerinde barındırılabilir. Uç nokta, kullanıcının kimliğinin yapılıp yapılmayacağını ve yanıt olarak bir veya daha fazla belirteç vermeyi belirlemekten sorumludur. Kimlik doğrulama kitaplığı, kimlik doğrulama yanıtını almak için bir oturum açma geri araması sağlar.
  * Kullanıcının kimliği doğrulanmadıysa, Kullanıcı, genellikle ASP.NET Core kimlik olan temel alınan kimlik doğrulama sistemine yönlendirilir.
  * Kullanıcının kimliği zaten doğrulandıktan sonra, yetkilendirme uç noktası uygun belirteçleri oluşturur ve tarayıcıyı oturum açma geri çağırma uç noktasına (`/authentication/login-callback`) yeniden yönlendirir.
* Blazor WebAssembly uygulaması, oturum açma geri çağırma uç noktasını (`/authentication/login-callback`) yüklediğinde, kimlik doğrulama yanıtı işlenir.
  * Kimlik doğrulama işlemi başarıyla tamamlanırsa, kullanıcının kimliği doğrulanır ve isteğe bağlı olarak kullanıcının istediği özgün URL 'ye geri gönderilir.
  * Kimlik doğrulama işlemi herhangi bir nedenle başarısız olursa, Kullanıcı oturum açma başarısız sayfasına gönderilir (`/authentication/login-failed`) ve bir hata görüntülenir.
