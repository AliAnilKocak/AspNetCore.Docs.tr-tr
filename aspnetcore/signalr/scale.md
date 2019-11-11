---
title: ASP.NET Core SignalR üretim barındırma ve ölçeklendirme
author: bradygaster
description: ASP.NET Core SignalRkullanan uygulamalardaki performans ve ölçeklendirme sorunlarının nasıl önleneceğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/scale
no-loc:
- SignalR
ms.openlocfilehash: a215ce489746a7585cf4e72d4f04e0eac588b44c
ms.sourcegitcommit: a7bbe3890befead19440075b05b9674351f98872
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905718"
---
# <a name="aspnet-core-opno-locsignalr-hosting-and-scaling"></a>ASP.NET Core SignalR barındırma ve ölçeklendirme

, [Andrew Stanton-nuri](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)ve [Tom Dykstra](https://github.com/tdykstra),

Bu makalede, ASP.NET Core SignalRkullanan yüksek trafik uygulamalarına yönelik barındırma ve ölçeklendirme konuları açıklanmaktadır.

## <a name="sticky-sessions"></a>Yapışkan oturumlar

SignalR, belirli bir bağlantı için tüm HTTP isteklerinin aynı sunucu işlemi tarafından işlenmesini gerektirir. Sunucu grubunda (birden çok sunucu) SignalR çalıştığında, "yapışkan oturumlar" kullanılmalıdır. "Yapışkan oturumlar", bazı yük dengeleyiciler tarafından oturum benzeşimi olarak da adlandırılır. Azure App Service istekleri yönlendirmek için [uygulama Isteği yönlendirme](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) kullanır. Azure App Service "ARR benzeşimi" ayarının etkinleştirilmesi "yapışkan oturumlar" sağlayacaktır. Yapışkan oturumların gerekmediği tek koşullar şunlardır:

1. Tek bir sunucuda barındırırken, tek bir işlemde.
1. Azure SignalR hizmetini kullanırken.
1. Tüm istemciler **yalnızca** WebSockets kullanacak şekilde yapılandırıldığında **ve** istemci yapılandırmasında [skipanlaşma ayarı](xref:signalr/configuration#configure-additional-options) etkinse.

Tüm diğer koşullarda (Redsıs geri düzlemi kullanıldığında dahil), sunucu ortamının yapışkan oturumlar için yapılandırılması gerekir.

SignalRiçin Azure App Service yapılandırma hakkında yönergeler için bkz. <xref:signalr/publish-to-azure-web-app>.

## <a name="tcp-connection-resources"></a>TCP bağlantı kaynakları

Bir Web sunucusunun destekleyebileceği eşzamanlı TCP bağlantısı sayısı sınırlıdır. Standart HTTP istemcileri, *kısa ömürlü* bağlantıları kullanır. Bu bağlantılar, istemci boşta kaldığında ve daha sonra yeniden açıldığı zaman kapatılabilir. Öte yandan, SignalR bir bağlantı *kalıcıdır*. SignalR bağlantılar, istemci boşta kaldığında bile açık kalır. Çok sayıda istemciye hizmet veren yüksek trafikli bir uygulamada, bu kalıcı bağlantılar sunucuların en fazla bağlantı sayısına ulaşmasına neden olabilir.

Kalıcı bağlantılar, her bağlantıyı izlemek için ek bellek de tüketir.

SignalR göre bağlantıyla ilgili kaynakların ağır kullanımı, aynı sunucuda barındırılan diğer Web uygulamalarını etkileyebilir. SignalR açılıp en son kullanılabilir TCP bağlantılarını tutuyorsa, aynı sunucudaki diğer Web uygulamalarına da daha fazla bağlantı bulunmaz.

Sunucuda bağlantı biterse rastgele yuva hataları ve bağlantı sıfırlama hataları görürsünüz. Örneğin:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

SignalR kaynak kullanımını diğer Web uygulamalarındaki hatalara neden olacak şekilde korumak için, diğer Web uygulamalarınızdan farklı sunucularda SignalR çalıştırın.

SignalR kaynak kullanımını SignalR uygulamasında hatalara neden olacak şekilde korumak için, bir sunucunun işleyeceği bağlantı sayısını sınırlamak üzere ölçeği ölçeklendirin.

## <a name="scale-out"></a>Ölçeklendirme

SignalR kullanan bir uygulamanın, bir sunucu grubu için sorunlar oluşturan tüm bağlantılarını izlemesi gerekir. Bir sunucu ekleyin ve diğer sunucuların hakkında bilgi sahibi olmadığı yeni bağlantıları alır. Örneğin, aşağıdaki diyagramdaki her bir sunucuda SignalR diğer sunuculardaki bağlantılardan haberdar değildir. Sunuculardan birindeki SignalR tüm istemcilere ileti göndermek istediğinde, ileti yalnızca bu sunucuya bağlı olan istemcilere gider.

![Ölçeklendirme [! Üs. NO-LOC (SignalR)] geri düzlemi olmadan](scale/_static/scale-no-backplane.png)

Bu sorunu çözmeye yönelik seçenekler [Azure SignalR hizmetidir](#azure-signalr-service) ve [redsıs arkadüzledir](#redis-backplane).

## <a name="azure-opno-locsignalr-service"></a>Azure SignalR hizmeti

Azure SignalR hizmeti, arka düzlemi yerine bir ara sunucu. İstemci sunucuya bir bağlantı başlattığında, istemci hizmete bağlanmak için yeniden yönlendirilir. Bu işlem aşağıdaki diyagramda gösterilmiştir:

![Azure 'a bağlantı kurma [! Üs. NO-LOC (SignalR)] hizmeti](scale/_static/azure-signalr-service-one-connection.png)

Sonuç olarak, hizmetin tüm istemci bağlantılarını yönetmesi, her sunucunun aşağıdaki diyagramda gösterildiği gibi yalnızca küçük bir sabit bağlantı sayısına ihtiyacı vardır:

![Hizmete bağlı istemciler, hizmete bağlı sunucular](scale/_static/azure-signalr-service-multiple-connections.png)

Bu genişleme yaklaşımına yönelik bu yaklaşım, Redsıs geri düzlemi 'nin diğer avantajlarından biridir:

* [İstemci benzeşimi](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)olarak da bilinen yapışkan oturumlar gerekli değildir, çünkü istemciler, bağlandıklarında Azure SignalR hizmetine anında yönlendirilir.
* SignalR bir uygulama, gönderilen ileti sayısına göre ölçeklenebilir, ancak Azure SignalR hizmeti herhangi bir sayıda bağlantıyı işleyecek şekilde otomatik olarak ölçeklendirilir. Örneğin, binlerce istemci olabilir, ancak saniye başına yalnızca birkaç ileti gönderiliyorsa, SignalR uygulamanın yalnızca bağlantıları işlemek için birden çok sunucuya ölçeklendirilmesi gerekmez.
* SignalR bir uygulama, SignalRolmayan bir Web uygulamasından daha fazla bağlantı kaynağı kullanmaz.

Bu nedenlerden dolayı, App Service, VM 'Ler ve kapsayıcılar dahil olmak üzere Azure üzerinde barındırılan tüm ASP.NET Core SignalR uygulamaları için Azure SignalR hizmetini öneririz.

Daha fazla bilgi için bkz. [Azure SignalR hizmeti belgeleri](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Redis kartı

[Redsıs](https://redis.io/) , bir yayımlama/abonelik modeliyle bir mesajlaşma sistemini destekleyen bellek içi anahtar-değer deposudur. SignalR redin backdüzlemi, iletileri diğer sunuculara iletmek için pub/Sub özelliğini kullanır. İstemci bir bağlantı yaptığında, bağlantı bilgileri geri düzleme geçirilir. Bir sunucu tüm istemcilere ileti göndermek istediğinde, bu geri düzlemi gönderir. Biriktirme listesi, tüm bağlı istemcileri ve bunların hangi sunuculara olduğunu bilir. İletiyi ilgili sunucuları aracılığıyla tüm istemcilere gönderir. Bu işlem aşağıdaki diyagramda gösterilmiştir:

![Redsıs geri düzlemi, bir sunucudan tüm istemcilere gönderilen ileti](scale/_static/redis-backplane.png)

Redsıs geri düzlemi, kendi altyapınızda barındırılan uygulamalar için önerilen genişleme yaklaşımıdır. Azure SignalR hizmeti, veri merkezinizdeki bir Azure veri merkezi arasındaki bağlantı gecikmesi nedeniyle Şirket içi uygulamalarla üretim kullanımı için pratik bir seçenek değildir.

Daha önce bahsedilen Azure SignalR hizmet avantajları Redsıs geri düzlemi için dezavantajlardır:

* [İstemci benzeşimi](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)olarak da bilinen yapışkan oturumlar gereklidir. Sunucuda bir bağlantı başlatıldıktan sonra bağlantı o sunucuda kalmaya devam etmek zorunda kalır.
* Birkaç ileti gönderilse bile SignalR bir uygulama, istemci sayısına göre ölçeklendirmelidir.
* SignalR uygulaması, SignalRolmayan bir Web uygulamasından çok daha fazla bağlantı kaynağı kullanır.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure SignalR hizmeti belgeleri](/azure/azure-signalr/signalr-overview)
* [Redsıs geri düzlemi ayarlama](xref:signalr/redis-backplane)
