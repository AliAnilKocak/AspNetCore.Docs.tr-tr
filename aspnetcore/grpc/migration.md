---
title: GRPC hizmetlerini C Core 'dan ASP.NET Core geçirme
author: juntaoluo
description: Mevcut bir C çekirdekli tabanlı gRPC uygulamasını ASP.NET Core yığının üstünde çalışacak şekilde taşımayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 8f0d9dd980fa3281f30dc29d329d10ccd352ae72
ms.sourcegitcommit: 994da92edb0abf856b1655c18880028b15a28897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71278708"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>GRPC hizmetlerini C Core 'dan ASP.NET Core geçirme

[John Luo](https://github.com/juntaoluo) tarafından

Temel alınan yığının uygulanması nedeniyle, tüm özellikler [C Core tabanlı GRPC](https://grpc.io/blog/grpc-stacks) uygulamaları ve ASP.NET Core tabanlı uygulamalar arasında aynı şekilde çalışmaz. Bu belgede, iki yığın arasında geçiş yapmak için önemli farklılıklar vurgulanmıştır.

## <a name="grpc-service-implementation-lifetime"></a>gRPC hizmeti uygulama ömrü

ASP.NET Core yığınında, varsayılan olarak gRPC Hizmetleri [kapsamlı bir ömür](xref:fundamentals/dependency-injection#service-lifetimes)ile oluşturulur. Buna karşılık, gRPC C-Core varsayılan olarak [tek bir yaşam süresine](xref:fundamentals/dependency-injection#service-lifetimes)sahip bir hizmete bağlanır.

Kapsamlı ömür, hizmet uygulamasının kapsamlı ömürlerle diğer hizmetleri çözümlemesine izin verir. Örneğin, kapsamlı bir yaşam süresi aynı zamanda, `DbContext` Oluşturucu ekleme yoluyla dı kapsayıcısından da çözümlenir. Kapsamlı ömür kullanımı:

* Her istek için hizmet uygulamasının yeni bir örneği oluşturulur.
* Uygulama türündeki örnek üyeleri aracılığıyla istekler arasında durum paylaşmak mümkün değildir.
* Beklentisi, paylaşılan durumları dı kapsayıcısında tek bir hizmette depobir biçimde depokadır. Depolanan paylaşılan durumlar, gRPC hizmet uygulamasının oluşturucusunda çözümlenir.

Hizmet yaşam süreleri hakkında daha fazla bilgi için <xref:fundamentals/dependency-injection#service-lifetimes>bkz.

### <a name="add-a-singleton-service"></a>Tek bir hizmet ekleyin

GRPC C çekirdekli bir uygulamadan ASP.NET Core geçişi kolaylaştırmak için, hizmet uygulamasının hizmet ömrünü kapsamlı olarak tek başına değiştirmek mümkündür. Bu, bir hizmet uygulamasının bir örneğini dı kapsayıcısına eklemeyi içerir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Ancak, tek bir yaşam süresine sahip bir hizmet uygulamasının kapsamı, kapsamlı hizmetleri Oluşturucu ekleme yoluyla çözemeyebilir.

## <a name="configure-grpc-services-options"></a>GRPC Hizmetleri seçeneklerini yapılandırma

C Core tabanlı uygulamalarda `grpc.max_receive_message_length` , ve `grpc.max_send_message_length` gibi ayarlar [sunucu örneği oluşturulurken](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)ile `ChannelOption` yapılandırılır.

ASP.NET Core, GRPC, `GrpcServiceOptions` tür aracılığıyla yapılandırma sağlar. Örneğin, gRPC hizmetinin en büyük gelen ileti boyutu ile `AddGrpc`yapılandırılabilir. Aşağıdaki örnek 4 MB ile 16 `ReceiveMaxMessageSize` MB arasında varsayılan değer değiştirir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Yapılandırma hakkında daha fazla bilgi için bkz <xref:grpc/configuration>.

## <a name="logging"></a>Günlüğe Kaydetme

C çekirdekli tabanlı uygulamalar, `GrpcEnvironment` hata ayıklama amacıyla [günlükçüsü yapılandırmak](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) için kullanır. ASP.NET Core Stack, bu işlevselliği [günlüğe kaydetme API 'si](xref:fundamentals/logging/index)aracılığıyla sağlar. Örneğin, gRPC hizmetine Oluşturucu ekleme yoluyla bir günlükçü eklenebilir:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

C-Core tabanlı uygulamalar, [Server. Ports özelliği](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)aracılığıyla https 'yi yapılandırır. Benzer bir kavram, ASP.NET Core sunucuları yapılandırmak için kullanılır. Örneğin, Kestrel Bu işlevsellik için [uç nokta yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) kullanır.

## <a name="interceptors-and-middleware"></a>Yakalayıcılar ve ara yazılım

ASP.NET Core [Ara yazılım](xref:fundamentals/middleware/index) , C çekirdekli tabanlı GRPC uygulamalarındaki yakalayıcılar ile karşılaştırıldığında benzer işlevler sunar. Bir gRPC isteğini işleyen bir işlem hattı oluşturmak için, ara yazılım ve yakalayıcılar kavramsal olarak aynıdır. Bunlar her ikisi de iş hattındaki bir sonraki bileşenden önce veya sonra çalışmasına izin verir. Ancak ASP.NET Core, ana bilgisayar, temel alınan HTTP/2 iletilerinde çalışır, ancak dinleyici yöneticileri [Servercallcontext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)kullanarak GRPC soyutlama katmanı üzerinde çalışır.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
