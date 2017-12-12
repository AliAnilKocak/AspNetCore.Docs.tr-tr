---
title: "Veri Koruma API'ları ile çalışmaya başlama"
author: rick-anderson
description: "Bu belge koruma ve uygulama veri koruması kaldırıldığında ASP.NET Core veri koruma API kullanımı açıklanmaktadır."
keywords: ASP.NET Core, veri koruma
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 39b7a73c-29d4-4137-b311-49529adcf3cb
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 535bfaf2077cda91c27e7d0d68b9804e8596070e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
# <a name="getting-started-with-the-data-protection-apis"></a>Veri Koruma API'ları ile çalışmaya başlama

<a name="security-data-protection-getting-started"></a>

Basit ve koruma verilerini aşağıdaki adımlardan oluşur:

1. Veri koruyucu bir veri koruma sağlayıcıdan oluşturun.

2. Çağrı `Protect` yöntemi ile korumak istediğiniz verileri.

3. Çağrı `Unprotect` yöntemi verilerle istediğiniz geri düz metne dönüştürecek.

Çoğu çerçeveler ve ASP.NET veya SignalR, gibi uygulama modelleri zaten veri koruma sisteminde yapılandırmak ve bağımlılık ekleme erişim bir hizmet kapsayıcısı ekleyin. Aşağıdaki örnek, bir hizmet kapsayıcısı bağımlılık ekleme için yapılandırma ve veri koruma yığını kaydetme, veri koruma sağlayıcısı dı aracılığıyla alma, koruyucusu ve koruma sonra kaldırmayı veri oluşturma gösterir

[!code-csharp[Main](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Bir koruyucu oluşturduğunuzda bir veya daha fazla sağlamalısınız [amacı dizeleri](consumer-apis/purpose-strings.md). Bir amaç dize tüketicileri arasında yalıtım sağlar. Örneğin, "Yeşil" amacı dizesi ile oluşturulan bir koruyucu "mor" bir amacı koruyucusu tarafından sağlanan verilerin korumasını mümkün olmayacaktır.

>[!TIP]
> Örneklerini `IDataProtectionProvider` ve `IDataProtector` olan birden çok çağıranlar için iş parçacığı. Bu bileşen bir başvuru edinir sonra hedeflenen bir `IDataProtector` çağrısıyla `CreateProtector`, birden çok çağrılar için bu başvuru kullanacağı `Protect` ve `Unprotect`.
>
>Çağrı `Unprotect` korumalı yükü doğrulandı veya yararlanılarak CryptographicException özel durum oluşturacak. Bazı bileşenler hatalarını yok saymak isteyebilirsiniz sırasında işlemleri; korumayı Kaldır kimlik doğrulaması tanımlama bilgileri okuyan bir bileşeni bu hatayı işleyebilir ve isteği, tanımlama bilgisi hiç sahipmiş gibi ele alın yerine depolayabileceği isteği başarısız. Bu davranış istediğiniz bileşenleri tüm özel durumları swallowing yerine CryptographicException özellikle catch.
