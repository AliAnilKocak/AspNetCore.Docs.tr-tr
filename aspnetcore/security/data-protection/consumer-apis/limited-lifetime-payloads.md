---
title: "Korumalı yüklerini ömrü sınırlama"
author: rick-anderson
description: "Bu belge, ASP.NET Core veri koruma API'ları kullanarak korumalı bir yükü ömrü sınırlamak açıklanmaktadır."
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: d631851b5b933d75c37a308f492840e3442e6f1a
ms.sourcegitcommit: 7ac15eaae20b6d70e65f3650af050a7880115cbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/02/2018
---
# <a name="limiting-the-lifetime-of-protected-payloads"></a>Korumalı yüklerini ömrü sınırlama

Burada belirlenen bir süre sonra süresi dolar korumalı bir yükü oluşturmak için uygulama geliştiricisi istediği senaryo vardır. Örneğin, korumalı yükü yalnızca bir saat için geçerli olacağını bir parola sıfırlama belirteci temsil edebilir. Geliştirici bir katıştırılmış sona erme tarihini içerir, kendi yük biçimi oluşturmak kesinlikle mümkündür ve Gelişmiş geliştiriciler bunu yine de yapmak isteyebilirsiniz, ancak bu süre sonu yönetme geliştiriciler çoğunluğu için can sıkıcı büyüyebilir.

Bu bizim Geliştirici hedef kitlesi, paket kolaylaştırmak için [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) otomatik olarak ayarlanmış bir süre sonra süresi dolacak yüklerini oluşturmak için yardımcı programı API'lerini içerir. Bu API'leri kapatarak askıda `ITimeLimitedDataProtector` türü.

## <a name="api-usage"></a>API kullanımı

`ITimeLimitedDataProtector` Koruma ve koruması kaldırıldığında, zaman sınırlı / Self süresi dolan yükü için çekirdek arabirimi bir arabirimdir. Örneği oluşturmak için bir `ITimeLimitedDataProtector`, önce bir normal örneği gerekir. [Idataprotector](overview.md) belirli bir amaç ile oluşturulmuş. Bir kez `IDataProtector` örneği kullanılabilir, çağrı `IDataProtector.ToTimeLimitedDataProtector` bir koruyucu yerleşik sona erme özellikleriyle geri almak için genişletme yöntemi.

`ITimeLimitedDataProtector` Aşağıdaki API yüzeyi ve genişletme yöntemlerini gösterir:

* CreateProtector (dize amacı): ITimeLimitedDataProtector - bu API benzer varolan `IDataProtectionProvider.CreateProtector` oluşturmak için kullanılabilir olduğunu [amaçla zincirleri](purpose-strings.md) kök zaman sınırlı koruyucusu gelen.

* (Byte [] düz metin, DateTimeOffset sona erme) korunmasına: byte]

* Koruma (byte [] düz metin, TimeSpan ömrü): byte]

* (Byte [] düz) korumak: byte]

* (String düz metin, DateTimeOffset sona erme) korunmasına: dize

* Koruma (dize düz metin, TimeSpan ömrü): dize

* (String düz) korumak: dize

Çekirdek yanı sıra `Protect` yalnızca düz metin, alması yöntemleri çalışmayıp'ın sona erme tarihi belirterek izin veren yeni aşırı. Sona erme tarihini mutlak bir tarih belirtilebilir (aracılığıyla bir `DateTimeOffset`) veya göreli zaman olarak (geçerli sisteminden zaman aracılığıyla bir `TimeSpan`). Bir süre sonu almaz bir aşırı çağrılırsa, yükü dolmayacak varsayılır.

* (Byte [] protectedData, DateTimeOffset sona erme çıkışı) korumasını: byte]

* (Byte [] protectedData) korumasını: byte]

* (DateTimeOffset sona erme çıkışı dize protectedData) korumasını: dize

* (String protectedData) korumasını: dize

`Unprotect` Yöntemleri özgün korumasız veri döndürür. Yükü henüz Süresi dolmamışsa mutlak zaman aşımı çıkış parametresi özgün korumasız veri birlikte isteğe bağlı olarak döndürülür. Yükü süresi dolmuşsa, tüm aşırı Unprotect yöntemi CryptographicException özel durum oluşturacak.

>[!WARNING]
> Bu uzun vadeli veya belirsiz Kalıcılık gerektiren yüklerini korumak için bu API'leri kullanın belirlemeleri değil. "I bir ay sonra kalıcı olarak kurtarılamaz olmasını korumalı yükü için göze alabilir?" iyi altın kural hizmet verebilir; yanıt ise hiçbir sonra geliştiriciler alternatif API'leri göz önünde bulundurmalısınız.

Örnek kullanır [dı olmayan kod yollarının](../configuration/non-di-scenarios.md) veri koruma sisteminde başlatmasını için. Bu örneği çalıştırmak için önce Microsoft.AspNetCore.DataProtection.Extensions paketine başvuru eklediğinizden emin olun.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
