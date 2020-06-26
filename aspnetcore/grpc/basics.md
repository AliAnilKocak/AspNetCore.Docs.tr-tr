---
title: C# içeren gRPC hizmetleri
author: juntaoluo
description: C# ile gRPC hizmetlerini yazarken temel kavramları öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/basics
ms.openlocfilehash: 08c755cf078fe71d09e8c7af2cc16f5c427495f1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407297"
---
# <a name="grpc-services-with-c"></a>C ile gRPC Hizmetleri\#

Bu belgede, C# dilinde [GRPC](https://grpc.io/docs/guides/) uygulamaları yazmak için gereken kavramlar özetlenmektedir. Burada ele alınan konular hem [C Core](https://grpc.io/blog/grpc-stacks)tabanlı hem de ASP.NET Core tabanlı GRPC uygulamaları için geçerlidir.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a>Proto dosyası

gRPC, API geliştirmesi için bir sözleşmenin ilk yaklaşımını kullanır. Protokol arabellekleri (protobellek) varsayılan olarak arabirim tasarım dili (IDL) olarak kullanılır. * \* . Proto* dosyası şunları içerir:

* GRPC hizmetinin tanımı.
* İstemciler ve sunucular arasında gönderilen iletiler.

Prototipsiz dosyaların sözdizimi hakkında daha fazla bilgi için, [resmi belgelere (protoarabellek)](https://developers.google.com/protocol-buffers/docs/proto3)bakın.

Örneğin, [gRPC hizmetini kullanmaya başlama](xref:tutorials/grpc/grpc-start)bölümünde kullanılan *Greet. proto* dosyasını düşünün:

* Bir `Greeter` hizmeti tanımlar.
* `Greeter`Hizmet bir çağrıyı tanımlar `SayHello` .
* `SayHello`bir `HelloRequest` ileti gönderir ve bir `HelloReply` ileti alır:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>C uygulamasına bir. proto dosyası ekleyin \#

* \* . Proto* dosyası bir projeye öğe grubuna eklenerek dahil edilir `<Protobuf>` :

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>. Proto dosyaları için C# araçları desteği

Araçları Paketi [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) , * \* . proto* dosyalarından C# varlıkları oluşturmak için gereklidir. Oluşturulan varlıklar (dosyalar):

* , Projenin oluşturulduğu her seferinde gerekli olarak oluşturulur.
* Projeye eklenmez veya kaynak denetimine iade edilmedi.
* *Obj* dizininde bulunan bir yapı yapıtı.

Bu paket hem sunucu hem de istemci projeleri için gereklidir. `Grpc.AspNetCore`Metapackage öğesine bir başvuru içerir `Grpc.Tools` . Sunucu projeleri `Grpc.AspNetCore` , Visual Studio 'Da Paket Yöneticisi 'ni kullanarak veya bir proje dosyasına bir ekleyerek ekleyebilir `<PackageReference>` :

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

İstemci projeleri `Grpc.Tools` , gRPC istemcisini kullanmak için gereken diğer paketlerle birlikte doğrudan başvurmalıdır. Çalışma zamanında araç paketi gerekli değildir, bu nedenle bağımlılık şu şekilde işaretlenir `PrivateAssets="All"` :

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Oluşturulan C# varlıkları

Araç paketi, eklenen * \* . proto* dosyalarında tanımlanan Iletileri temsil eden C# türlerini üretir.

Sunucu tarafı varlıklar için, soyut bir hizmet temel türü oluşturulur. Temel tür, *. proto* dosyasında bulunan tüm GRPC çağrılarının tanımlarını içerir. Bu temel türden türetilen somut bir hizmet uygulamasını oluşturun ve gRPC çağrılarının mantığını uygular. `greet.proto`Daha önce açıklanan örnek için, `GreeterBase` sanal bir yöntemi içeren bir soyut tür `SayHello` oluşturulur. Somut bir uygulama `GreeterService` , yöntemini geçersiz kılar ve gRPC çağrısını işleme mantığını uygular.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

İstemci tarafı varlıklar için somut bir istemci türü oluşturulur. *. Proto* dosyasındaki GRPC çağrıları, çağrılabilecek somut türdeki yöntemlere çevrilir. `greet.proto`Daha önce açıklanan örnek için somut bir `GreeterClient` tür oluşturulur. `GreeterClient.SayHelloAsync`Sunucuya bir gRPC çağrısı başlatmak için çağrısı yapın.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Varsayılan olarak, sunucu ve istemci varlıkları öğe grubuna dahil edilen her * \* . proto* dosyası için oluşturulur `<Protobuf>` . Sunucu projesinde yalnızca sunucu varlıklarının oluşturulmasını sağlamak için `GrpcServices` özniteliği olarak ayarlanır `Server` .

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Benzer şekilde, özniteliği `Client` istemci projelerinde olarak ayarlanır.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
