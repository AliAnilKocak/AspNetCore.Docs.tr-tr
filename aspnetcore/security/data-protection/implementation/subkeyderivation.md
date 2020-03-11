---
title: ASP.NET Core 'de alt anahtar türetme ve kimliği doğrulanmış şifreleme
author: rick-anderson
description: ASP.NET Core Data Protection alt anahtar türetme ve kimliği doğrulanmış şifrelemenin uygulama ayrıntılarını öğrenin.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: bbfde378755b09cd5b1217b8cf66249b9fa1d6ad
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660554"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>ASP.NET Core 'de alt anahtar türetme ve kimliği doğrulanmış şifreleme

<a name="data-protection-implementation-subkey-derivation"></a>

Anahtar halkasındaki çoğu anahtar, bir tür entropi içerir ve "CBC-Mode ENCRYPTION + HMAC doğrulaması" veya "GCM Encryption + doğrulaması" belirten algoritmik bilgilerine sahip olur. Bu gibi durumlarda, bu anahtar için ana anahtar malzemesi (veya KM) olarak katıştırılmış entropi 'ye başvurduk ve gerçek şifreleme işlemleri için kullanılacak anahtarları türetmek için bir anahtar türetme işlevi gerçekleştirdik.

> [!NOTE]
> Anahtarlar soyuttur ve özel bir uygulama aşağıda belirtildiği gibi davranmayabilir. Anahtar, yerleşik fabrikalarımızın birini kullanmak yerine kendi `IAuthenticatedEncryptor` uygulanmasını sağlıyorsa, bu bölümde açıklanan mekanizma artık geçerli değildir.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>Ek kimliği doğrulanmış veriler ve alt anahtar türetme

`IAuthenticatedEncryptor` arabirimi tüm kimliği doğrulanmış şifreleme işlemleri için çekirdek arabirim işlevi görür. `Encrypt` yöntemi iki ara bellek alır: düz metin ve Adtionalaıditeddata (AAD). Düz metin içerik akışı `IDataProtector.Protect`çağrısını değiştirmez, ancak AAD sistem tarafından oluşturulur ve üç bileşenden oluşur:

1. Veri koruma sisteminin bu sürümünü tanımlayan 32-bit Magic Header 09 F0 C9 F0.

2. 128 bitlik anahtar kimliği.

3. Bu işlemi gerçekleştiren `IDataProtector` oluşturan amaç zincirinden oluşturulmuş değişken uzunluklu bir dize.

AAD, üç bileşenin de kayıt düzeni için benzersiz olduğundan, bunu, şifreleme işlemlerimizin tümünde KM 'yi kullanmak yerine, KM 'den yeni anahtar türetmede kullanabiliriz. Her `IAuthenticatedEncryptor.Encrypt`çağrısı için aşağıdaki anahtar türetme işlemi gerçekleşir:

( K_E, K_H ) = SP800_108_CTR_HMACSHA512(K_M, AAD, contextHeader || keyModifier)

Burada, (bkz. [NıST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1), AŞAĞıDAKI parametrelerle NIST SP800-108 KDF 'yi arıyoruz:

* Anahtar türetme anahtarı (KDK) = K_M

* PRF = HMACSHA512

* Label = Adtionalaıseteddata

* bağlam = contextHeader | | keyModifier

Bağlam üst bilgisi değişken uzunluktadır ve temelde K_E ve K_H türettiğimiz algoritmaların bir parmak izi olarak görev yapar. Anahtar değiştirici, `Encrypt` her bir çağrı için rastgele oluşturulan 128 bitlik bir dizedir ve KDF 'ye yapılan diğer tüm girişler sabit olsa bile, bu belirli kimlik doğrulama şifreleme işlemi için KE ve KH 'nin benzersiz olmasını sağlamak için hizmet verir.

CBC modu şifreleme + HMAC doğrulama işlemleri için | K_E | , simetrik blok şifre anahtarının uzunluğu ve | K_H | HMAC yordamının Özet boyutudur. GCM şifreleme + doğrulama işlemleri için | K_H | = 0.

## <a name="cbc-mode-encryption--hmac-validation"></a>CBC modu şifreleme + HMAC doğrulaması

Yukarıdaki mekanizma aracılığıyla K_E oluşturulduktan sonra rastgele bir başlatma vektörü oluşturur ve düz metin şifreler için simetrik blok şifre algoritmasını çalıştırır. Başlatma vektörü ve şifreli dosyalar, MAC 'i oluşturmak için anahtar K_H ile başlatılan HMAC yordamı aracılığıyla çalıştırılır. Bu işlem ve dönüş değeri aşağıda grafik olarak gösterilir.

![CBC modunda işlem ve dönüş](subkeyderivation/_static/cbcprocess.png)

*Çıkış: = keyModifier | | IV | | E_cbc (K_E, IV, veri) | | HMAC (K_H, IV | | E_cbc (K_E, IV, veri))*

> [!NOTE]
> `IDataProtector.Protect` uygulama, bir [MAGIC üst bilgisini ve anahtar kimliğini](xref:security/data-protection/implementation/authenticated-encryption-details) çağırana döndürmeden önce çıktının önüne alacak. Sihirli üstbilgi ve anahtar kimliği, [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad)'nin örtük bir parçası olduğundan ve anahtar değiştiricisi KDF 'ye giriş olarak beslendiği için, bu, son döndürülen yükün her bir BAYTıNıN Mac tarafından doğrulanması anlamına gelir.

## <a name="galoiscounter-mode-encryption--validation"></a>Galoa/sayaç modu şifreleme + doğrulama

Yukarıdaki mekanizmayla K_E oluşturulduktan sonra rastgele bir 96-bit nonce oluşturur ve simetrik blok şifre algoritmasını şifreler düz metin olarak çalıştırır ve 128 bit kimlik doğrulama etiketini oluşturur.

![GCM modu işlemi ve döndürme](subkeyderivation/_static/galoisprocess.png)

*Çıkış: = keyModifier | | nonce | | E_gcm (K_E, nonce, veri) | | authTag*

> [!NOTE]
> GCM yerel olarak AAD kavramını desteklese de, AAD parametresi için bir boş dizeyi GCM 'ye geçirmek için AAD 'yi yalnızca özgün KDF 'ye besliyoruz. Bunun nedeni iki katdır. İlk olarak, [çevikliği desteklemek için](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) K_M doğrudan şifreleme anahtarı olarak kullanmak istemezsiniz. Ayrıca, GCM, girişlerinde çok sıkı benzersizlik gereksinimleri uygular. GCM şifreleme yordamının, aynı (anahtar, nonce) çiftiyle iki veya daha fazla farklı giriş verisi kümesinde çağrılması olasılığı 2 ^ 32 ' yi aşmamalıdır. K_E düzeltireceğiz 2 ^-32 sınırının afoul 'i çalıştırmadan önce 2 ^ 32 ' den fazla şifreleme işlemi gerçekleştiremedik. Bu çok fazla sayıda işlem gibi görünebilir, ancak yüksek trafikli bir Web sunucusu, bu anahtarların normal ömrü içinde boyutundaydı gün içinde 4.000.000.000 istek aracılığıyla değişebilir. 2 ^-32 olasılık sınırının uyumlu kalmasını sağlamak için, belirli bir K_M için kullanılabilir işlem sayısını genişleten bir 128 bit anahtar değiştiricisi ve 96-bit nonce kullanmaya devam ediyoruz. Tasarımın basitliği için, CBC ve GCM işlemleri arasında KDF kodu yolunu paylaşıyoruz ve AAD 'nin KDF 'de zaten kabul edildiği için, bunu GCM yordamına iletmeniz gerekmez.
