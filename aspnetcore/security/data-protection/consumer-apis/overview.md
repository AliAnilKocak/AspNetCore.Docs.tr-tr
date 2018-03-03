---
title: "Tüketici API'leri genel bakış"
author: rick-anderson
description: "Bu belge, çeşitli tüketici API'leri ASP.NET Core veri koruma kitaplıkta kullanılabilir kısa bir genel bakış sağlar."
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: 3aa0c4bc8d009147dd15571da4d7d63402e4c512
ms.sourcegitcommit: 7ac15eaae20b6d70e65f3650af050a7880115cbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/02/2018
---
# <a name="consumer-apis-overview"></a>Tüketici API'leri genel bakış

`IDataProtectionProvider` Ve `IDataProtector` arabirimlerdir temel arabirimleri tüketiciler veri koruma sistemi kullanır. Bulunan [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) paket.

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

Sağlayıcı arabirimi veri koruma sisteminde kökünü temsil eder. Koruma veya verilerin korumasını doğrudan kullanılamaz. Bunun yerine, bir başvuru tüketici almalısınız bir `IDataProtector` çağırarak `IDataProtectionProvider.CreateProtector(purpose)`amacı hedeflenen tüketici kullanım örneğini tanımlayan bir dize olmalıdır. Bkz: [amacı dizeleri](purpose-strings.md) Bu parametre ve uygun bir değer seçme hedefi üzerinde çok daha fazla bilgi için.

## <a name="idataprotector"></a>IDataProtector

Koruyucu arabirimi için bir çağrı tarafından döndürülen `CreateProtector`ve tüketicileri gerçekleştirmek için kullanabileceğiniz bu arabirim korumak ve işlemleri korumasını kaldırın.

Veri parçası korumak için verileri geçirmek `Protect` yöntemi. Hangi dönüştürür byte [] -> byte [] bir yöntem temel arabirimi tanımlar, ancak yok dize dönüştüren (bir genişletme yöntemi sağlanır) bir aşırı dize de ->. İki yöntem tarafından sunulan güvenlik aynıdır; Geliştirici, hangi aşırı kendi kullanım durumu için en uygun seçmeniz gerekir. Seçilen, aşırı koruma tarafından döndürülen değer yedeklemiş yöntemi şimdi (enciphered ve yetkisiz değiştirmeye karşı proofed) korunur ve uygulama güvenilmeyen bir istemci gönderebilirsiniz.

Daha önce korunan bir veri korumasını kaldırmak için korumalı veri geçirmek `Unprotect` yöntemi. (Byte [] vardır-tabanlı ve dize tabanlı aşırı geliştiriciye kolaylık sağlamak için.) Korumalı yükü için önceki bir çağrı tarafından oluşturulmuşsa `Protect` bu aynı üzerinde `IDataProtector`, `Unprotect` yöntemi özgün korumasız yükü döndürür. Korumalı yükü oynanmış veya farklı bir tarafından üretilmiş `IDataProtector`, `Unprotect` yöntemi CryptographicException oluşturur.

Kavramı aynı, farklı karşılaştırması `IDataProtector` TIES amacı kavramı yedekleyin. İki `IDataProtector` örnekleri aynı kökünden oluşturuldu `IDataProtectionProvider` ancak farklı amaç dizeleri çağrısında aracılığıyla `IDataProtectionProvider.CreateProtector`, kabul sonra [farklı koruyucuları](purpose-strings.md), ve bir edemez korumasını kaldırmak tarafından oluşturulan yükler.

## <a name="consuming-these-interfaces"></a>Bu arabirimleri kullanma

DI algılayan bir bileşen için bileşen yapmanızı hedeflenen kullanım olan bir `IDataProtectionProvider` kurucusu parametresinde ve bileşen örneği oluşturulduğunda dı sistem otomatik olarak bu hizmet sağlar.

> [!NOTE]
> Bazı uygulamalar (örneğin, konsol uygulaması veya ASP.NET 4.x uygulamaları) dı açıklanan mekanizması burada kullanamazlar uyumlu olmayabilir. Bu senaryolar başvurun [olmayan dı kullanan senaryolar](../configuration/non-di-scenarios.md) belge örneği alma hakkında daha fazla bilgi için bir `IDataProtection` dı giderek olmadan sağlayıcısı.

Aşağıdaki örnek, üç kavramları göstermektedir:

1. [Veri koruma sisteminde ekleme](../configuration/overview.md) hizmet kapsayıcısı

2. Örneğini almak için dı kullanarak bir `IDataProtectionProvider`, ve

3. Oluşturma bir `IDataProtector` gelen bir `IDataProtectionProvider` ve korumak ve veri korumasını kaldırmak için kullanma.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Bir genişletme yöntemi Microsoft.AspNetCore.DataProtection.Abstractions paketi içeren `IServiceProvider.GetDataProtector` Geliştirici kolaylık. Her iki alma tek bir işlem olarak yalıtan bir `IDataProtectionProvider` hizmet sağlayıcısı ve arama `IDataProtectionProvider.CreateProtector`. Aşağıdaki örnek kullanımını gösterir.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Örneklerini `IDataProtectionProvider` ve `IDataProtector` olan birden çok çağıranlar için iş parçacığı. Bir bileşen bir başvuru edinir sonra istemiş bir `IDataProtector` çağrısıyla `CreateProtector`, birden çok çağrılar için bu başvuru kullanacağı `Protect` ve `Unprotect`. Çağrı `Unprotect` korumalı yükü doğrulandı veya yararlanılarak CryptographicException özel durum oluşturacak. Bazı bileşenler hatalarını yok saymak isteyebilirsiniz sırasında işlemleri; korumayı Kaldır kimlik doğrulaması tanımlama bilgileri okuyan bir bileşeni bu hatayı işleyebilir ve isteği, tanımlama bilgisi hiç sahipmiş gibi ele alın yerine depolayabileceği isteği başarısız. Bu davranış istediğiniz bileşenleri tüm özel durumları swallowing yerine CryptographicException özellikle catch.
