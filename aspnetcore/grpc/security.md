---
title: ASP.NET Core için gRPC 'de güvenlik konuları
author: jamesnk
description: ASP.NET Core için gRPC güvenlik konuları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: f84bec0ef485b701b2be36384a2e49b9b28e473d
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310365"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>ASP.NET Core için gRPC 'de güvenlik konuları

, [James bAyKiNg](https://twitter.com/jamesnk)

Bu makalede, gRPC 'yi .NET Core ile güvenli hale getirme hakkında bilgi verilmektedir.

## <a name="transport-security"></a>Taşıma güvenliği

gRPC iletileri HTTP/2 kullanılarak gönderilir ve alınır. Şunları öneririz:

* [Aktarım Katmanı Güvenliği (TLS)](https://tools.ietf.org/html/rfc5246) , üretim GRPC uygulamalarında iletileri güvenli hale getirmek için kullanılır.
* gRPC Hizmetleri yalnızca güvenli bağlantı noktalarını dinler ve bunlara yanıt vermelidir.

TLS, Kestrel içinde yapılandırılır. Kestrel uç noktalarını yapılandırma hakkında daha fazla bilgi için bkz. [Kestrel Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).

## <a name="exceptions"></a>Özel Durumlar

Özel durum iletileri genellikle bir istemciye görüntülenmemelidir gizli veriler olarak değerlendirilir. Varsayılan olarak, gRPC, bir gRPC hizmeti tarafından oluşturulan özel durumun ayrıntılarını istemciye göndermez. Bunun yerine, istemci bir hata oluştuğunu belirten genel bir ileti alır. İstemciye özel durum iletisi teslimi, [Enabledetailederrors](xref:grpc/configuration#configure-services-options)ile geçersiz kılınabilir (örneğin, geliştirme veya test). Özel durum iletileri, üretim uygulamalarındaki istemciye gösterilmemelidir.

## <a name="message-size-limits"></a>İleti boyutu sınırları

GRPC istemcilerine ve hizmetlerine gelen iletiler belleğe yüklenir. İleti boyutu sınırları, gRPC 'nin aşırı kaynak tüketmesini önlemeye yardımcı olmak için bir mekanizmadır.

gRPC gelen ve giden iletileri yönetmek için ileti başına boyut sınırlarını kullanır. Varsayılan olarak, gRPC gelen iletileri 4 MB ile sınırlandırır. Giden iletilerde sınır yoktur.

Sunucusunda, gRPC ileti limitleri bir uygulamadaki tüm hizmetler için ile `AddGrpc`yapılandırılabilir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

Ayrıca, kullanılarak `AddServiceOptions<TService>`tek bir hizmet için sınırlamalar da yapılandırılabilir. İleti boyutu sınırlarını yapılandırma hakkında daha fazla bilgi için bkz. [GRPC yapılandırması](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>İstemci sertifikası doğrulaması

[İstemci sertifikaları](https://tools.ietf.org/html/rfc5246#section-7.4.4) , bağlantı oluşturulduğunda başlangıçta onaylanır. Varsayılan olarak, Kestrel bir bağlantının istemci sertifikası için ek doğrulama gerçekleştirmez.

İstemci sertifikaları tarafından güvenliği sağlanan gRPC hizmetlerinin [Microsoft. AspNetCore. Authentication. Certificate](xref:security/authentication/certauth) paketini kullanması önerilir. ASP.NET Core sertifika kimlik doğrulaması, bir istemci sertifikasında aşağıdakiler de dahil olmak üzere ek doğrulama gerçekleştirir:

* Sertifikada geçerli bir genişletilmiş anahtar kullanımı (EKU) vardır
* Geçerlilik süresi içinde
* Sertifika iptalini denetle
