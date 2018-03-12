---
title: "Razor sayfalarının yol ve uygulama kuralı özellikleri ASP.NET Core"
author: guardrex
description: "Nasıl yol ve uygulama modeli sağlayıcı Kuralı Özellikleri sayfası denetimi yönlendirme, bulma ve işleme yardımcı bulur."
manager: wpickett
ms.author: riande
ms.date: 10/23/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: mvc/razor-pages/razor-pages-convention-features
ms.openlocfilehash: 5105935a8f5b9e9f258fe84f839d17f6948bab1d
ms.sourcegitcommit: 9622bdc6326c28c3322c70000468a80ef21ad376
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/12/2018
---
# <a name="razor-pages-route-and-app-convention-features-in-aspnet-core"></a>Razor sayfalarının yol ve uygulama kuralı özellikleri ASP.NET Core

Tarafından [Luke Latham](https://github.com/guardrex)

Sayfa kullanmayı öğrenin [yol ve uygulama model sağlayıcısı kuralı](xref:mvc/controllers/application-model#conventions) denetim özellikleri sayfasında yönlendirme, bulma ve Razor sayfalarının uygulamalarda işleme. Tek tek sayfaları, özel sayfa yolları gerektiğinde sayfalarıyla için yönlendirmeyi yapılandırma [AddPageRoute kuralı](#configure-a-page-route) bu konunun ilerleyen bölümlerinde açıklanan.

[Görüntülemek veya karşıdan örnek kod](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/razor-pages-convention-features/sample/) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))

| Özellikler | Örnek gösterir... |
| -------- | --------------------------- |
| [Yol ve uygulama modeli kuralları](#add-route-and-app-model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li></ul> | Bir rota şablonu ve üst bilgisi bir uygulamanın sayfalara ekleme. |
| [Sayfa rota eylem kuralları](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Bir rota şablonu, bir klasördeki sayfalar ve tek bir sayfayla ekleniyor. |
| [Sayfa modeli eylem kuralları](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre Fabrika)</li></ul> | Bir klasördeki sayfalara üstbilgi ekleme, tek bir sayfa için üstbilgi ekleme ve yapılandırma bir [filtre Fabrika](xref:mvc/controllers/filters#ifilterfactory) üstbilgi bir uygulamanın sayfalara eklemek için. |
| [Varsayılan sayfa uygulama modeli sağlayıcısı](#replace-the-default-page-app-model-provider) | İşleyici adlandırma kurallarını değiştirmek için varsayılan sayfa model sağlayıcısı değiştiriliyor. |

## <a name="add-route-and-app-model-conventions"></a>Yol ve uygulama modeli kuralları ekleme

Bir temsilci Ekle [IPageConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageconvention) eklemek için [yol ve uygulama modeli kuralları](xref:mvc/controllers/application-model#conventions) Razor sayfalara uygulayın.

**Tüm sayfalar için bir yol modeli Kuralı Ekle**

Kullanım [kuralları](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.razorpagesoptions.conventions) oluşturmak ve eklemek için bir [IPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelconvention) koleksiyonuna [IPageConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageconvention) yolu ve sayfayı modeli sırasında uygulanan örnekleri oluşturma.

Örnek uygulaması ekler bir `{globalTemplate?}` uygulamadaki tüm sayfalar için rota şablonu:

[!code-csharp[](razor-pages-convention-features/sample/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

> [!NOTE]
> `Order` Özelliği için `AttributeRouteModel` ayarlanır `-1`. Bu, bu şablonu bir tek yönlendirme değeri sağlanır ve onun üzerinde otomatik olarak oluşturulan Razor sayfalarının yollar önceliği de olurdu ilk rota veri değeri konumunun öncelik verilir sağlar. Örneğin, örnek ekler bir `{aboutTemplate?}` konunun ilerleyen bölümlerinde rota şablonu. `{aboutTemplate?}` Şablon verilen bir `Order` , `1`. Ne zaman hakkında sayfa istenen adresindeki `/About/RouteDataValue`, "RouteDataValue" içine yüklenir `RouteData.Values["globalTemplate"]` (`Order = -1`) ve `RouteData.Values["aboutTemplate"]` (`Order = 1`) ayarı nedeniyle `Order` özelliği.

Razor sayfalarının seçenekleri ekleme gibi [kuralları](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.razorpagesoptions.conventions), MVC hizmet koleksiyonunda eklendiğinde eklenen `Startup.ConfigureServices`. Bir örnek için bkz: [örnek uygulaması](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/razor-pages-convention-features/sample/).

[!code-csharp[](razor-pages-convention-features/sample/Startup.cs?name=snippet1)]

Örnek ait hakkında sayfanın isteği `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:

![Hakkında sayfa GlobalRouteValue sahip bir rota segment istendi. İşlenen sayfanın rota veri değeri sayfa OnGet yönteminde yakalanır gösterir.](razor-pages-convention-features/_static/about-page-global-template.png)

**Tüm sayfalar için bir uygulama modeli Kuralı Ekle**

Kullanım [kuralları](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.razorpagesoptions.conventions) oluşturmak ve eklemek için bir [IPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelconvention) koleksiyonuna [IPageConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageconvention) yolu ve sayfayı sırasında uygulanan örnekleri Model oluşturma.

Bu ve diğer kuralları konunun ilerleyen bölümlerinde göstermek için örnek uygulamasını içeren bir `AddHeaderAttribute` sınıfı. Sınıf oluşturucu kabul eden bir `name` dize ve `values` dize dizisi. Bu değerler kullanılır, `OnResultExecuting` yöntemi bir yanıt üstbilgisi ayarlayın. Tam sınıfı gösterilen [sayfasında modeli eylem kuralları](#page-model-action-conventions) konunun ilerleyen bölümlerinde.

Örnek uygulama kullandığı `AddHeaderAttribute` üst bilgi eklemek için sınıfı `GlobalHeader`, uygulamadaki tüm sayfalar için:

[!code-csharp[](razor-pages-convention-features/sample/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-convention-features/sample/Startup.cs?name=snippet2)]

Örnek ait hakkında sayfanın isteği `localhost:5000/About` ve sonuçları görüntülemek için üstbilgileri inceleyin:

![Yanıt Üstbilgileri hakkında sayfasının GlobalHeader eklenmiş olduğunu gösterir.](razor-pages-convention-features/_static/about-page-global-header.png)

## <a name="page-route-action-conventions"></a>Sayfa rota eylem kuralları

Türetilen varsayılan rota model sağlayıcısı [IPageRouteModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelprovider) sayfa yolları yapılandırmak için genişletilebilirlik noktaları sağlamak için tasarlanmış kuralları çağırır.

**Klasör yolu modeli kuralı**

Kullanım [AddFolderRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addfolderroutemodelconvention) oluşturmak ve eklemek için bir [IPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelconvention) , çağıran bir eylem üzerinde [PageRouteModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageroutemodel) tüm sayfalar için Belirtilen klasör.

Örnek uygulama kullandığı `AddFolderRouteModelConvention` eklemek için bir `{otherPagesTemplate?}` sayfaları için rota şablonu *OtherPages* klasörü:

[!code-csharp[](razor-pages-convention-features/sample/Startup.cs?name=snippet3)]

> [!NOTE]
> `Order` Özelliği için `AttributeRouteModel` ayarlanır `1`. Bu şablon için sağlar `{globalTemplate?}` (küme konudaki daha önceki) bir tek yönlendirme değeri sağlandığında konumu ilk rota veri değeri için öncelik verilen. Konumundaki Page1 sayfa istediyseniz `/OtherPages/Page1/RouteDataValue`, "RouteDataValue" içine yüklenir `RouteData.Values["globalTemplate"]` (`Order = -1`) ve `RouteData.Values["otherPagesTemplate"]` (`Order = 1`) ayarı nedeniyle `Order` özelliği.

Örnek 's Page1 sayfanın isteği `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:

![Page1 OtherPages klasöründeki GlobalRouteValue ve OtherPagesRouteValue yol kesimi ile istendi. İşlenen sayfanın rota veri değerleri sayfa OnGet yönteminde yakalanır gösterir.](razor-pages-convention-features/_static/otherpages-page1-global-and-otherpages-templates.png)

**Sayfa rota modeli kuralı**

Kullanım [AddPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addpageroutemodelconvention) oluşturmak ve eklemek için bir [IPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelconvention) , çağıran bir eylem üzerinde [PageRouteModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageroutemodel) belirtilen sayfa için adı.

Örnek uygulama kullandığı `AddPageRouteModelConvention` eklemek için bir `{aboutTemplate?}` hakkında sayfasına rota şablonu:

[!code-csharp[](razor-pages-convention-features/sample/Startup.cs?name=snippet4)]

> [!NOTE]
> `Order` Özelliği için `AttributeRouteModel` ayarlanır `1`. Bu şablon için sağlar `{globalTemplate?}` (küme konudaki daha önceki) bir tek yönlendirme değeri sağlandığında konumu ilk rota veri değeri için öncelik verilen. Konumundaki hakkında sayfa istediyseniz `/About/RouteDataValue`, "RouteDataValue" içine yüklenir `RouteData.Values["globalTemplate"]` (`Order = -1`) ve `RouteData.Values["aboutTemplate"]` (`Order = 1`) ayarı nedeniyle `Order` özelliği.

Örnek ait hakkında sayfanın isteği `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:

![Sayfa hakkında GlobalRouteValue ve AboutRouteValue için yol kesimleri ile istendi. İşlenen sayfanın rota veri değerleri sayfa OnGet yönteminde yakalanır gösterir.](razor-pages-convention-features/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Bir sayfa yolunu Yapılandır

Kullanım [AddPageRoute](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.addpageroute) belirtilen sayfa yolda bir rota bir sayfaya yapılandırmak için. Belirtilen rota sayfasına oluşturulan bağlantıları kullanın. `AddPageRoute` kullanan `AddPageRouteModelConvention` rota oluşturmak için.

Örnek uygulama için bir yol oluşturur `/TheContactPage` için *Contact.cshtml*:

[!code-csharp[](razor-pages-convention-features/sample/Startup.cs?name=snippet5)]

İlgili kişi sayfası üzerinde de erişilebilir `/Contact` varsayılan yol aracılığıyla.

İlgili kişi sayfası örnek uygulamanın özel yol için bir isteğe bağlı verir `text` yol kesimi (`{text?}`). Sayfa Ayrıca isteğe bağlı bu kesimdeki içerir, `@page` sayfanın ziyaretçi erişen durumda yönergesi kendi `/Contact` yol:

[!code-cshtml[](razor-pages-convention-features/sample/Pages/Contact.cshtml?highlight=1)]

URL için oluşturulan Not **kişi** işlenen sayfa bağlantıyı güncelleştirilmiş rota yansıtır:

![Örnek uygulama kişi bağlantı gezinti çubuğunda](razor-pages-convention-features/_static/contact-link.png)

![İşlenen HTML kişi bağlantıyı inceleniyor gösterir href ayarlamak ' / TheContactPage'](razor-pages-convention-features/_static/contact-link-source.png)

Kendi sıradan rota ya da kişi sayfasını ziyaret edin `/Contact`, veya özel bir rota `/TheContactPage`. Ek bir sağlarsanız `text` yol kesimi sayfası gösterir HTML ile kodlanmış segment sağlamanızı:

![URL'deki 'MetinDeğeri' bir isteğe bağlı 'text' yol kesimi sağladığını edge tarayıcı örneği. İşlenen sayfanın 'text' segmenti değeri gösterir.](razor-pages-convention-features/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Sayfa modeli eylem kuralları

Uygulayan varsayılan sayfa model sağlayıcısı [IPageApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelprovider) sayfa modelleri yapılandırmak için genişletilebilirlik noktaları sağlamak için tasarlanmış kuralları çağırır. Bu kuralları derleme ve sayfa bulma ve işleme özelliklerini değiştirme olduğu durumlarda faydalıdır.

Bu bölümdeki örnekler için örnek uygulamayı kullanan bir `AddHeaderAttribute` olan sınıf bir [ResultFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.resultfilterattribute), bir yanıt üstbilgisi geçerlidir:

[!code-csharp[](razor-pages-convention-features/sample/Filters/AddHeader.cs?name=snippet1)]

Kuralları kullanarak, örnek bir klasördeki tüm sayfalar için ve tek bir sayfayla öznitelik uygulamak nasıl gösterir.

**Klasör uygulama modeli kuralı**

Kullanım [AddFolderApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addfolderapplicationmodelconvention) oluşturmak ve eklemek için bir [IPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelconvention) , çağıran bir eylem üzerinde [PageApplicationModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageapplicationmodel) örnekler Belirtilen klasör altındaki tüm sayfalar.

Örnek kullanımını gösteren `AddFolderApplicationModelConvention` üst bilgi ekleyerek `OtherPagesHeader`, içinde sayfalara *OtherPages* uygulamanın klasörü:

[!code-csharp[](razor-pages-convention-features/sample/Startup.cs?name=snippet6)]

Örnek 's Page1 sayfanın isteği `localhost:5000/OtherPages/Page1` ve sonuçları görüntülemek için üstbilgileri inceleyin:

![Yanıt üstbilgilerini OtherPages/Page1 sayfasının OtherPagesHeader eklenmiş olduğunu gösterir.](razor-pages-convention-features/_static/page1-otherpages-header.png)

**Sayfa uygulama modeli kuralı**

Kullanım [AddPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addpageapplicationmodelconvention) oluşturmak ve eklemek için bir [IPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelconvention) , çağıran bir eylem üzerinde [PageApplicationModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageapplicationmodel) sayfası speciifed adıyla.

Örnek kullanımını gösteren `AddPageApplicationModelConvention` üst bilgi ekleyerek `AboutHeader`, hakkında sayfasına:

[!code-csharp[](razor-pages-convention-features/sample/Startup.cs?name=snippet7)]

Örnek ait hakkında sayfanın isteği `localhost:5000/About` ve sonuçları görüntülemek için üstbilgileri inceleyin:

![Yanıt Üstbilgileri hakkında sayfasının AboutHeader eklenmiş olduğunu gösterir.](razor-pages-convention-features/_static/about-page-about-header.png)

**Bir filtre yapılandırın**

[ConfigureFilter](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.configurefilter) belirtilen filtre uygulamak için yapılandırır. Bir filtre sınıf uygulayabilirsiniz, ancak örnek uygulaması Perde Arkası bir filtre döndüren bir Fabrika olarak uygulanan bir lambda ifadesinde bir filtre uygulamak gösterilmektedir:

[!code-csharp[](razor-pages-convention-features/sample/Startup.cs?name=snippet8)]

Sayfa uygulama modeli Page2 sayfası yol kesimleri için göreli yol denetlemek için kullanılır *OtherPages* klasör. Koşul geçerse, bir başlığı eklenir. Aksi takdirde, `EmptyFilter` uygulanır.

`EmptyFilter` olan bir [eylem filtresi](xref:mvc/controllers/filters#action-filters). Eylem filtreleri Razor sayfalarının tarafından göz ardı edilir beri `EmptyFilter` no-ops yolu içermiyor, beklendiği gibi `OtherPages/Page2`.

Örnek 's Page2 sayfanın isteği `localhost:5000/OtherPages/Page2` ve sonuçları görüntülemek için üstbilgileri inceleyin:

![OtherPagesPage2Header Page2 için yanıta eklenir.](razor-pages-convention-features/_static/page2-filter-header.png)

**Bir filtre fabrikası yapılandırın**

[ConfigureFilter](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.configurefilter?view=aspnetcore-2.0#Microsoft_Extensions_DependencyInjection_PageConventionCollectionExtensions_ConfigureFilter_Microsoft_AspNetCore_Mvc_ApplicationModels_PageConventionCollection_System_Func_Microsoft_AspNetCore_Mvc_ApplicationModels_PageApplicationModel_Microsoft_AspNetCore_Mvc_Filters_IFilterMetadata__) uygulamak için belirtilen Üreteç yapılandırır [filtreleri](xref:mvc/controllers/filters) tüm Razor sayfalara.

Örnek uygulamayı kullanarak örneğidir bir [filtre Fabrika](xref:mvc/controllers/filters#ifilterfactory) üst bilgi ekleyerek `FilterFactoryHeader`, uygulamanın sayfalara iki değerlerle:

[!code-csharp[](razor-pages-convention-features/sample/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-convention-features/sample/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Örnek ait hakkında sayfanın isteği `localhost:5000/About` ve sonuçları görüntülemek için üstbilgileri inceleyin:

![Yanıt Üstbilgileri hakkında sayfasının iki FilterFactoryHeader üstbilgi eklendiğini gösterir.](razor-pages-convention-features/_static/about-page-filter-factory-header.png)

## <a name="replace-the-default-page-app-model-provider"></a>Varsayılan sayfa uygulama modeli sağlayıcıyı Değiştir

Razor sayfalarının kullanan `IPageApplicationModelProvider` oluşturmak için arabirimi bir [DefaultPageApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.internal.defaultpageapplicationmodelprovider). İşleyici bulma ve işlenmesi için kendi uygulama mantığı sağlamak için varsayılan model sağlayıcısından devralabilirsiniz. Varsayılan uygulama ([başvuru kaynağı](https://github.com/aspnet/Mvc/blob/rel/2.0.1/src/Microsoft.AspNetCore.Mvc.RazorPages/Internal/DefaultPageApplicationModelProvider.cs)) için kuralları kurar *adlandırılmamış* ve *adlı* aşağıda açıklanan adlandırma işleyicisi.

**Varsayılan işleyici yöntemleri adlandırılmamış**

İşleyici yöntemleri HTTP fiilleri ("adsız" işleyicisi yöntemleri) için bir kuralı uygulayın: `On<HTTP verb>[Async]` (ekleme `Async` isteğe bağlıdır, ancak zaman uyumsuz yöntemleri için önerilen).

| Adlandırılmamış işleyici yöntemi     | Çalışma                      |
| -------------------------- | ------------------------------ |
| `OnGet`/`OnGetAsync`       | Sayfa durumu başlatır.     |
| `OnPost`/`OnPostAsync`     | POST isteklerini işler.          |
| `OnDelete`/`OnDeleteAsync` | DELETE isteklerini işlemek&#8224;. |
| `OnPut`/`OnPutAsync`       | PUT isteklerini işleyecek&#8224;.    |
| `OnPatch`/`OnPatchAsync`   | Düzeltme eki istekleri işlemek&#8224;.  |

&#8224;API çağrıları sayfasına yapmak için kullanılır.

**Varsayılan işleyici yöntemleri adlı**

Benzer bir kural ("adlı işleyici yöntemleri") geliştirici tarafından sağlanan işleyici yöntemleri uygulayın. Sonra HTTP fiili veya HTTP fiili arasında işleyicisi adı görüntülenir ve `Async`: `On<HTTP verb><handler name>[Async]` (ekleme `Async` isteğe bağlıdır, ancak zaman uyumsuz yöntemleri için önerilen). Örneğin, aşağıdaki tabloda gösterilen adlandırma iletileri işleme yöntemlerini alabilir.

| İşleyici yöntemi adlı örnek             | Örnek işlemi        |
| ---------------------------------------- | ------------------------ |
| `OnGetMessage`/`OnGetMessageAsync`       | Bir ileti alın.        |
| `OnPostMessage`/`OnPostMessageAsync`     | Bir ileti gönderin.          |
| `OnDeleteMessage`/`OnDeleteMessageAsync` | İleti silme&#8224;. |
| `OnPutMessage`/`OnPutMessageAsync`       | Bir ileti&#8224;.    |
| `OnPatchMessage`/`OnPatchMessageAsync`   | Bir ileti düzeltme eki&#8224;.  |

&#8224;API çağrıları sayfasına yapmak için kullanılır.

**İşleyici yöntemi adlarını özelleştirme**

Adsız ve adlandırılmış işleyici yöntemleri adlı şeklini değiştirmek tercih ettiğiniz varsayalım. Alternatif bir adlandırma şeması yöntem adları "Açık" ile başlayan önlemek ve HTTP fiili belirlemek için ilk word segment kullanmaktır. Diğer değişiklikler yapabileceğiniz fiilleri silme için dönüştürme gibi koy ve sonrası için düzeltme eki. Bu tür bir düzeni aşağıdaki tabloda gösterilen yöntemi adlarını sağlar.

| İşleyici yöntemi                       | Çalışma                      |
| ------------------------------------ | ------------------------------ |
| `Get`                                | Sayfa durumu başlatır.     |
| `Post`/`PostAsync`                   | POST isteklerini işler.          |
| `Delete`/`DeleteAsync`               | DELETE isteklerini işlemek&#8224;. |
| `Put`/`PutAsync`                     | PUT isteklerini işleyecek&#8224;.    |
| `Patch`/`PatchAsync`                 | Düzeltme eki istekleri işlemek&#8224;.  |
| `GetMessage`                         | Bir ileti alın.              |
| `PostMessage`/`PostMessageAsync`     | Bir ileti gönderin.                |
| `DeleteMessage`/`DeleteMessageAsync` | Silmek için bir ileti gönderin.      |
| `PutMessage`/`PutMessageAsync`       | Yerleştirme için bir ileti gönderin.         |
| `PatchMessage`/`PatchMessageAsync`   | Düzeltme eki için bir ileti gönderin.       |

&#8224;API çağrıları sayfasına yapmak için kullanılır.

Bu düzen oluşturmak için devralınmalıdır `DefaultPageApplicationModelProvider` sınıfı ve geçersiz kılma [CreateHandlerModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.internal.defaultpageapplicationmodelprovider.createhandlermodel) çözmek için Özel mantık sağlamak için yöntemi [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) işleyici adları. Bunun nasıl yapılacağı gösteren örnek uygulaması, `CustomPageApplicationModelProvider` sınıfı:

[!code-csharp[](razor-pages-convention-features/sample/CustomPageApplicationModelProvider.cs?name=snippet1&highlight=1-2,45-46,64-68,78-85,87,92,106)]

Sınıf noktalar:

* Sınıfının devraldığı `DefaultPageApplicationModelProvider`.
* `TryParseHandlerMethod` HTTP fiili belirlemek için bir işleyici işler (`httpMethod`) ve adlandırılmış işleyicisi adı (`handlerName`) oluştururken `PageHandlerModel`.
  * Bir `Async` sonek göz ardı edilir, varsa.
  * Büyük/küçük harf yöntemi adından HTTP fiili ayrıştırmak için kullanılır.
  * Zaman yöntem adı (olmadan `Async`) olan HTTP fiilini adına eşit, yok adlı işleyici yok. `handlerName` Ayarlanır `null`, ve yöntem adı `Get`, `Post`, `Delete`, `Put`, veya `Patch`.
  * Zaman yöntem adı (olmadan `Async`) HTTP fiili adından uzun adlandırılmış bir işleyici yok. `handlerName` Ayarlanır `<method name (less 'Async', if present)>`. Örneğin, "GetMessage" ve "GetMessageAsync" "GetMessage" işleyici adını verim.
  * SİLME, PUT ve düzeltme eki HTTP fiilleri POST dönüştürülür.

Kayıt `CustomPageApplicationModelProvider` içinde `Startup` sınıfı:

[!code-csharp[](razor-pages-convention-features/sample/Startup.cs?name=snippet10)]

Sayfa modelinde *Index.cshtml.cs* uygulama sayfaları için sıradan işleyici yöntemi adlandırma kuralları nasıl değiştirilir gösterir. "Açık" Razor sayfalarıyla kullanılan öneki adlandırma sıradan kaldırıldı. Sayfa durumu başlatır yöntemi şimdi adlı `Get`. Bu kural sayfalar için herhangi bir sayfayı modeli açarsanız uygulama boyunca kullanılan görebilirsiniz.

Diğer yöntemlerin her biri ile işlemesi açıklar HTTP fiili başlatın. İle başlayan iki yöntem `Delete` normalde DELETE HTTP fiilleri ancak mantık olarak değerlendirilmesi `TryParseHandlerMethod` açıkça fiili POST için her iki işleyicilerini ayarlar.

Unutmayın `Async` arasında isteğe bağlı olduğu `DeleteAllMessages` ve `DeleteMessageAsync`. Her iki zaman uyumsuz yöntemleri oldukları ancak kullanmayı tercih edebileceğiniz `Async` veya sonek; bunu yapmanızı öneririz. `DeleteAllMessages` Burada gösterim amacıyla kullanılmıştır, ancak böyle bir yöntem adı öneririz `DeleteAllMessagesAsync`. İşleme etkilemez örnek 's uygulaması, ancak kullanarak `Async` zaman uyumsuz bir yöntem olduğunu olgu giden çağrıları sonek.

[!code-csharp[](razor-pages-convention-features/sample/Pages/Index.cshtml.cs?name=snippet1&highlight=1,6,16,29)]

Sağlanan işleyici adlarını not edin *Index.cshtml* eşleşen `DeleteAllMessages` ve `DeleteMessageAsync` işleyici yöntemleri:

[!code-cshtml[](razor-pages-convention-features/sample/Pages/Index.cshtml?range=29-60&highlight=7-8,24-25)]

`Async` işleyici yöntem adı `DeleteMessageAsync` tarafından out oluşturmak `TryParseHandlerMethod` yöntemi POST isteğinin işleyici eşlemesi. `asp-page-handler` Adını `DeleteMessage` işleyici yöntemi eşleşen `DeleteMessageAsync`.

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC filtreleri ve sayfa filtresi (IPageFilter)

MVC [eylem filtrelerini](xref:mvc/controllers/filters#action-filters) Razor sayfalarının işleyici yöntemleri kullandığından Razor sayfalarının tarafından göz ardı edilir. MVC filtreleri diğer türlerini kullanmak için kullanılabilir: [yetkilendirme](xref:mvc/controllers/filters#authorization-filters), [özel durum](xref:mvc/controllers/filters#exception-filters), [kaynak](xref:mvc/controllers/filters#resource-filters), ve [sonuç](xref:mvc/controllers/filters#result-filters). Daha fazla bilgi için bkz: [filtreleri](xref:mvc/controllers/filters) konu.

Sayfa filtresi ([IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter)) Razor sayfaya uygulanan bir filtredir. Bir sayfa işleyici yönteminin yürütülmesi çevreleyen. Özel kod sayfası işleyici yöntemi yürütme aşamalarında işlemenize olanak sağlar. Örnek uygulamadan örnek aşağıda verilmiştir:

[!code-csharp[](razor-pages-convention-features/sample/Filters/ReplaceRouteValueFilterAttribute.cs?name=snippet1)]

Bu filtre denetler bir `globalTemplate` "ReplacementValue içinde" değeri "TriggerValue" ve takasları yol.

`ReplaceRouteValueFilter` Özniteliği doğrudan uygulanabilir bir `PageModel`:

[!code-csharp[](razor-pages-convention-features/sample/Pages/OtherPages/Page3.cshtml.cs?range=10-12&highlight=1)]

İle örnek uygulamasından Page3 sayfa isteği `localhost:5000/OtherPages/Page3/TriggerValue`. Filtre rota değeri nasıl değiştirir dikkat edin:

![Rota değerini ReplacementValue ile değiştirerek filtresi bir TriggerValue yol kesimi sonuçlarla OtherPages/Page3 isteyin.](razor-pages-convention-features/_static/otherpages-page3-filter-replacement-value.png)

## <a name="see-also"></a>Ayrıca bkz.

* [Razor sayfalarının yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization)
