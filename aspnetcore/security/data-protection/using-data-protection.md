---
title: ASP.NET Core 'de veri koruma API 'Lerini kullanmaya başlayın
author: rick-anderson
description: Bir uygulamadaki verileri korumak ve korumayı kaldırmak için ASP.NET Core veri koruma API 'Lerini kullanmayı öğrenin.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 1b0dc6756de55d9ce35eb08ca037e4d4b1fede75
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405620"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>ASP.NET Core 'de veri koruma API 'Lerini kullanmaya başlayın

<a name="security-data-protection-getting-started"></a>

En basit olan verileri korumak aşağıdaki adımlardan oluşur:

1. Bir veri koruma sağlayıcısından veri koruyucusu oluşturun.

2. Korumak istediğiniz `Protect` verilerle yöntemi çağırın.

3. `Unprotect`Düz metin haline döndürmek istediğiniz verilerle yöntemi çağırın.

ASP.NET Core veya gibi birçok çerçeve ve uygulama modeli, SignalR veri koruma sistemini zaten yapılandırıp bağımlılık ekleme yoluyla erişenbir hizmet kapsayıcısına ekler. Aşağıdaki örnek, bağımlılık ekleme ve veri koruma yığınını kaydetme, veri koruma sağlayıcısını dı aracılığıyla alma, bir koruyucu oluşturma ve verilerin korumasını kaldırma için bir hizmet kapsayıcısını yapılandırmayı gösterir.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Bir koruyucu oluşturduğunuzda bir veya daha fazla [Amaç dizesi](xref:security/data-protection/consumer-apis/purpose-strings)sağlamanız gerekir. Amaç dizesi, tüketiciler arasında yalıtım sağlar. Örneğin, "yeşil" bir amaç dizesiyle oluşturulan bir koruyucu, "mor" amacını taşıyan bir koruyucu tarafından belirtilen verilerin korumasını yapamaz.

>[!TIP]
> Ve örnekleri `IDataProtectionProvider` , `IDataProtector` birden çok çağıranlar için iş parçacığı güvenlidir. Bir bileşen bir öğesine çağrısıyla öğesine bir başvuru aldıktan sonra `IDataProtector` `CreateProtector` , ve ' a yönelik birden çok çağrı için bu başvuruyu kullanacaktır `Protect` `Unprotect` .
>
>`Unprotect`Korumalı yük doğrulanamazsa veya çözümlenememişse, öğesine yapılan bir çağrı CryptographicException oluşturur. Bazı bileşenler, kaldırma işlemleri sırasında hataları yoksaymak isteyebilir; kimlik doğrulama tanımlama bilgilerini okuyan bir bileşen bu hatayı işleyebilir ve isteği, isteğin hemen başarısız olması yerine hiç bir tanımlama bilgisine sahip olmamış gibi değerlendirir. Bu davranışın, tüm özel durumlara izin vermek yerine CryptographicException özel olarak yakalamalı bileşenler.
