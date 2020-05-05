---
title: ASP.NET Core filtreler
author: Rick-Anderson
description: Filtrelerin nasıl çalıştığını ve ASP.NET Core nasıl kullanılacağını öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 7272e05b408ac6f8daeda586c6f40fcc5bd1f6eb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776792"
---
# <a name="filters-in-aspnet-core"></a>ASP.NET Core filtreler

::: moniker range=">= aspnetcore-3.0"

[Kirk Larkabağı](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından

ASP.NET Core *Filtreler* , istek işleme ardışık düzeninde belirli aşamalardan önce veya sonra kod çalıştırılmasına izin verir.

Yerleşik Filtreler şunları gibi görevleri işler:

* Yetkilendirme (kullanıcının yetkili olmadığı kaynaklara erişimi önler).
* Yanıt önbelleğe alma (istek ardışık düzenini önbelleğe alınmış bir yanıt döndürecek şekilde döndürür).

Çapraz kesme sorunlarını işlemek için özel filtreler oluşturulabilir. Çapraz kesme sorunlarına örnek olarak hata işleme, önbelleğe alma, yapılandırma, yetkilendirme ve günlüğe kaydetme dahildir.  Filtreler kodu çoğaltmaktan kaçının. Örneğin, bir hata işleme özel durum filtresi hata işlemeyi birleştirebilir.

Bu belge, görünümler içeren Razor Pages, API denetleyicileri ve denetleyiciler için geçerlidir. Filtreler, [Razor bileşenleriyle](xref:blazor/components)doğrudan çalışmaz. Filtre, şu durumlarda bir bileşeni yalnızca dolaylı olarak etkileyebilir:

* Bileşen bir sayfa veya görünüme katıştırılır.
* Sayfa veya denetleyici/görünüm filtreyi kullanır.

Örneği ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) .

## <a name="how-filters-work"></a>Filtreler nasıl çalışır?

Filtreler, bazen *Filtre işlem hattı*olarak da adlandırılan *ASP.NET Core eylemi çağırma işlem hattı*içinde çalışır. Filtre işlem hattı çalıştırılacak eylemi ASP.NET Core seçtikten sonra çalışır.

![İstek diğer ara yazılım, yönlendirme ara yazılımı, eylem seçimi ve eylem çağırma Işlem hattı aracılığıyla işlenir. İstek işleme, istemciye gönderilen bir yanıt olmadan önce eylem seçimi, yönlendirme ara yazılımı ve diğer diğer ara yazılım aracılığıyla yeniden devam eder.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Filtre türleri

Her filtre türü, filtre ardışık düzeninde farklı bir aşamada yürütülür:

* İlk olarak [Yetkilendirme filtreleri](#authorization-filters) çalışır ve kullanıcının istek için yetkilendirilip yetkilendirilmediğini tespit etmek için kullanılır. Yetkilendirme filtreleri, istek yetkilendirilmezse işlem hattı kısa devre dışı olur.

* [Kaynak filtreleri](#resource-filters):

  * Yetkilendirmeden sonra çalıştırın.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>Filtre işlem hattının geri kalanında kodu çalıştırır. Örneğin, `OnResourceExecuting` model bağlamadan önce kodu çalıştırır.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>ardışık düzenin geri kalanı tamamlandıktan sonra kodu çalıştırır.

* [Eylem filtreleri](#action-filters):

  * Kodu bir eylem yöntemi çağrıldıktan hemen önce ve sonra çalıştırın.
  * Bir eyleme geçirilen bağımsız değişkenleri değiştirebilir.
  * Eylemden döndürülen sonucu değiştirebilir.
  * Razor Pages **desteklenmez.**

* [Özel durum filtreleri](#exception-filters) , yanıt gövdesinin üzerine yazılmadan önce oluşan işlenmemiş özel durumlara genel ilkeler uygular.

* [Sonuç filtreleri](#result-filters) , eylem sonuçlarının yürütülmesinden hemen önce ve sonra kodu çalıştırır. Bunlar yalnızca eylem yöntemi başarıyla yürütüldüğünde çalışır. Bu değerler, görünüm veya biçimlendirici yürütmesinin yürütülmesi gereken mantık için faydalıdır.

Aşağıdaki diyagramda filtre türlerinin filtre ardışık düzeninde nasıl etkileşimde bulunduğu gösterilmektedir.

![İstek, Yetkilendirme filtreleri, kaynak filtreleri, model bağlama, eylem filtreleri, eylem yürütme ve eylem sonucu dönüştürme, özel durum filtreleri, sonuç filtreleri ve sonuç yürütmesi aracılığıyla işlenir. Bu şekilde, istek yalnızca istemciye gönderilen yanıt haline gelmeden önce sonuç filtreleri ve kaynak filtreleri tarafından işlenir.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Uygulama

Filtreler, farklı arabirim tanımları aracılığıyla hem zaman uyumlu hem de zaman uyumsuz uygulamaları destekler.

Zaman uyumlu filtreler, ardışık düzen aşamasından önce ve sonra kodu çalıştırır. Örneğin, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> eylem yöntemi çağrılmadan önce çağrılır. <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>, eylem yöntemi döndüğünde çağrılır.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

Zaman uyumsuz filtreler bir `On-Stage-ExecutionAsync` yöntemi tanımlar. Örneğin <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

Önceki kodda,, `SampleAsyncActionFilter` Action metodunu yürüten bir <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) içerir.

### <a name="multiple-filter-stages"></a>Birden çok filtre aşaması

Birden çok filtre aşaması için arabirimler tek bir sınıfta uygulanabilir. Örneğin, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> sınıfı şunları uygular:

* Zaman uyumlu <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> : ve<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>
* Zaman uyumsuz <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> : ve<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

Her ikisini de **değil** , bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.** Çalışma zamanı öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır. Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır. Tek bir sınıfta hem zaman uyumsuz hem de zaman uyumlu arabirimler uygulanmışsa, yalnızca zaman uyumsuz yöntem çağrılır. Gibi <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>soyut sınıflar kullanırken, her bir filtre türü için yalnızca zaman uyumlu yöntemleri veya zaman uyumsuz yöntemi geçersiz kılın.

### <a name="built-in-filter-attributes"></a>Yerleşik filtre öznitelikleri

ASP.NET Core, alt sınıflanmış ve özelleştirilebilen yerleşik öznitelik tabanlı filtreler içerir. Örneğin, aşağıdaki sonuç filtresi yanıta bir üst bilgi ekler:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Öznitelikler, önceki örnekte gösterildiği gibi, filtrelerin bağımsız değişkenleri kabul etmesine izin verir. `AddHeaderAttribute` ' İ bir denetleyiciye veya eylem yöntemine UYGULAYıN ve http üstbilgisinin adını ve değerini belirtin:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

Üst bilgileri incelemek için [tarayıcı geliştirici araçları](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) gibi bir araç kullanın. **Yanıt üst bilgileri** `author: Rick Anderson` altında görüntülenir.

Aşağıdaki kod şunları uygular `ActionFilterAttribute` :

* Yapılandırma sistemindeki başlığı ve adı okur. Önceki örnekten farklı olarak, aşağıdaki kod koda filtre parametrelerinin eklenmesine gerek yoktur.
* Başlık ve adı yanıt üstbilgisine ekler.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

Yapılandırma seçenekleri, [Seçenekler deseninin](xref:fundamentals/configuration/options)kullanıldığı [yapılandırma sisteminden](xref:fundamentals/configuration/index) sağlanır. Örneğin, *appSettings. JSON* dosyasından:

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

İçinde `StartUp.ConfigureServices`:

* `PositionOptions` Sınıfı, `"Position"` yapılandırma alanı ile hizmet kapsayıcısına eklenir.
* , `MyActionFilterAttribute` Hizmet kapsayıcısına eklenir.

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

Aşağıdaki kod, `PositionOptions` sınıfını göstermektedir:

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

Aşağıdaki kod `MyActionFilterAttribute` `Index2` yöntemi için geçerlidir:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

**Yanıt üst bilgileri**altında `author: Rick Anderson`, ve `Editor: Joe Smith` `Sample/Index2` uç nokta çağrıldığında görüntülenir.

Aşağıdaki kod, `MyActionFilterAttribute` ve ' i `AddHeaderAttribute` Razor sayfasına uygular:

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

, Razor sayfası işleyici yöntemlerine filtre uygulanamaz. Bu kişiler Razor sayfa modeline veya küresel olarak uygulanabilir.

Filtre arabirimlerinden birkaçı, özel uygulamalar için temel sınıflar olarak kullanılabilecek karşılık gelen özniteliklere sahiptir.

Filtre öznitelikleri:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Kapsamları ve yürütme sırasını filtrele

Üç *kapsamından*birindeki işlem hattına bir filtre eklenebilir:

* Bir denetleyici eyleminde bir özniteliği kullanma. Filtre öznitelikleri Razor Pages işleyici yöntemlerine uygulanamaz.
* Bir denetleyici veya Razor sayfasında bir özniteliği kullanma.
* Genel olarak tüm denetleyiciler, Eylemler ve Razor Pages aşağıdaki kodda gösterildiği gibi:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a>Varsayılan yürütme sırası

İşlem hattının belirli bir aşamasına ilişkin birden çok filtre olduğunda, kapsam varsayılan filtre yürütme sırasını belirler.  Genel filtreler kapsayan sınıf filtreleri, bu da kapsayan Yöntem filtreleri.

Filtre iç içe geçme sonucu *olarak, filtrenin kodu,* *önceki* kodun ters sırasına göre çalışır. Filtre sırası:

* Genel filtrelerin *önceki* kodu.
  * Denetleyicinin ve Razor sayfası filtrelerinin *öncesindeki* kodu.
    * Eylem yöntemi filtrelerinden *önceki* kod.
    * Eylem yöntemi filtrelerinden *sonraki* kod.
  * Denetleyicinin ve Razor sayfası filtrelerinin *sonraki* kodu.
* Genel filtrelerin *sonraki* kodu.
  
Zaman uyumlu eylem filtreleri için filtre yöntemlerinin çağrıldığı sırayı gösteren aşağıdaki örnek.

| Sequence | Filtre kapsamı | Filter yöntemi |
|:--------:|:------------:|:-------------:|
| 1 | Genel | `OnActionExecuting` |
| 2 | Denetleyici veya Razor sayfası| `OnActionExecuting` |
| 3 | Yöntem | `OnActionExecuting` |
| 4 | Yöntem | `OnActionExecuted` |
| 5 | Denetleyici veya Razor sayfası | `OnActionExecuted` |
| 6 | Genel | `OnActionExecuted` |

### <a name="controller-level-filters"></a>Denetleyici düzeyi filtreleri

<xref:Microsoft.AspNetCore.Mvc.Controller> Temel sınıftan devralan her denetleyici [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. onactionexecutionasync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)ve [Controller. onactionyürütülmüş](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` yöntemlerini içerir. Bu Yöntemler:

* Belirli bir eylem için çalışan filtreleri sarın.
* `OnActionExecuting`, eylemin filtrelerinden herhangi birinin önüne çağırılır.
* `OnActionExecuted`tüm eylem filtrelerinden sonra çağrılır.
* `OnActionExecutionAsync`, eylemin filtrelerinden herhangi birinin önüne çağırılır. Eylem yöntemiyle çalıştıktan sonra `next` filtredeki kod.

Örneğin, indirme örneğinde, `MySampleActionFilter` başlangıçta genel olarak uygulanır.

`TestController`Şunları yapın:

* `FilterTest2` Eyleme `SampleActionFilterAttribute` (`[SampleActionFilter]`) uygular.
* Geçersiz `OnActionExecuting` kılmalar `OnActionExecuted`ve.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

' A `https://localhost:5001/Test2/FilterTest2` gitme aşağıdaki kodu çalıştırır:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Denetleyici düzeyi filtreleri [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) özelliğini olarak `int.MinValue`ayarlar. Denetleyici düzeyi filtreleri metotlara uygulandıktan sonra çalıştırılacak şekilde ayarlanamaz. **not** Sıra, sonraki bölümde açıklanmaktadır.

Razor Pages için bkz. [filtre yöntemlerini geçersiz kılarak Razor sayfası filtrelerini uygulama](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Varsayılan sırayı geçersiz kılma

Varsayılan yürütme sırası, uygulama <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>tarafından geçersiz kılınabilir. `IOrderedFilter`yürütme sırasını <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> tespit etmek için kapsama göre öncelik alan özelliği gösterir. Düşük `Order` değere sahip bir filtre:

* Daha yüksek değeri olan bir filtreden önce koddan *önce* kodu çalıştırır `Order`.
* Daha yüksek *after* `Order` bir değere sahip bir filtrenin sonrasında koddan sonra çalışır.

`Order` Özelliği bir Oluşturucu parametresiyle ayarlanır:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

Aşağıdaki denetleyicide iki eylem filtresini göz önünde bulundurun:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

İçinde `StartUp.ConfigureServices`genel bir filtre eklenir:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

3 filtre aşağıdaki sırayla çalışır:

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

`Order` Özelliği filtrelerin çalıştırılacağı sıra belirlenirken kapsamı geçersiz kılar. Filtreler sırasıyla sıraya göre sıralanır, ardından kapsam bölmek için kullanılır. Yerleşik filtrelerin hepsi uygular `IOrderedFilter` ve varsayılan `Order` değeri 0 olarak ayarlar. Daha önce belirtildiği gibi, denetleyici düzeyi filtreleri [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) özelliğini yerleşik filtreler `int.MinValue` için olarak ayarlar, sıfır olmayan bir değere ayarlanmadığı sürece `Order` kapsam sıralamayı belirler.

Önceki kodda, `MySampleActionFilter` denetleyici kapsamı olan, daha önce `MyAction2FilterAttribute`çalışması için genel kapsama sahiptir. Önce `MyAction2FilterAttribute` çalıştırmak için, sırayı şu şekilde `int.MinValue`ayarlayın:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

Önce genel filtrenin `MySampleActionFilter` çalışmasını sağlamak için şu şekilde `Order` `int.MinValue`ayarlayın:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a>İptal ve kısa devre dışı

Filtre işlem hattı, filtre yöntemine sunulan <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametrede özelliği ayarlanarak kısa devre yapılabilir. Örneğin, aşağıdaki kaynak filtresi, ardışık düzenin geri kalanının yürütülmesini engeller:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

Aşağıdaki kodda, `ShortCircuitingResourceFilter` ve `AddHeader` filtresi `SomeResource` Action metodunu hedefleyin. `ShortCircuitingResourceFilter`Şunları yapın:

* İlk olarak bir kaynak filtresi olduğundan ve `AddHeader` bir eylem filtresiyle çalışır.
* Kısa süreli işlem hattının geri kalanı.

Bu nedenle `AddHeader` , filtre `SomeResource` eylem için hiçbir şekilde çalışmayacaktır. Bu davranış, her iki filtre de eylem yöntemi düzeyinde uygulanırsa, ilk olarak `ShortCircuitingResourceFilter` çalıştırıldığında aynı olur. İlk `ShortCircuitingResourceFilter` olarak, filtre türü veya açıkça `Order` özelliğin kullanımı kullanılarak çalışır.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a>Bağımlılık ekleme

Filtreler türe veya örneğe göre eklenebilir. Bir örnek eklenirse, bu örnek her istek için kullanılır. Bir tür eklenirse, türü etkinleştirilmiş olur. Tür etkinleştirilmiş bir filtre şu anlama gelir:

* Her istek için bir örnek oluşturulur.
* Herhangi bir Oluşturucu bağımlılığı [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından doldurulur.

Öznitelik olarak uygulanan ve doğrudan denetleyici sınıflarına veya eylem yöntemlerine eklenen filtreler [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından sağlanmış Oluşturucu bağımlılıklarına sahip olamaz. Oluşturucu bağımlılıkları şu nedenle şu nedenle sağlanamaz:

* Özniteliklerin, uygulandıkları yerlerde, Oluşturucu parametreleri sağlanmalıdır. 
* Bu, özniteliklerin nasıl çalıştığı konusunda bir kısıtlamadır.

Aşağıdaki filtreler, dı tarafından belirtilen Oluşturucu bağımlılıklarını destekler:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>özniteliği üzerinde uygulandı.

Önceki filtreler bir denetleyiciye veya eylem yöntemine uygulanabilir:

Günlükçüler, DI 'den alınabilir. Ancak, yalnızca günlüğe kaydetme amacıyla filtre oluşturmaktan ve kullanmaktan kaçının. [Yerleşik çerçeve günlüğü](xref:fundamentals/logging/index) genellikle günlüğe kaydetme için gerekenleri sağlar. Filtrelere günlük eklendi:

* , İş etki alanı kaygılarını veya filtreye özgü davranışları odaklamalıdır.
* Eylemleri veya diğer çerçeve olaylarını günlüğe **içermemelidir** . Yerleşik filtreler günlük eylemleri ve çerçeve olayları.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Hizmet filtresi uygulama türleri ' de `ConfigureServices`kaydedilir. Bir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> filtrenin bir örneğini alır.

Aşağıdaki kod şunu gösterir `AddHeaderResultServiceFilter`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Aşağıdaki kodda, `AddHeaderResultServiceFilter` dı kapsayıcısına eklenmiştir:

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

Aşağıdaki kodda, `ServiceFilter` özniteliği, bir `AddHeaderResultServiceFilter` filtrenin bir örneğini dı:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Kullanırken `ServiceFilterAttribute`, [servicefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)olarak ayarlanıyor:

* Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden *kullanılabilir olabileceğini gösteren* bir ipucu sağlar. ASP.NET Core çalışma zamanı garanti etmez:

  * Filtrenin tek bir örneğinin oluşturulması gerekir.
  * Filtre, sonraki bir noktada dı kapsayıcısından yeniden istenmeyecek.

* Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> örnek oluşturma <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> yöntemini gösterir. `CreateInstance`belirtilen türü DI 'dan yükler.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>benzerdir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ancak türü doğrudan dı kapsayıcısından çözümlenmez. Kullanarak <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>türü başlatır.

Türler `TypeFilterAttribute` doğrudan dı kapsayıcısından çözümlenmediğinden:

* ' İ kullanılarak başvurulan türlerin, `TypeFilterAttribute` dı kapsayıcısına kayıtlı olması gerekmez.  Bunların bağımlılıkları, dı kapsayıcısı tarafından yerine getirilir.
* `TypeFilterAttribute`isteğe bağlı olarak tür için Oluşturucu bağımsız değişkenlerini kabul edebilir.

Kullanırken `TypeFilterAttribute`, [typefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)olarak ayarlanıyor:
* Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden kullanılabilir *olabileceği* ipucu sağlar. ASP.NET Core çalışma zamanı, filtrenin tek bir örneğinin oluşturulacağı garantisi vermez.

* Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.

Aşağıdaki örnek kullanarak `TypeFilterAttribute`bir türe bağımsız değişkenlerin nasıl geçirileceğini göstermektedir:

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Yetkilendirme filtreleri

Yetkilendirme filtreleri:

* , Filtre ardışık düzeninde ilk filtrelerin çalıştırılmasıyla sonuçlanır.
* Eylem yöntemlerine erişimi denetleyin.
* Bir Before yöntemi, ancak After yönteminden hiçbiri.

Özel Yetkilendirme filtreleri özel bir yetkilendirme çerçevesi gerektirir. Özel bir filtre yazmak için yetkilendirme ilkelerini yapılandırmayı veya özel bir yetkilendirme ilkesi yazmayı tercih edin. Yerleşik yetkilendirme filtresi:

* Yetkilendirme sistemini çağırır.
* İstekleri yetkilendirmez.

Yetkilendirme filtreleri içinde özel **durumlar atamayın:**

* Özel durum işlenmeyecektir.
* Özel durum filtreleri özel durumu işleymeyecektir.

Bir yetkilendirme filtresinde özel durum oluştuğunda bir sınama vermeyi düşünün.

[Yetkilendirme](xref:security/authorization/introduction)hakkında daha fazla bilgi edinin.

## <a name="resource-filters"></a>Kaynak filtreleri

Kaynak filtreleri:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> Ya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> da arabirimini uygulayın.
* Yürütme, filtre işlem hattının çoğunu sarmalar.
* Kaynak filtrelerinden önce yalnızca [Yetkilendirme filtreleri](#authorization-filters) çalışır.

Kaynak filtreleri, işlem hattının büyük bir yanındaki kısa devre için faydalıdır. Örneğin, bir önbelleğe alma filtresi, bir önbellek isabetinden ardışık düzen geri kalanından kaçınabilir.

Kaynak filtresi örnekleri:

* Daha önce gösterilen [kısa devre dışı kaynak filtresi](#short-circuiting-resource-filter) .
* [Disableformvaluemodelbindingattribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Model bağlamanın form verilerine erişimini engeller.
  * Form verilerinin belleğe okunmasını engellemek için büyük dosya yüklemeleri için kullanılır.

## <a name="action-filters"></a>Eylem filtreleri

Eylem filtreleri Razor Pages **için uygulanmaz.** Razor Pages ve <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> destekler. Daha fazla bilgi için bkz. [Razor Pages Için filtre yöntemleri](xref:razor-pages/filter).

Eylem filtreleri:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> Ya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> da arabirimini uygulayın.
* Yürütmesinin, eylem yöntemlerinin yürütülmesi çevreler.

Aşağıdaki kod bir örnek eylem filtresi gösterir:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Aşağıdaki özellikleri sağlar:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-girişlerin bir eylem yöntemine okunmasını sağlar.
* <xref:Microsoft.AspNetCore.Mvc.Controller>-denetleyici örneğinin işlenmesine izin vermez.
* <xref:System.Web.Mvc.ActionExecutingContext.Result>-Eylem `Result` yönteminin ve sonraki eylem filtrelerinin kısa devre dışı yürütülmesi ayarlanıyor.

Eylem yönteminde özel durum oluşturma:

* Sonraki filtrelerin çalıştırılmasını önler.
* Ayarından `Result`farklı olarak, başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.

, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> Ve `Controller` `Result` ek olarak aşağıdaki özellikleri sağlar:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled>-Eylem yürütmesi başka bir filtre tarafından kabul edilse true.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception>-Eylem veya daha önce çalıştırılan eylem filtresi bir özel durum oluşturdu-null değil. Bu özellik null olarak ayarlanıyor:

  * Özel durumu etkin bir şekilde işler.
  * `Result`eylem yönteminden döndürülmüş gibi yürütülür.

Bir `IAsyncActionFilter`için bir çağrısı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:

* Sonraki eylem filtrelerini ve eylem yöntemini yürütür.
* `ActionExecutedContext` döndürür.

Kısa devre dışı, bir sonuç <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> örneğine atayın ve çağırmayın `next` ( `ActionExecutionDelegate`).

Çerçeve, alt sınıflı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> olabilecek bir Özet sağlar.

`OnActionExecuting` Eylem filtresi şu şekilde kullanılabilir:

* Model durumunu doğrulayın.
* Durum geçersizse bir hata döndürür.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

`OnActionExecuted` Yöntemi eylem yönteminden sonra çalışır:

* Ve, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> özelliği aracılığıyla eylemin sonuçlarını görebilir ve değiştirebilir.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>eylem yürütmesi başka bir filtre tarafından kabul edilse, true olarak ayarlanır.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>eylem veya sonraki bir eylem filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır. Null `Exception` olarak ayarlanıyor:

  * Bir özel durumu etkin bir şekilde işler.
  * `ActionExecutedContext.Result`eylem yönteminden normal olarak döndürülmüş gibi yürütülür.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Özel durum filtreleri

Özel durum filtreleri:

* Veya <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>uygulayın.
* , Yaygın hata işleme ilkelerini uygulamak için kullanılabilir.

Aşağıdaki örnek özel durum filtresi, uygulama geliştirmede olduğunda oluşan özel durumlar hakkındaki ayrıntıları görüntülemek için özel bir hata görünümü kullanır:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Aşağıdaki kod özel durum filtresini sınar:

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

Özel durum filtreleri:

* Etkinlikden önceki ve sonraki olaylar yok.
* Veya <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>uygulayın.
* Razor sayfası veya denetleyici oluşturma, [model bağlama](xref:mvc/models/model-binding), eylem filtreleri veya eylem yöntemlerinde oluşan işlenmemiş özel durumları işleyin.
* Kaynak filtrelerinde, sonuç filtrelerinde veya MVC sonuç yürütülürken oluşan özel **durumları yakalamayın** .

Bir özel durumu işlemek için, <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> özelliğini bir yanıt `true` olarak ayarlayın veya yazın. Bu, özel durumun yayılmasını engeller. Özel durum filtresi bir özel durumu "başarılı" olarak açamaz. Yalnızca bir eylem filtresi bunu yapabilir.

Özel durum filtreleri:

* Eylemler içinde oluşan özel durumları yakalamaya uygundur.
* , Hata işleme ara yazılımı olarak esnek değildir.

Özel durum işleme için ara yazılımı tercih edin. Yalnızca hata işlemenin hangi eylem yöntemine göre *farklılık* gösteren özel durum filtrelerini kullanın. Örneğin, bir uygulama hem API uç noktaları hem de görünümler/HTML için eylem yöntemlerine sahip olabilir. API uç noktaları, hata bilgilerini JSON olarak döndürebilir, ancak görünüm tabanlı eylemler bir hata sayfasını HTML olarak döndürebilir.

## <a name="result-filters"></a>Sonuç filtreleri

Sonuç filtreleri:

* Arabirim uygulama:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Yürütmesi, eylem sonuçlarının yürütülmesini çevreler.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter ve ıasyncresultfilter

Aşağıdaki kod, bir HTTP üst bilgisi ekleyen bir sonuç filtresi gösterir:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Yürütülen sonuç türü eyleme göre değişir. Bir görünüm döndüren bir eylem, <xref:Microsoft.AspNetCore.Mvc.ViewResult> çalıştırılmakta olan bir parçası olarak tüm Razor işlemlerini içerir. Bir API yöntemi, sonucun yürütülmesinin bir parçası olarak bazı serileştirme işlemleri gerçekleştirebilir. [Eylem sonuçları](xref:mvc/controllers/actions)hakkında daha fazla bilgi edinin.

Sonuç filtreleri yalnızca bir eylem veya eylem filtresi bir eylem sonucu üretirse yürütülür. Şu durumlarda sonuç filtreleri yürütülmez:

* Bir yetkilendirme filtresi veya kaynak filtresi, işlem hattı için kısa süreli olarak devre dışı.
* Bir özel durum filtresi, bir eylem sonucu üreterek özel durumu işler.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> Yöntemi, olarak <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true`ayarlanarak eylem sonucunun ve sonraki sonuç filtrelerinin kısa devre yürütülmesine neden olabilir. Boş bir yanıt oluşturmamaya kaçınmak için kısa devre dışı bırakıldığında yanıt nesnesine yazın. İçinde `IResultFilter.OnResultExecuting`bir özel durum üretiliyor:

* Eylem sonucunun ve sonraki filtrelerin yürütülmesini önler.
* Başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> Yöntem çalıştığında, yanıt muhtemelen istemciye zaten gönderilmiştir. Yanıt istemciye zaten gönderildiyse, bu değiştirilemez.

`ResultExecutedContext.Canceled`eylem sonucu yürütmesi `true` başka bir filtre tarafından kabul edilen ise olarak ayarlanır.

`ResultExecutedContext.Exception`eylem sonucu veya sonraki sonuç filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır. Null `Exception` olarak ayarlanması bir özel durumu işler ve sonra işlem hattının daha sonra yeniden oluşturulmasını engeller. Bir sonuç filtresinde özel durum işlenirken yanıta veri yazmanın güvenilir bir yolu yoktur. Bir eylem sonucu bir özel durum oluşturduğunda üstbilgiler istemciye temizleniyorsa, hata kodu göndermek için güvenilir bir mekanizma yoktur.

Bir <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>için bir çağrısı `await next` , sonraki sonuç filtrelerini <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ve eylem sonucunu yürütür. Kısa devre dışı, [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) olarak ayarlayın `true` ve şunu çağırmayın `ResultExecutionDelegate`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

Çerçeve, alt sınıflı `ResultFilterAttribute` olabilecek bir Özet sağlar. Daha önce gösterilen [Addheaderattribute](#add-header-attribute) sınıfı bir sonuç Filtresi özniteliği örneğidir.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>Ialwaysrunresultfilter ve ıasyncalwaysrunresultfilter

Ve <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> arabirimleri, tüm eylem <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> sonuçları için çalışan bir uygulama bildirir. Bu, tarafından oluşturulan eylem sonuçlarını içerir:

* Kısa devre olan Yetkilendirme filtreleri ve kaynak filtreleri.
* Özel durum filtreleri.

Örneğin, aşağıdaki filtre her zaman çalışır ve içerik anlaşması başarısız olduğunda<xref:Microsoft.AspNetCore.Mvc.ObjectResult> *422 olmayan bir varlık* durum kodu ile bir eylem sonucu () ayarlar:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Bu nedenle, `IFilterFactory` bir örnek, filtre ardışık düzeninde `IFilterMetadata` herhangi bir yerde örnek olarak kullanılabilir. Çalışma zamanı filtreyi çağırmayı hazırlarken, bir `IFilterFactory`öğesine dönüştürmeyi dener. Atama başarılı olursa, çağrılan <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> `IFilterMetadata` örneği oluşturmak için yöntemi çağırılır. Bu, tam filtre işlem hattının uygulama başladığında açıkça ayarlanması gerektiğinden esnek bir tasarım sağlar.

`IFilterFactory`, filtre oluşturmaya yönelik başka bir yaklaşım olarak özel öznitelik uygulamaları kullanılarak uygulanabilir:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

Filtre aşağıdaki kodda uygulanır:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

Önceki kodu [indirme örneğini](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)çalıştırarak test edin:

* F12 geliştirici araçlarını çağırın.
* `https://localhost:5001/Sample/HeaderWithFactory` sayfasına gidin.

F12 geliştirici araçları, örnek kod tarafından eklenen aşağıdaki yanıt üstbilgilerini görüntüler:

* **Yazar:**`Rick Anderson`
* **globaladdheader:**`Result filter added to MvcOptions.Filters`
* **iç:**`My header`

Yukarıdaki kod, **iç:** `My header` yanıt üst bilgisini oluşturur.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>Öznitelik üzerinde IFilterFactory uygulandı

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Uygulayan `IFilterFactory` filtreler şu filtreler için yararlıdır:

* Parametre geçirme gerekmez.
* DI tarafından doldurulması gereken Oluşturucu bağımlılıkları vardır.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> örnek oluşturma <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> yöntemini gösterir. `CreateInstance`belirtilen türü hizmetler kapsayıcısından (dı) yükler.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Aşağıdaki kod, `[SampleActionFilter]`uygulamak için üç yaklaşımda gösterilmiştir:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

Yukarıdaki kodda, yöntemi ile `[SampleActionFilter]` dekorasyon, uygulamak için tercih edilen yaklaşımdır. `SampleActionFilter`

## <a name="using-middleware-in-the-filter-pipeline"></a>Filtre ardışık düzeninde ara yazılım kullanma

Kaynak filtreleri, işlem hattında daha sonra gelen her şeyin yürütülmesini çevreleyecek olan [Ara yazılım](xref:fundamentals/middleware/index) gibi çalışır. Ancak filtreler, çalışma zamanının bir parçası oldukları ve bu sayede bağlam ve yapılara erişimi olan ara yazılımlar farklıdır.

Ara yazılımı bir filtre olarak kullanmak için, filtre ardışık düzenine eklenecek `Configure` olan ara yazılımı belirten bir yöntemi olan bir tür oluşturun. Aşağıdaki örnek, bir istek için geçerli kültürü oluşturmak üzere yerelleştirme ara yazılımını kullanır:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Ara yazılımını <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> çalıştırmak için kullanın:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Ara yazılım filtreleri, filtre işlem hattının aynı aşamasında, model bağlamadan önce ve işlem hattının geri kalanı ile kaynak filtreleri olarak çalışır.

## <a name="next-actions"></a>Sonraki eylemler

* [Razor Pages Için filtre yöntemlerine](xref:razor-pages/filter)bakın.
* Filtrelerle denemek için [GitHub örneğini indirin, test edin ve değiştirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Kirk Larkabağı](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından

ASP.NET Core *Filtreler* , istek işleme ardışık düzeninde belirli aşamalardan önce veya sonra kod çalıştırılmasına izin verir.

Yerleşik Filtreler şunları gibi görevleri işler:

* Yetkilendirme (kullanıcının yetkili olmadığı kaynaklara erişimi önler).
* Yanıt önbelleğe alma (istek ardışık düzenini önbelleğe alınmış bir yanıt döndürecek şekilde döndürür).

Çapraz kesme sorunlarını işlemek için özel filtreler oluşturulabilir. Çapraz kesme sorunlarına örnek olarak hata işleme, önbelleğe alma, yapılandırma, yetkilendirme ve günlüğe kaydetme dahildir.  Filtreler kodu çoğaltmaktan kaçının. Örneğin, bir hata işleme özel durum filtresi hata işlemeyi birleştirebilir.

Bu belge, görünümler içeren Razor Pages, API denetleyicileri ve denetleyiciler için geçerlidir.

Örneği ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) .

## <a name="how-filters-work"></a>Filtreler nasıl çalışır?

Filtreler, bazen *Filtre işlem hattı*olarak da adlandırılan *ASP.NET Core eylemi çağırma işlem hattı*içinde çalışır.  Filtre işlem hattı çalıştırılacak eylemi ASP.NET Core seçtikten sonra çalışır.

![İstek diğer ara yazılım, yönlendirme ara yazılımı, eylem seçimi ve ASP.NET Core eylemi çağırma Işlem hattı aracılığıyla işlenir. İstek işleme, istemciye gönderilen bir yanıt olmadan önce eylem seçimi, yönlendirme ara yazılımı ve diğer diğer ara yazılım aracılığıyla yeniden devam eder.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Filtre türleri

Her filtre türü, filtre ardışık düzeninde farklı bir aşamada yürütülür:

* İlk olarak [Yetkilendirme filtreleri](#authorization-filters) çalışır ve kullanıcının istek için yetkilendirilip yetkilendirilmediğini tespit etmek için kullanılır. Yetkilendirme filtreleri, istek yetkisiz ise işlem hattı kısa devre dışı.

* [Kaynak filtreleri](#resource-filters):

  * Yetkilendirmeden sonra çalıştırın.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>Filtre işlem hattının geri kalanından önce kod çalıştırabilir. Örneğin, `OnResourceExecuting` model bağlamadan önce kodu çalıştırabilir.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>ardışık düzenin geri kalanı tamamlandıktan sonra kodu çalıştırabilir.

* [Eylem filtreleri](#action-filters) , tek bir eylem yöntemi çağrıldıktan hemen önce ve sonra kodu çalıştırabilir. Bir eyleme geçirilen bağımsız değişkenleri ve eylemden döndürülen sonucu işlemek için kullanılabilirler. Eylem filtreleri Razor Pages **desteklenmez.**

* [Özel durum filtreleri](#exception-filters) , yanıt gövdesine hiçbir şey yazılmadan önce oluşan işlenmemiş özel durumlara genel ilkeler uygulamak için kullanılır.

* [Sonuç filtreleri](#result-filters) , tek tek eylem sonuçlarının yürütülmesinden hemen önce ve sonra kodu çalıştırabilir. Bunlar yalnızca eylem yöntemi başarıyla yürütüldüğünde çalışır. Bu değerler, görünüm veya biçimlendirici yürütmesinin yürütülmesi gereken mantık için faydalıdır.

Aşağıdaki diyagramda filtre türlerinin filtre ardışık düzeninde nasıl etkileşimde bulunduğu gösterilmektedir.

![İstek, Yetkilendirme filtreleri, kaynak filtreleri, model bağlama, eylem filtreleri, eylem yürütme ve eylem sonucu dönüştürme, özel durum filtreleri, sonuç filtreleri ve sonuç yürütmesi aracılığıyla işlenir. Bu şekilde, istek yalnızca istemciye gönderilen yanıt haline gelmeden önce sonuç filtreleri ve kaynak filtreleri tarafından işlenir.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Uygulama

Filtreler, farklı arabirim tanımları aracılığıyla hem zaman uyumlu hem de zaman uyumsuz uygulamaları destekler.

Zaman uyumlu filtreler, ardışık düzen aşamasını`On-Stage-Executing`() ve sonra`On-Stage-Executed`kodu çalıştırabilir. Örneğin, `OnActionExecuting` eylem yöntemi çağrılmadan önce çağrılır. `OnActionExecuted`, eylem yöntemi döndüğünde çağrılır.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

Zaman uyumsuz filtreler bir `On-Stage-ExecutionAsync` yöntemi tanımlar:

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

Önceki kodda,, `SampleAsyncActionFilter` Action metodunu yürüten bir <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) içerir.  `On-Stage-ExecutionAsync` Yöntemlerin her biri, filtrenin ardışık `FilterType-ExecutionDelegate` düzen aşamasını yürüten bir alır.

### <a name="multiple-filter-stages"></a>Birden çok filtre aşaması

Birden çok filtre aşaması için arabirimler tek bir sınıfta uygulanabilir. Örneğin, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> sınıfı, `IActionFilter` `IResultFilter`ve zaman uyumsuz eşdeğerlerini uygular.

Her ikisini de **değil** , bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.** Çalışma zamanı öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır. Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır. Tek bir sınıfta hem zaman uyumsuz hem de zaman uyumlu arabirimler uygulanmışsa, yalnızca zaman uyumsuz yöntem çağrılır. Soyut sınıfların kullanıldığı durumlarda, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> yalnızca zaman uyumlu yöntemleri veya her bir filtre türü için zaman uyumsuz yöntemi geçersiz kılın.

### <a name="built-in-filter-attributes"></a>Yerleşik filtre öznitelikleri

ASP.NET Core, alt sınıflanmış ve özelleştirilebilen yerleşik öznitelik tabanlı filtreler içerir. Örneğin, aşağıdaki sonuç filtresi yanıta bir üst bilgi ekler:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Öznitelikler, önceki örnekte gösterildiği gibi, filtrelerin bağımsız değişkenleri kabul etmesine izin verir. `AddHeaderAttribute` ' İ bir denetleyiciye veya eylem yöntemine UYGULAYıN ve http üstbilgisinin adını ve değerini belirtin:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

Filtre arabirimlerinden birkaçı, özel uygulamalar için temel sınıflar olarak kullanılabilecek karşılık gelen özniteliklere sahiptir.

Filtre öznitelikleri:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Kapsamları ve yürütme sırasını filtrele

Üç *kapsamından*birindeki işlem hattına bir filtre eklenebilir:

* Bir eylem üzerinde bir öznitelik kullanma.
* Bir denetleyicide bir özniteliği kullanma.
* Aşağıdaki kodda gösterildiği gibi tüm denetleyiciler ve eylemler için genel olarak:

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

Yukarıdaki kod, [Mvcoptions. Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) koleksiyonunu kullanarak genel olarak üç filtre ekler.

### <a name="default-order-of-execution"></a>Varsayılan yürütme sırası

*Aynı türde*birden çok filtre olduğunda kapsam, filtre yürütmenin varsayılan sırasını belirler.  Genel filtreler saran sınıf filtreleri. Sınıf filtreleri surround yöntemi filtreleri.

Filtre iç içe geçme sonucu *olarak, filtrenin kodu,* *önceki* kodun ters sırasına göre çalışır. Filtre sırası:

* Genel filtrelerin *önceki* kodu.
  * Denetleyici filtrelerinden *önceki* kod.
    * Eylem yöntemi filtrelerinden *önceki* kod.
    * Eylem yöntemi filtrelerinden *sonraki* kod.
  * Denetleyici filtrelerinden *sonraki* kod.
* Genel filtrelerin *sonraki* kodu.
  
Zaman uyumlu eylem filtreleri için filtre yöntemlerinin çağrıldığı sırayı gösteren aşağıdaki örnek.

| Sequence | Filtre kapsamı | Filter yöntemi |
|:--------:|:------------:|:-------------:|
| 1 | Genel | `OnActionExecuting` |
| 2 | Kumandasını | `OnActionExecuting` |
| 3 | Yöntem | `OnActionExecuting` |
| 4 | Yöntem | `OnActionExecuted` |
| 5 | Kumandasını | `OnActionExecuted` |
| 6 | Genel | `OnActionExecuted` |

Bu sıra şunları gösterir:

* Yöntem filtresi, denetleyici filtresi içinde iç içe geçmiş.
* Denetleyici filtresi, genel filtrenin içinde iç içe geçmiş.

### <a name="controller-and-razor-page-level-filters"></a>Denetleyici ve Razor sayfa düzeyi filtreleri

<xref:Microsoft.AspNetCore.Mvc.Controller> Temel sınıftan devralan her denetleyici [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. onactionexecutionasync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)ve [Controller. onactionyürütülmüş](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` yöntemlerini içerir. Bu Yöntemler:

* Belirli bir eylem için çalışan filtreleri sarın.
* `OnActionExecuting`, eylemin filtrelerinden herhangi birinin önüne çağırılır.
* `OnActionExecuted`tüm eylem filtrelerinden sonra çağrılır.
* `OnActionExecutionAsync`, eylemin filtrelerinden herhangi birinin önüne çağırılır. Eylem yöntemiyle çalıştıktan sonra `next` filtredeki kod.

Örneğin, indirme örneğinde, `MySampleActionFilter` başlangıçta genel olarak uygulanır.

`TestController`Şunları yapın:

* `FilterTest2` Eyleme `SampleActionFilterAttribute` (`[SampleActionFilter]`) uygular.
* Geçersiz `OnActionExecuting` kılmalar `OnActionExecuted`ve.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

' A `https://localhost:5001/Test/FilterTest2` gitme aşağıdaki kodu çalıştırır:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Razor Pages için bkz. [filtre yöntemlerini geçersiz kılarak Razor sayfası filtrelerini uygulama](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Varsayılan sırayı geçersiz kılma

Varsayılan yürütme sırası, uygulama <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>tarafından geçersiz kılınabilir. `IOrderedFilter`yürütme sırasını <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> tespit etmek için kapsama göre öncelik alan özelliği gösterir. Düşük `Order` değere sahip bir filtre:

* Daha yüksek değeri olan bir filtreden önce koddan *önce* kodu çalıştırır `Order`.
* Daha yüksek *after* `Order` bir değere sahip bir filtrenin sonrasında koddan sonra çalışır.

`Order` Özelliği bir Oluşturucu parametresiyle ayarlanabilir:

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Yukarıdaki örnekte gösterilen 3 eylem filtresini göz önünde bulundurun. Denetleyicinin ve `Order` genel filtrelerin özelliği sırasıyla 1 ve 2 olarak ayarlandıysa, yürütme sırası tersine çevrilir.

| Sequence | Filtre kapsamı | `Order`özelliði | Filter yöntemi |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Yöntem | 0 | `OnActionExecuting` |
| 2 | Kumandasını | 1  | `OnActionExecuting` |
| 3 | Genel | 2  | `OnActionExecuting` |
| 4 | Genel | 2  | `OnActionExecuted` |
| 5 | Kumandasını | 1  | `OnActionExecuted` |
| 6 | Yöntem | 0  | `OnActionExecuted` |

`Order` Özelliği filtrelerin çalıştırılacağı sıra belirlenirken kapsamı geçersiz kılar. Filtreler sırasıyla sıraya göre sıralanır, ardından kapsam bölmek için kullanılır. Yerleşik filtrelerin hepsi uygular `IOrderedFilter` ve varsayılan `Order` değeri 0 olarak ayarlar. Yerleşik filtreler için kapsam, sıfır olmayan bir değere ayarlanmadığı `Order` takdirde sıralamayı belirler.

## <a name="cancellation-and-short-circuiting"></a>İptal ve kısa devre dışı

Filtre işlem hattı, filtre yöntemine sunulan <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametrede özelliği ayarlanarak kısa devre yapılabilir. Örneğin, aşağıdaki kaynak filtresi, ardışık düzenin geri kalanının yürütülmesini engeller:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

Aşağıdaki kodda, `ShortCircuitingResourceFilter` ve `AddHeader` filtresi `SomeResource` Action metodunu hedefleyin. `ShortCircuitingResourceFilter`Şunları yapın:

* İlk olarak bir kaynak filtresi olduğundan ve `AddHeader` bir eylem filtresiyle çalışır.
* Kısa süreli işlem hattının geri kalanı.

Bu nedenle `AddHeader` , filtre `SomeResource` eylem için hiçbir şekilde çalışmayacaktır. Bu davranış, her iki filtre de eylem yöntemi düzeyinde uygulanırsa, ilk olarak `ShortCircuitingResourceFilter` çalıştırıldığında aynı olur. İlk `ShortCircuitingResourceFilter` olarak, filtre türü veya açıkça `Order` özelliğin kullanımı kullanılarak çalışır.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Bağımlılık ekleme

Filtreler türe veya örneğe göre eklenebilir. Bir örnek eklenirse, bu örnek her istek için kullanılır. Bir tür eklenirse, türü etkinleştirilmiş olur. Tür etkinleştirilmiş bir filtre şu anlama gelir:

* Her istek için bir örnek oluşturulur.
* Herhangi bir Oluşturucu bağımlılığı [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından doldurulur.

Öznitelik olarak uygulanan ve doğrudan denetleyici sınıflarına veya eylem yöntemlerine eklenen filtreler [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından sağlanmış Oluşturucu bağımlılıklarına sahip olamaz. Oluşturucu bağımlılıkları şu nedenle şu nedenle sağlanamaz:

* Özniteliklerin, uygulandıkları yerlerde, Oluşturucu parametreleri sağlanmalıdır. 
* Bu, özniteliklerin nasıl çalıştığı konusunda bir kısıtlamadır.

Aşağıdaki filtreler, dı tarafından belirtilen Oluşturucu bağımlılıklarını destekler:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>özniteliği üzerinde uygulandı.

Önceki filtreler bir denetleyiciye veya eylem yöntemine uygulanabilir:

Günlükçüler, DI 'den alınabilir. Ancak, yalnızca günlüğe kaydetme amacıyla filtre oluşturmaktan ve kullanmaktan kaçının. [Yerleşik çerçeve günlüğü](xref:fundamentals/logging/index) genellikle günlüğe kaydetme için gerekenleri sağlar. Filtrelere günlük eklendi:

* , İş etki alanı kaygılarını veya filtreye özgü davranışları odaklamalıdır.
* Eylemleri veya diğer çerçeve olaylarını günlüğe **içermemelidir** . Yerleşik filtreler günlük eylemleri ve çerçeve olayları.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Hizmet filtresi uygulama türleri ' de `ConfigureServices`kaydedilir. Bir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> filtrenin bir örneğini alır.

Aşağıdaki kod şunu gösterir `AddHeaderResultServiceFilter`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Aşağıdaki kodda, `AddHeaderResultServiceFilter` dı kapsayıcısına eklenmiştir:

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

Aşağıdaki kodda, `ServiceFilter` özniteliği, bir `AddHeaderResultServiceFilter` filtrenin bir örneğini dı:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Kullanırken `ServiceFilterAttribute`, [servicefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)olarak ayarlanıyor:

* Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden *kullanılabilir olabileceğini gösteren* bir ipucu sağlar. ASP.NET Core çalışma zamanı garanti etmez:

  * Filtrenin tek bir örneğinin oluşturulması gerekir.
  * Filtre, sonraki bir noktada dı kapsayıcısından yeniden istenmeyecek.

* Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> örnek oluşturma <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> yöntemini gösterir. `CreateInstance`belirtilen türü DI 'dan yükler.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>benzerdir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ancak türü doğrudan dı kapsayıcısından çözümlenmez. Kullanarak <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>türü başlatır.

Türler `TypeFilterAttribute` doğrudan dı kapsayıcısından çözümlenmediğinden:

* ' İ kullanılarak başvurulan türlerin, `TypeFilterAttribute` dı kapsayıcısına kayıtlı olması gerekmez.  Bunların bağımlılıkları, dı kapsayıcısı tarafından yerine getirilir.
* `TypeFilterAttribute`isteğe bağlı olarak tür için Oluşturucu bağımsız değişkenlerini kabul edebilir.

Kullanırken `TypeFilterAttribute`, [typefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)olarak ayarlanıyor:
* Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden kullanılabilir *olabileceği* ipucu sağlar. ASP.NET Core çalışma zamanı, filtrenin tek bir örneğinin oluşturulacağı garantisi vermez.

* Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.

Aşağıdaki örnek kullanarak `TypeFilterAttribute`bir türe bağımsız değişkenlerin nasıl geçirileceğini göstermektedir:

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Yetkilendirme filtreleri

Yetkilendirme filtreleri:

* , Filtre ardışık düzeninde ilk filtrelerin çalıştırılmasıyla sonuçlanır.
* Eylem yöntemlerine erişimi denetleyin.
* Bir Before yöntemi, ancak After yönteminden hiçbiri.

Özel Yetkilendirme filtreleri özel bir yetkilendirme çerçevesi gerektirir. Özel bir filtre yazmak için yetkilendirme ilkelerini yapılandırmayı veya özel bir yetkilendirme ilkesi yazmayı tercih edin. Yerleşik yetkilendirme filtresi:

* Yetkilendirme sistemini çağırır.
* İstekleri yetkilendirmez.

Yetkilendirme filtreleri içinde özel **durumlar atamayın:**

* Özel durum işlenmeyecektir.
* Özel durum filtreleri özel durumu işleymeyecektir.

Bir yetkilendirme filtresinde özel durum oluştuğunda bir sınama vermeyi düşünün.

[Yetkilendirme](xref:security/authorization/introduction)hakkında daha fazla bilgi edinin.

## <a name="resource-filters"></a>Kaynak filtreleri

Kaynak filtreleri:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> Ya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> da arabirimini uygulayın.
* Yürütme, filtre işlem hattının çoğunu sarmalar.
* Kaynak filtrelerinden önce yalnızca [Yetkilendirme filtreleri](#authorization-filters) çalışır.

Kaynak filtreleri, işlem hattının büyük bir yanındaki kısa devre için faydalıdır. Örneğin, bir önbelleğe alma filtresi, bir önbellek isabetinden ardışık düzen geri kalanından kaçınabilir.

Kaynak filtresi örnekleri:

* Daha önce gösterilen [kısa devre dışı kaynak filtresi](#short-circuiting-resource-filter) .
* [Disableformvaluemodelbindingattribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Model bağlamanın form verilerine erişimini engeller.
  * Form verilerinin belleğe okunmasını engellemek için büyük dosya yüklemeleri için kullanılır.

## <a name="action-filters"></a>Eylem filtreleri

> [!IMPORTANT]
> Eylem filtreleri Razor sayfalara **uygulanmaz.** RazorSayfalar ve <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> destekler. Daha fazla bilgi için bkz. [Sayfalar Için Razor filtre yöntemleri](xref:razor-pages/filter).

Eylem filtreleri:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> Ya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> da arabirimini uygulayın.
* Yürütmesinin, eylem yöntemlerinin yürütülmesi çevreler.

Aşağıdaki kod bir örnek eylem filtresi gösterir:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Aşağıdaki özellikleri sağlar:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-bir eylem yöntemine yönelik girişlerin okunmalarını sağlar.
* <xref:Microsoft.AspNetCore.Mvc.Controller>-denetleyici örneğinin işlenmesine izin vermez.
* <xref:System.Web.Mvc.ActionExecutingContext.Result>-Eylem `Result` yönteminin ve sonraki eylem filtrelerinin kısa devre dışı yürütülmesi ayarlanıyor.

Eylem yönteminde özel durum oluşturma:

* Sonraki filtrelerin çalıştırılmasını önler.
* Ayarından `Result`farklı olarak, başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.

, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> Ve `Controller` `Result` ek olarak aşağıdaki özellikleri sağlar:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled>-Eylem yürütmesi başka bir filtre tarafından kabul edilse true.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception>-Eylem veya daha önce çalıştırılan eylem filtresi bir özel durum oluşturdu-null değil. Bu özellik null olarak ayarlanıyor:

  * Özel durumu etkin bir şekilde işler.
  * `Result`eylem yönteminden döndürülmüş gibi yürütülür.

Bir `IAsyncActionFilter`için bir çağrısı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:

* Sonraki eylem filtrelerini ve eylem yöntemini yürütür.
* `ActionExecutedContext` döndürür.

Kısa devre dışı, bir sonuç <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> örneğine atayın ve çağırmayın `next` ( `ActionExecutionDelegate`).

Çerçeve, alt sınıflı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> olabilecek bir Özet sağlar.

`OnActionExecuting` Eylem filtresi şu şekilde kullanılabilir:

* Model durumunu doğrulayın.
* Durum geçersizse bir hata döndürür.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

`OnActionExecuted` Yöntemi eylem yönteminden sonra çalışır:

* Ve, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> özelliği aracılığıyla eylemin sonuçlarını görebilir ve değiştirebilir.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>eylem yürütmesi başka bir filtre tarafından kabul edilse, true olarak ayarlanır.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>eylem veya sonraki bir eylem filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır. Null `Exception` olarak ayarlanıyor:

  * Bir özel durumu etkin bir şekilde işler.
  * `ActionExecutedContext.Result`eylem yönteminden normal olarak döndürülmüş gibi yürütülür.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Özel durum filtreleri

Özel durum filtreleri:

* Veya <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>uygulayın. 
* , Yaygın hata işleme ilkelerini uygulamak için kullanılabilir.

Aşağıdaki örnek özel durum filtresi, uygulama geliştirmede olduğunda oluşan özel durumlar hakkındaki ayrıntıları görüntülemek için özel bir hata görünümü kullanır:

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Özel durum filtreleri:

* Etkinlikden önceki ve sonraki olaylar yok.
* Veya <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>uygulayın.
* Razor Sayfa veya denetleyici oluşturma, [model bağlama](xref:mvc/models/model-binding), eylem filtreleri veya eylem yöntemlerinde oluşan işlenmemiş özel durumları işleyin.
* Kaynak filtrelerinde, sonuç filtrelerinde veya MVC sonuç yürütülürken oluşan özel **durumları yakalamayın** .

Bir özel durumu işlemek için, <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> özelliğini bir yanıt `true` olarak ayarlayın veya yazın. Bu, özel durumun yayılmasını engeller. Özel durum filtresi bir özel durumu "başarılı" olarak açamaz. Yalnızca bir eylem filtresi bunu yapabilir.

Özel durum filtreleri:

* Eylemler içinde oluşan özel durumları yakalamaya uygundur.
* , Hata işleme ara yazılımı olarak esnek değildir.

Özel durum işleme için ara yazılımı tercih edin. Yalnızca hata işlemenin hangi eylem yöntemine göre *farklılık* gösteren özel durum filtrelerini kullanın. Örneğin, bir uygulama hem API uç noktaları hem de görünümler/HTML için eylem yöntemlerine sahip olabilir. API uç noktaları, hata bilgilerini JSON olarak döndürebilir, ancak görünüm tabanlı eylemler bir hata sayfasını HTML olarak döndürebilir.

## <a name="result-filters"></a>Sonuç filtreleri

Sonuç filtreleri:

* Arabirim uygulama:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Yürütmesi, eylem sonuçlarının yürütülmesini çevreler.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter ve ıasyncresultfilter

Aşağıdaki kod, bir HTTP üst bilgisi ekleyen bir sonuç filtresi gösterir:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Yürütülen sonuç türü eyleme göre değişir. Bir görünüm döndüren bir eylem, <xref:Microsoft.AspNetCore.Mvc.ViewResult> çalıştırılmakta olan bir parçası olarak tüm Razor işlemlerini içerir. Bir API yöntemi, sonucun yürütülmesinin bir parçası olarak bazı serileştirme işlemleri gerçekleştirebilir. [Eylem sonuçları](xref:mvc/controllers/actions)hakkında daha fazla bilgi edinin.

Sonuç filtreleri yalnızca bir eylem veya eylem filtresi bir eylem sonucu üretirse yürütülür. Şu durumlarda sonuç filtreleri yürütülmez:

* Bir yetkilendirme filtresi veya kaynak filtresi, işlem hattı için kısa süreli olarak devre dışı.
* Bir özel durum filtresi, bir eylem sonucu üreterek özel durumu işler.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> Yöntemi, olarak <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true`ayarlanarak eylem sonucunun ve sonraki sonuç filtrelerinin kısa devre yürütülmesine neden olabilir. Boş bir yanıt oluşturmamaya kaçınmak için kısa devre dışı bırakıldığında yanıt nesnesine yazın. Bir özel durum oluşturmak `IResultFilter.OnResultExecuting` için şunları yapmanız gerekir:

* Eylem sonucunun ve sonraki filtrelerin yürütülmesini önleyin.
* Başarılı bir sonuç yerine hata olarak kabul edilir.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> Yöntem çalıştığında, yanıt zaten istemciye gönderilmiştir. Yanıt istemciye zaten gönderildiyse, daha fazla değiştirilemez.

`ResultExecutedContext.Canceled`eylem sonucu yürütmesi `true` başka bir filtre tarafından kabul edilen ise olarak ayarlanır.

`ResultExecutedContext.Exception`eylem sonucu veya sonraki sonuç filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır. Null `Exception` olarak ayarlanması bir özel durumu işler ve işlem hattının ilerleyen kısımlarında ASP.NET Core özel durumun yeniden oluşturulmasını önler. Bir sonuç filtresinde özel durum işlenirken yanıta veri yazmanın güvenilir bir yolu yoktur. Bir eylem sonucu bir özel durum oluşturduğunda üstbilgiler istemciye temizleniyorsa, hata kodu göndermek için güvenilir bir mekanizma yoktur.

Bir <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>için bir çağrısı `await next` , sonraki sonuç filtrelerini <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ve eylem sonucunu yürütür. Kısa devre dışı, [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) olarak ayarlayın `true` ve şunu çağırmayın `ResultExecutionDelegate`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

Çerçeve, alt sınıflı `ResultFilterAttribute` olabilecek bir Özet sağlar. Daha önce gösterilen [Addheaderattribute](#add-header-attribute) sınıfı bir sonuç Filtresi özniteliği örneğidir.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>Ialwaysrunresultfilter ve ıasyncalwaysrunresultfilter

Ve <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> arabirimleri, tüm eylem <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> sonuçları için çalışan bir uygulama bildirir. Bu, tarafından oluşturulan eylem sonuçlarını içerir:

* Kısa devre olan Yetkilendirme filtreleri ve kaynak filtreleri.
* Özel durum filtreleri.

Örneğin, aşağıdaki filtre her zaman çalışır ve içerik anlaşması başarısız olduğunda<xref:Microsoft.AspNetCore.Mvc.ObjectResult> *422 olmayan bir varlık* durum kodu ile bir eylem sonucu () ayarlar:

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Bu nedenle, `IFilterFactory` bir örnek, filtre ardışık düzeninde `IFilterMetadata` herhangi bir yerde örnek olarak kullanılabilir. Çalışma zamanı filtreyi çağırmayı hazırlarken, bir `IFilterFactory`öğesine dönüştürmeyi dener. Atama başarılı olursa, çağrılan <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> `IFilterMetadata` örneği oluşturmak için yöntemi çağırılır. Bu, tam filtre işlem hattının uygulama başladığında açıkça ayarlanması gerektiğinden esnek bir tasarım sağlar.

`IFilterFactory`, filtre oluşturmaya yönelik başka bir yaklaşım olarak özel öznitelik uygulamaları kullanılarak uygulanabilir:

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

Önceki kod, [indirme örneği](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)çalıştırılarak test edilebilir:

* F12 geliştirici araçlarını çağırın.
* `https://localhost:5001/Sample/HeaderWithFactory` sayfasına gidin.

F12 geliştirici araçları, örnek kod tarafından eklenen aşağıdaki yanıt üstbilgilerini görüntüler:

* **Yazar:**`Joe Smith`
* **globaladdheader:**`Result filter added to MvcOptions.Filters`
* **iç:**`My header`

Yukarıdaki kod, **iç:** `My header` yanıt üst bilgisini oluşturur.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>Öznitelik üzerinde IFilterFactory uygulandı

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Uygulayan `IFilterFactory` filtreler şu filtreler için yararlıdır:

* Parametre geçirme gerekmez.
* DI tarafından doldurulması gereken Oluşturucu bağımlılıkları vardır.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> örnek oluşturma <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> yöntemini gösterir. `CreateInstance`belirtilen türü hizmetler kapsayıcısından (dı) yükler.

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Aşağıdaki kod, `[SampleActionFilter]`uygulamak için üç yaklaşımda gösterilmiştir:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

Yukarıdaki kodda, yöntemi ile `[SampleActionFilter]` dekorasyon, uygulamak için tercih edilen yaklaşımdır. `SampleActionFilter`

## <a name="using-middleware-in-the-filter-pipeline"></a>Filtre ardışık düzeninde ara yazılım kullanma

Kaynak filtreleri, işlem hattında daha sonra gelen her şeyin yürütülmesini çevreleyecek olan [Ara yazılım](xref:fundamentals/middleware/index) gibi çalışır. Ancak filtreler, ASP.NET Core çalışma zamanının bir parçası olan ara yazılımlar dışında farklılık gösterir, bu da ASP.NET Core bağlamına ve yapılarına erişime sahip oldukları anlamına gelir.

Ara yazılımı bir filtre olarak kullanmak için, filtre ardışık düzenine eklenecek `Configure` olan ara yazılımı belirten bir yöntemi olan bir tür oluşturun. Aşağıdaki örnek, bir istek için geçerli kültürü oluşturmak üzere yerelleştirme ara yazılımını kullanır:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Ara yazılımını <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> çalıştırmak için kullanın:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Ara yazılım filtreleri, filtre işlem hattının aynı aşamasında, model bağlamadan önce ve işlem hattının geri kalanı ile kaynak filtreleri olarak çalışır.

## <a name="next-actions"></a>Sonraki eylemler

* [Sayfalar Için Razor filtre yöntemlerine](xref:razor-pages/filter)bakın.
* Filtrelerle denemek için [GitHub örneğini indirin, test edin ve değiştirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

::: moniker-end
