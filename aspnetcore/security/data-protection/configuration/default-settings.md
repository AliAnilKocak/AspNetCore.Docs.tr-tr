---
title: Veri koruma anahtar yönetimi ve ASP.NET Core yaşam süresi
author: rick-anderson
description: Veri koruma anahtar yönetimi ve ASP.NET Core yaşam süresi hakkında bilgi edinin.
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: b43c14af015d5e03f46200c51a1218a581b1de0c
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/30/2018
ms.locfileid: "28887296"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>Veri koruma anahtar yönetimi ve ASP.NET Core yaşam süresi

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>Anahtar Yönetimi

İşletimsel ortamı algılar ve kendi başına anahtar yapılandırma işlemek uygulama çalışır.

1. Uygulama içinde barındırılıyorsa [Azure uygulamaları](https://azure.microsoft.com/services/app-service/), anahtarları şekilde kalıcı *%HOME%\ASP.NET\DataProtection-Keys* klasör. Bu klasör, ağ depolama tarafından yedeklenir ve uygulamayı barındıran tüm makinelerde eşitlenir.
   * Anahtarları bekleyen korumalı değil.
   * *DataProtection anahtarları* klasörü tüm örnekleri, bir uygulamanın tek dağıtım yuvasındaki anahtar halkası sağlar.
   * Hazırlama ve üretim gibi ayrı bir dağıtım yuvası anahtar halkası paylaşmayın. Örneğin üretime hazırlama değiştirmeyi veya kullanan bir dağıtım yuvası arasında taktığınızda / B testi, veri koruması'nı kullanarak herhangi bir uygulama anahtar halkası önceki yuvasına kullanarak depolanan verilerin şifresini çözmek mümkün olmayacaktır. Bu, kendi tanımlama bilgilerini korumak için veri koruması kullanır gibi standart ASP.NET Core tanımlama bilgisi kimlik doğrulaması kullanır, bir uygulamanın oturumunu oturum açmış kullanıcılara yol gösterir. Yuva bağımsız anahtar çalma üzerinde isterse, Azure Blob Storage, Azure anahtar kasası, bir SQL deposu gibi bir dış anahtar halkası sağlayıcısı kullanın veya Redis önbelleği.

1. Kullanıcı profili varsa, anahtarları için kalıcı *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* klasör. İşletim sistemi Windows ise, anahtarları DPAPI kullanılarak bekleme sırasında şifrelenir.

1. Uygulama IIS barındırılıyorsa, anahtarları ACLed yalnızca çalışan işlem hesabı için bir özel kayıt defteri anahtarı HKLM Kayıt defterinde kalıcı niteliktedir. Anahtarları DPAPI kullanılarak bekleme sırasında şifrelenir.

1. Bu koşulların hiçbiri eşleşirse, geçerli işlemin dışında anahtarları kalıcı değil. İşlem oluşturulan tüm aşağı kapattığında anahtarları kaybolur.

Geliştirici her zaman tam denetimi ve nasıl ve anahtarları depolandığı geçersiz kılabilirsiniz. Yukarıdaki ilk üç seçenekten nasıl benzer çoğu uygulamalar için iyi Varsayılanları sağlamalıdır ASP.NET  **\<machineKey >** otomatik oluşturma yordamları çalışılan geçmişte. Son, geri dönüş seçeneği belirtmek Geliştirici gerektiren tek bir senaryodur [yapılandırma](xref:security/data-protection/configuration/overview) ön anahtar Kalıcılık istiyor, ancak bu geri dönüş yalnızca nadir durumlarda gerçekleşir.

Bir Docker kapsayıcısı barındırdığında anahtarları Docker birim (paylaşılan bir birim veya kapsayıcının ömür devam ederse konak takılı bir birimde) bir klasörde kalıcı veya bir dış sağlayıcı gibi [Azure anahtar kasası](https://azure.microsoft.com/services/key-vault/) veya [Redis](https://redis.io/). Uygulamalar bir paylaşılan ağ birim erişemiyorsanız dış sağlayıcı ayrıca web grubu senaryolarda kullanışlıdır (bkz [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) daha fazla bilgi için).

> [!WARNING]
> Geliştirici yukarıda özetlenen kuralları geçersiz kılar ve belirli bir anahtar deposunda veri koruma sisteminde işaret ediyorsa, REST anahtarların otomatik şifreleme devre dışı bırakılır. Çalışmıyorken koruma aracılığıyla yeniden etkinleştirilmiş olabilir [yapılandırma](xref:security/data-protection/configuration/overview).

## <a name="key-lifetime"></a>Anahtar yaşam süresi

Anahtarları varsayılan olarak 90 gün ömrü vardır. Bir anahtar süresi dolduğunda, uygulama otomatik olarak yeni bir anahtar oluşturur ve yeni anahtar etkin anahtar olarak ayarlar. Sistemde Çekildi anahtarları kaldığı sürece, uygulamanızın bunlarla korunan tüm verileri şifresini çözebilir. Bkz: [anahtar yönetimi](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) daha fazla bilgi için.

## <a name="default-algorithms"></a>Varsayılan algoritmaları

Kullanılan varsayılan yükü koruması algoritması AES 256 CBC Orijinallik gizliliği ve HMACSHA256 içindir. Değiştirilen her 90 günde bir 512 bit ana anahtar yükü başına temelinde Bu algoritmalar için kullanılan iki alt anahtarlarında türetmek için kullanılır. Bkz: [alt anahtar türetme](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) daha fazla bilgi için.

## <a name="see-also"></a>Ayrıca bkz.

* [Anahtar yönetimi genişletilebilirliği](xref:security/data-protection/extensibility/key-management)
