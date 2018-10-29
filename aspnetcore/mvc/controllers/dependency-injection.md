---
title: ASP.NET core'da denetleyicilere bağımlılık ekleme
author: ardalis
description: ASP.NET Core MVC denetleyicileri bağımlılıklarını oluşturucuları bağımlılık ekleme ASP.NET Core ile açıkça aracılığıyla nasıl istek keşfedin.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 12247dbbbb6de3f8feb7bc37caec4ecf4bd21719
ms.sourcegitcommit: 375e9a67f5e1f7b0faaa056b4b46294cc70f55b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50206348"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>ASP.NET core'da denetleyicilere bağımlılık ekleme

<a name="dependency-injection-controllers"></a>

Tarafından [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC denetleyicileri bağımlılıklarını oluşturucuları aracılığıyla açıkça istemeniz gerekir. Bazı durumlarda, bir hizmet ayrı denetleyicisinin Eylemler gerekebilir ve bu denetleyici düzeyinde istemek için anlamlı olmayabilir. Bu durumda, eylem yöntemine bir parametre olarak bir hizmet ekleme de seçebilirsiniz.

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))

## <a name="dependency-injection"></a>Bağımlılık ekleme

Bağımlılık ekleme, izleyen bir teknik [bağımlılık tersine çevirme ilkesine](http://deviq.com/dependency-inversion-principle/), gevşek modüllerinin oluşturulması uygulamalara izin verme. ASP.NET Core için yerleşik desteği vardır [bağımlılık ekleme](../../fundamentals/dependency-injection.md), getiren uygulamaları test etmek ve sürdürmek daha kolay.

## <a name="constructor-injection"></a>Oluşturucu ekleme

Oluşturucu tabanlı bağımlılık ekleme için ASP.NET Core'nın yerleşik destek, MVC denetleyicileri genişletir. Yalnızca bir hizmet türü bir oluşturucu parametresi olarak denetleyicisine ekleyerek, ASP.NET Core yerleşik hizmet kapsayıcısında kullanarak bu tür çözümlemeyi dener. Hizmetleri genellikle, ancak her zaman, arabirimler kullanılarak tanımlanır. Örneğin, geçerli zamanı bağlıdır iş mantığı uygulamanız varsa, testlerinizi bir süre kullanan uygulamalar geçmesine izin saati (yerine sabit kodlama), alan bir hizmeti ekleyebilir.

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]


Bunun gibi arabirimi çalışma zamanında sistem saatini kullanır, böylece uygulama kısmı oldukça kolaydır:

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]


Bu yerinde hizmet denetleyici kullanabiliriz. Bu durumda, bazı mantığını ekledik `HomeController` `Index` yöntemi bir karşılama kullanıcıya göstermek için günün saatini temel alan.

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]

Biz uygulamayı şimdi çalıştırırsanız, biz büyük olasılıkla bir hatayla karşılaşırsınız:

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

Bir hizmet olarak yapılandırmadıysanız bu hata oluşur `ConfigureServices` yönteminde bizim `Startup` sınıfı. Yönelik isteklere belirtmek için `IDateTime` örneği kullanılarak çözülmesi gerektiğini `SystemDateTime`, vurgulanan satırın altındaki listeye ekleyin, `ConfigureServices` yöntemi:

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]

> [!NOTE]
> Bu belirli hizmet birkaç farklı ömrü seçeneklerden herhangi biri kullanılarak uygulanabilir (`Transient`, `Scoped`, veya `Singleton`). Bkz: [bağımlılık ekleme](../../fundamentals/dependency-injection.md) bu kapsam seçeneklerin her biri, bir hizmet davranışını nasıl etkileyeceğini anlamak için.

Hizmet yapılandırıldıktan sonra uygulamayı çalıştıran ve giriş sayfasına giderek zamana bağlı ileti beklendiği gibi görüntülenmelidir:

![Sunucu karşılaması](dependency-injection/_static/server-greeting.png)

>[!TIP]
> Bkz: [Test denetleyicisi mantığı](testing.md) açık şekilde istek bağımlılıkları öğrenmek için [ http://deviq.com/explicit-dependencies-principle/ ](http://deviq.com/explicit-dependencies-principle/) denetleyicileri kodu test etmek daha kolay hale getirir.

ASP.NET Core'nın yerleşik bağımlılık ekleme sınıfları Hizmetleri istemek için yalnızca tek bir oluşturucu sahip destekler. Birden fazla Oluşturucu varsa belirten bir durum karşılaşabilirsiniz:

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

Hata iletisinde gibi tek bir kurucu kullanarak bu sorunu düzeltebilirsiniz. Ayrıca [varsayılan bağımlılık ekleme kapsayıcısını üçüncü taraf bir uygulama ile değiştirin](xref:fundamentals/dependency-injection#default-service-container-replacement)birden çok Oluşturucu destekleyen çoğu.

## <a name="action-injection-with-fromservices"></a>FromServices ile eylemi ekleme

Bazen denetleyicinizin içinde bir hizmet için birden fazla eylem gerekmez. Bu durumda, bu eylem yöntemine bir parametre olarak hizmet ekleme mantıklı olabilir. Bu öznitelik ile parametre olarak işaretleyerek yapılır `[FromServices]` burada gösterildiği gibi:

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]

## <a name="accessing-settings-from-a-controller"></a>Bir denetleyiciden ayarlarına erişme

Bir denetleyici içinde gelen uygulama veya yapılandırma ayarlarına erişme ortak bir desendir. Bu erişim açıklanan seçenekler deseni kullanması gereken [yapılandırma](xref:fundamentals/configuration/index). Bağımlılık ekleme kullanılarak doğrudan denetleyicinizden ayarları genellikle istek olmamalıdır. Daha iyi bir yaklaşım isteği bir `IOptions<T>` örneği burada `T` ihtiyacınız yapılandırma sınıftır.

Seçenekleri Desen ile çalışmak için bu gibi seçenekleri temsil eden bir sınıf oluşturmanız gerekir:

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]

Seçenekleri modeli kullanır ve yapılandırma sınıfınıza Hizmetleri koleksiyona eklemek için uygulamayı yapılandırma ayarını yapmanız gerekir `ConfigureServices`:

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]

> [!NOTE]
> Yukarıdaki listenin biz JSON biçimli bir dosyadan ayarları okumak için uygulamayı yapılandırdığınızı varsayalım. Tamamen kodunda yukarıdaki açıklamalı kodda gösterildiği gibi ayarları yapılandırabilirsiniz. Bkz: [yapılandırma](xref:fundamentals/configuration/index) daha fazla yapılandırma seçeneği.

Bir türü kesin belirlenmiş bir yapılandırma nesnesi, belirttiğiniz sonra (Bu durumda, `SampleWebSettings`) ve eklemiş Hizmetleri koleksiyonuna, onu bir denetleyici veya eylem yönteminden bir örneğini isteyerek talep edebilir `IOptions<T>` (Bu durumda, `IOptions<SampleWebSettings>`) . Aşağıdaki kod nasıl bir denetleyiciden ayarları istemek gösterir:

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]

Seçenekleri düzenini izleyerek birbirlerinden ölçeklendirilebilmeleri ayarlarını ve yapılandırmasını sağlar ve denetleyici takip sağlar [görev ayrımı nettir](http://deviq.com/separation-of-concerns/), burada veya nasıl bilmeniz gerekmez bu yana ayarları bulunamıyor bilgiler. Ayrıca denetleyicisi birim testi kolaylaştırır [Test denetleyicisi mantığı](testing.md), olduğundan hiçbir [statik cling](http://deviq.com/static-cling/) veya doğrudan örneğinin ayarları sınıfları için denetleyici sınıfı.
