---
title: "Veri koruma giriş"
author: rick-anderson
description: "Bu belge, veri koruma kavramını sunmaktadır ve ilişkili ASP.NET Core API tasarım ilkeleri özetlenmektedir."
keywords: ASP.NET Core, veri koruma
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4542cd37-b47c-454c-be19-d1b5810d67fe
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/introduction
ms.openlocfilehash: dd34f2e69ea0f6427ee5f446d6440dfab17a42c4
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
# <a name="introduction-to-data-protection"></a>Veri koruma giriş

Web uygulamaları, genellikle güvenlik açısından duyarlı verileri depolamak gerekir. Windows Masaüstü uygulamaları için DPAPI sağlar ancak bu web uygulamaları için uygun değil. ASP.NET Core veri koruma yığını Geliştirici anahtar yönetimi ve döndürme dahil olmak üzere verileri korumak için kullanabileceğiniz basit, kullanımı kolay bir şifreleme API sağlar.

ASP.NET Core veri koruma yığını uzun vadeli yerini olarak hizmet için tasarlanmış <machineKey> ASP.NET öğesinde 1.x - 4.x. Eski şifreleme yığın eksikliklerini çoğunu Çoğu modern uygulamalar karşılaşabileceğiniz olası kullanım durumu için Giden kutusu çözümünü sağlarken gidermek için tasarlanmıştır.

## <a name="problem-statement"></a>Sorun bildirimi

Genel Sorun bildirimi tek bir tümcedeki temellerini belirtilebilir: güvenilir bilgi sonraki alma için kalıcı olması gerekir, ancak Kalıcılık mekanizma güvenmeyin. "Güvenilmeyen bir istemci yoluyla güvenilen gidiş dönüş durumu istiyorum."olarak web bağlamında bu yazılı olabilir

Kurallı bu bir kimlik doğrulama tanımlama bilgisi veya taşıyıcı örneğidir belirteci. Sunucu oluşturur bir "I Groot çıkarım ve xyz izinleriniz" token ve istemciye aktarır. Bazı ileriki bir tarihte istemci sunucuya geri belirtecini sunacaktır, ancak sunucu istemci belirteç sahte kurmadı güvence çeşit gerekiyor. Bu nedenle ilk gereksinim: Orijinallik Sertifikası (paketini bütünlük, yetkisiz değiştirmeye karşı sağlama).

Kalıcı durum sunucusu tarafından güvenilir olduğundan, bu durum işletim ortamına özgü bilgiler içerebilir beklenir. Bu, bir dosya yolu, bir izin, tanıtıcı veya diğer dolaylı başvuru veya bazı bir sunucuya özgü veri parçası olabilir. Bu tür bilgiler genellikle güvenilmeyen bir istemci açıklanmaması gereken. Böylece ikinci gereksinimi: gizliliği.

Modern uygulamalar bileşenlerden oluşan olduğundan, son olarak, ne gördük bileşenleri tek tek sistemdeki diğer bileşenler dikkate almaksızın bu sistem yararlanmak istersiniz kalır. Örneği için bir taşıyıcı belirteci bileşen bu yığını kullanıyorsanız, aynı yığınına de kullanıyor olabilecek bir anti-CSRF mekanizması girişime olmadan çalışacağını. Böylece son gereksinimi: yalıtım.

Daha fazla kısıtlamaları bizim gereksinimleri kapsamını daraltmak için sunuyoruz. Cryptosystem içinde çalışan tüm hizmetlerin eşit oranda güvenilir ve veri oluşturulan veya doğrudan sunduğumuz denetim hizmetler dışında tüketilen gerekmediğine varsayıyoruz. Ayrıca, her web hizmeti isteğine bir veya birden çok kez cryptosystem geçebilir beri işlemleri olabildiğince hızlı gerektirir. Bu simetrik şifreleme senaryomuz için ideal hale getirir ve biz asimetrik şifreleme gibi kadar gerekli birer indirim.

## <a name="design-philosophy"></a>Tasarımı felsefesi

Varolan yığını sorun belirleyerek başlatıldı. Biz, vardı sonra biz varolan çözümleri yatay inceledik ve varolan bir çözümü oldukça biz Aranan yetenekleri vardı sonuçları. Biz sonra birkaç yol gösterici kurallara göre bir çözüm tasarlandı.

* Sistem Yapılandırması basitliği sunmaktadır. İdeal olarak sistem sıfır yapılandırma olacaktır ve geliştiricilerin çalıştıran başından başlayarak oluşabilir. Bu belirli yapılandırmalar basit hale getirmek için geliştiriciler nerede (örneğin, anahtar depo) belirli bir yönüne yapılandırmanıza gerek durumlarda göz önünde bulundurarak verilmesi gerekir.

* Basit bir tüketiciye yönelik API sunar. API'ler, doğru bir şekilde kullanmak kolay ve hatalı kullanmayı zor olması gerekir.

* Geliştiriciler anahtar yönetimi ilkeleri de öğrenmelisiniz değil. Sistem algoritması seçimi ve anahtar yaşam süresi Geliştirici adınıza işlemelidir. İdeal olarak Geliştirici hiçbir zaman bile ham anahtar malzemesi erişiminiz olması.

* Anahtarları bekleyen mümkün olduğunda korunmalıdır. Sistem, uygun varsayılan koruma mekanizması şekil ve otomatik olarak uygula.

Bu ilkeler aklınızda ile biz basit bir geliştirilen [kullanımı kolay](using-data-protection.md) veri koruma yığını.

ASP.NET Core veri koruma API değil öncelikle yöneliktir gizli yüklerini belirsiz kalıcılığını. Diğer teknolojiler ister [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) ve [Azure Rights Management](https://docs.microsoft.com/rights-management/) belirsiz depolama senaryosu için daha uygundur ve buna bağlı olarak güçlü anahtar yönetim olanaklarına sahip olursunuz. Bu, gizli verilerin uzun dönem koruma için ASP.NET Core veri koruma API'ları kullanarak bir geliştirici yasaklanması hiçbir şey belirtti.

## <a name="audience"></a>Hedef Kitle

Veri koruma sisteminde beş ana paketlere ayrılmıştır. Bu API'leri çeşitli yönlerini üç ana izleyicileri hedef;

1. [Tüketici API'leri genel bakış](consumer-apis/overview.md) hedef uygulama ve framework geliştiriciler.

   "Yığını nasıl çalıştığı hakkında veya nasıl yapılandırılacağı hakkında bilgi edinin istemiyorum. Yalnızca, başka bir işlem olarak basit bir şekilde mümkün olduğunca API'leri başarıyla kullanımının yüksek olasılık gerçekleştirmek istiyorum."

2. [Yapılandırma API'leri](configuration/overview.md) hedef uygulama geliştiricileri ve sistem yöneticileri.

   "I veri koruma sisteminde my ortam varsayılan olmayan yolları veya ayarları gerektirir bildirmeniz gerekir."

3. Özel ilke uygulama sorumlu genişletilebilirlik API hedef geliştiriciler. Bu API'leri kullanımını nadir durumlarda sınırlı ve karşılaştı, güvenlik kullanan geliştiriciler.

   "I gerçekten benzersiz davranış gereksinimlere sahip olduğundan sistem içinde tüm bir bileşen değiştirmeniz gerekiyor. My gereksinimlerini karşılayan bir eklenti oluşturmak için API yüzeyi uncommonly kullanılan parçalarının öğrenin istiyorum."

## <a name="package-layout"></a>Paket düzeni

Veri koruma yığını beş paketlerini oluşur.

* Microsoft.AspNetCore.DataProtection.Abstractions temel IDataProtectionProvider ve Idataprotector arabirimleri içerir. Ayrıca, bu türleri (örneğin, IDataProtector.Protect aşırı) ile çalışma yardımcı olabilecek yararlı genişletme yöntemleri içerir. Daha fazla bilgi için tüketici arabirimleri bölümüne bakın. Başkası veri koruma sisteminde başlatmasını sorumludur ve API'ları yalnızca kullanıyor, başvuru Microsoft.AspNetCore.DataProtection.Abstractions isteyeceksiniz.

* Microsoft.AspNetCore.DataProtection çekirdek şifreleme işlemleri, anahtar yönetimi, yapılandırması ve genişletilebilirlik gibi veri koruma sisteminde çekirdek uygulamasını içerir. Veri koruma sisteminde başlatmasını için sorumlu (örneğin, bir IServiceCollection ekleme) ya da değiştirme veya davranışını genişletme, başvuru Microsoft.AspNetCore.DataProtection istemeniz.

* Microsoft.AspNetCore.DataProtection.Extensions hangi geliştiricilere yararlı olabilir ancak çekirdek pakete ait olmayan ek API'leri içerir. Örneğin, bu paket, bir basit bir "Hiçbir bağımlılık ekleme Kurulum sahip bir özel anahtar depolama dizin gösteren sistem örneği" API (daha fazla bilgi) içerir. Ayrıca, korumalı yüklerini (daha fazla bilgi) ömrü sınırlamak için genişletme yöntemleri içerir.

* Microsoft.AspNetCore.DataProtection.SystemWeb yönlendirmek için mevcut ASP.NET 4.x uygulamasına yüklenebilir kendi <machineKey> yeni veri koruma yığını kullanmayı işlemleri. Bkz: [Uyumluluk](compatibility/replacing-machinekey.md#compatibility-replacing-machinekey) daha fazla bilgi için.

* Microsoft.AspNetCore.Cryptography.KeyDerivation PBKDF2 parola yordamı karma uygulaması sağlar ve kullanıcı parolalarını güvenli bir şekilde işlemek için gereken sistemleri tarafından kullanılabilir. Bkz: [parola karma](consumer-apis/password-hashing.md) daha fazla bilgi için.
