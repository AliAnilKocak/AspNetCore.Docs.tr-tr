---
title: "ASP.NET Core Razor sayfalarında giriş"
author: Rick-Anderson
description: "Razor sayfalarında ASP.NET Core Öğreticisi. MVC çekirdek, ASP.NET Core içerir 2.x, web geliştirme ve Visual Studio 2017 giriş. Bu belge sayfası odaklı senaryoları geliştirme kolaylığı için ASP.NET Core Razor sayfalarını kullanarak genel bir bakış sağlar."
manager: wpickett
ms.author: riande
ms.date: 09/12/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: mvc/razor-pages/index
ms.openlocfilehash: c16959189448337b12dc4902300516b25c200eee
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/30/2018
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>ASP.NET Core Razor sayfalarında giriş

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan Nowak](https://github.com/rynowak)

Razor sayfalarının sayfa odaklı senaryoları daha kolay ve daha üretken kodlama yapar ASP.NET Core MVC yeni özelliğidir.

Model-View-Controller yaklaşım kullanan bir öğretici için arıyorsanız bkz [ASP.NET Core MVC'ye Başlarken](xref:tutorials/first-mvc-app/start-mvc).

Bu belge Razor sayfalarının tanıtılmaktadır. Adım adım öğretici değil. Bazı bölümleri izleyin zor görürseniz, bkz: [Razor sayfalarının ile çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).

<a name="prerequisites"></a>

## <a name="aspnet-core-20-prerequisites"></a>ASP.NET Core 2.0 önkoşulları

Yükleme [.NET Core](https://www.microsoft.com/net/core) 2.0.0 veya sonraki bir sürümü.

Visual Studio kullanıyorsanız, yükleme [Visual Studio](https://www.visualstudio.com/vs/) 2017 15.3 veya aşağıdaki iş yükleri ile sonraki bir sürümü:

* **ASP.NET ve web geliştirme**
* **.NET core platformlar arası geliştirme**

<a name="rpvs17"></a>

## <a name="creating-a-razor-pages-project"></a>Razor sayfalarının proje oluşturma

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Bkz: [Razor sayfalarının ile çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) Razor sayfalarının oluşturma konusunda ayrıntılı yönergeler için Proje Visual Studio kullanarak.

#   <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Çalıştırma `dotnet new razor` komut satırından.

Oluşturulan açmak *.csproj* Visual Studio dosyasından Mac için

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

Çalıştırma `dotnet new razor` komut satırından.

#   <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli) 

Çalıştırma `dotnet new razor` komut satırından.

---

## <a name="razor-pages"></a>Razor sayfalarının

Razor sayfalarının etkinleşir *haline*:

[!code-cs[main](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Temel bir sayfa göz önünde bulundurun:<a name="OnGet"></a>

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Önceki kod çok Razor görünüm dosyası gibi görünüyor. Neler farklı kılan unsurdur `@page` yönergesi. `@page`Dosya, isteklerini doğrudan bir denetleyici geçmeden işleme anlamına gelir ve MVC eylemi - içine yapar. `@page`ilk Razor yönergesi bir sayfa üzerinde olmalıdır. `@page`diğer Razor yapıları davranışını etkiler.

Benzer bir sayfa, kullanarak bir `PageModel` sınıfında, aşağıdaki iki dosyada gösterilir. *Pages/Index2.cshtml* dosyası:

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

*Pages/Index2.cshtml.cs* sayfa modeli:

[!code-cs[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Kural tarafından `PageModel` sınıf dosyası Razor sayfa dosyası ile aynı ada sahip *.cs* eklenir. Örneğin, önceki Razor sayfasıdır *Pages/Index2.cshtml*. Dosyayı içeren `PageModel` sınıfı adlandırılan *Pages/Index2.cshtml.cs*.

URL yollarını sayfalara ilişkilendirmelerini dosya sisteminde sayfanın konuma göre belirlenir. Aşağıdaki tablo Razor sayfasının yolunu ve eşleşen URL gösterir:

| Dosya adı ve yolu               | URL eşleştirme |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/`veya`/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store`veya`/Store/Index` |

Notlar:

* Razor sayfalarının dosyalarında çalışma zamanı arar *sayfaları* varsayılan klasör.
* `Index`bir URL bir sayfa içermediğinde varsayılan sayfasıdır.

## <a name="writing-a-basic-form"></a>Temel form yazma

Razor sayfalarının özellikleri ortak desenler web tarayıcılarıyla birlikte kullanılan kolay hale getirmek üzere tasarlanmıştır. [Model bağlama](xref:mvc/models/model-binding), [etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML Yardımcıları tüm *yalnızca iş* Razor sayfasını sınıfında tanımlanan özelliklere sahip. "Bize başvurun" form için temel bir uygulayan bir sayfa göz önünde bulundurun `Contact` modeli:

Bu belgede, örnekleri için `DbContext` içinde başlatılan [haline](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) dosya.

[!code-cs[main](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Veri modeli:

[!code-cs[main](index/sample/RazorPagesContacts/Data/Customer.cs)]

Db bağlamı:

[!code-cs[main](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

*Pages/Create.cshtml* dosyasını görüntüle:

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

*Pages/Create.cshtml.cs* sayfa modeli:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Kural tarafından `PageModel` sınıfı çağrıldığında `<PageName>Model` ve sayfa aynı ad.

`PageModel` Sınıfı, bir sayfa mantığı ayrılması kendi sunudan olanak tanır. Sayfaya gönderilen istekleri ve sayfayı oluşturmak için kullanılan verileri için sayfa işleyiciler tanımlar. Bu ayrım aracılığıyla sayfa bağımlılıklar yönetmenize olanak sağlayan [bağımlılık ekleme](xref:fundamentals/dependency-injection) ve [birim testi](xref:testing/razor-pages-testing) sayfaları.

Sayfasına sahip bir `OnPostAsync` *işleyici yöntemi*, üzerinde çalıştığı `POST` ister (kullanıcı formu gönderdiğinde). Herhangi bir HTTP fiil için işleyici yöntemleri ekleyebilirsiniz. En yaygın işleyicileri şunlardır:

* `OnGet`sayfa için gereken durumu başlatılamadı. [OnGet](#OnGet) örnek.
* `OnPost`Form Gönderme işlemek için.

`Async` Adlandırma soneki isteğe bağlıdır ancak genellikle kurala göre zaman uyumsuz işlevleri için kullanılır. `OnPostAsync` Önceki örnekte kod ne, normalde bir denetleyicisi yazarsınız için benzer görünür. Önceki kod Razor sayfalar için normaldir. MVC elemanlar çoğu ister [model bağlama](xref:mvc/models/model-binding), [doğrulama](xref:mvc/models/validation), ve eylem sonuçlarını paylaşıldığı.  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

Önceki `OnPostAsync` yöntemi:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Temel akışı `OnPostAsync`:

Doğrulama hataları denetleyin.

*  Herhangi bir hata varsa, verileri Kaydet ve yeniden yönlendirme.
*  Hatalar varsa, doğrulama iletileri sayfasıyla yeniden gösterir. İstemci tarafı doğrulama geleneksel ASP.NET Core MVC uygulamaları için aynıdır. Çoğu durumda, doğrulama hataları istemcide algılanabilir ve hiçbir zaman sunucuya gönderildi.

Veri başarıyla girildiğinde `OnPostAsync` işleyici yöntem çağrılarını `RedirectToPage` bir örneğini döndürmek için yardımcı yöntem `RedirectToPageResult`. `RedirectToPage`Yeni eylem sonucu, benzer olduğunu `RedirectToAction` veya `RedirectToRoute`, ancak özelleştirilmiş sayfalar için. İsteğe bağlı olarak önceki örnekte kök dizin sayfasına yönlendiren (`/Index`). `RedirectToPage`içinde ayrıntılı [sayfaları için URL oluşturma](#url_gen) bölümü.

Teslim edilen formu (yani sunucuya geçirilir) doğrulama hataları olduğunda`OnPostAsync` işleyici yöntem çağrılarını `Page` yardımcı yöntemi. `Page`örneğini döndürür `PageResult`. Döndürme `Page` nasıl denetleyicileri Eylemler döndürmek için benzer `View`. `PageResult`Varsayılan değer <!-- Review  --> dönüş türü için bir işleyici yöntemi. Döndüren bir işleyici yöntemi `void` sayfasını işler.

`Customer` Özelliğini kullanan `[BindProperty]` model bağlama için kabul özniteliği.

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor sayfalarının varsayılan olarak, GET olmayan fiiller yalnızca özelliklerle bağlayın. Bağlama özellikleri için kod yazmak zorunda miktarını azaltır. Bağlama form alanları oluşturmak için aynı özelliği kullanarak kod azaltır (`<input asp-for="Customer.Name" />`) ve giriş kabul edin.

Giriş sayfası (*Index.cshtml*):

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

Arka plan kod *Index.cshtml.cs* dosyası:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

*Index.cshtml* dosyası her kişi için bir düzenleme bağlantısı oluşturmak için aşağıdaki biçimlendirme içerir:

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

[Yer işareti etiketi yardımcı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) kullanılan `asp-route-{value}` düzenleme sayfasını bir bağlantı oluşturmak için öznitelik. Rota verilerini kişiyle bağlantıyı içeren kimliği Örneğin, `http://localhost:5000/Edit/1`.

*Pages/Edit.cshtml* dosyası:

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

İlk satır içerir `@page "{id:int}"` yönergesi. Yönlendirme kısıtlaması`"{id:int}"` içeren sayfa isteklerini kabul etmek için sayfayı söyler `int` rota verileri. Sayfa için bir istek dönüştürülebilir rota verileri içermiyorsa, bir `int`, çalışma zamanı (bulunamadı) HTTP 404 hatası döndürür.

*Pages/Edit.cshtml.cs* dosyası:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

*Index.cshtml* dosya de Sil düğmesini her müşteri irtibat için oluşturmak için biçimlendirme içerir:

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Sil düğmesini HTML olarak işlendiğinde, `formaction` parametreleri içerir:

* Müşteri tarafından belirtilen kimliği başvurun `asp-route-id` özniteliği.
* `handler` Tarafından belirtilen `asp-page-handler` özniteliği.

Bir müşteri ile işlenen Sil düğmesini bir örneği burada verilmiştir Kimliğini başvurun `1`:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Düğme seçildiğinde, bir form `POST` isteği sunucuya gönderilir. Kurala göre işleyicisi yönteminin adı seçili değerine göre `handler` parametre düzeni göre `OnPost[handler]Async`.

Çünkü `handler` olan `delete` Bu örnekte, `OnPostDeleteAsync` işleyici yöntemi kullanılır işleme `POST` isteği. Varsa `asp-page-handler` gibi farklı bir değere ayarlanmış `remove`, ada sahip bir sayfa işleyici yöntemi `OnPostRemoveAsync` seçilir.

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

`OnPostDeleteAsync` Yöntemi:

* Kabul `id` Sorgu dizesinden.
* Müşteri irtibat ile veritabanını sorgular `FindAsync`.
* Müşteri irtibat bulunursa, müşteri kişiler listesinden kaldırıldı. Veritabanı güncelleştirilir.
* Çağrıları `RedirectToPage` kök dizin sayfasına yeniden yönlendirmek için (`/Index`).

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF ve Razor sayfaları

Herhangi bir kod yazmak zorunda değilsiniz [antiforgery doğrulama](xref:security/anti-request-forgery). Otomatik olarak antiforgery belirteci oluşturma ve doğrulama Razor sayfalarında dahil edilir.

<a name="layout"></a>
## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Düzenleri, kısmi, şablonları ve etiket Yardımcıları Razor Pages ile kullanma

Sayfaları tüm özellikleri Razor görüntüleme altyapısı ile çalışmaz. Düzenleri, kısmi, şablonları, etiket Yardımcıları *_ViewStart.cshtml*, *_viewımports.cshtml* iş için geleneksel Razor görünümleri yaparlar aynı şekilde.

Şimdi bu sayfa bu özelliklerden bazıları yararlanarak declutter.

Ekleme bir [düzen sayfası](xref:mvc/views/layout) için *Pages/_Layout.cshtml*:

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

[Düzeni](xref:mvc/views/layout):

* (Sayfa düzeni dışında çevrilir sürece) her sayfanın düzenini denetler.
* JavaScript ve stil sayfalarını gibi HTML yapıları alır.

Bkz: [düzen sayfası](xref:mvc/views/layout) daha fazla bilgi için.

[Düzeni](xref:mvc/views/layout#specifying-a-layout) özelliği ayarlanmış *Pages/_ViewStart.cshtml*:

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

**Not:** düzeni bulunduğu *sayfaları* klasör. Sayfaları başka görünümlerini (düzenleri, şablonlar, kısmi), geçerli sayfa ile aynı klasörde başlangıç hiyerarşik olarak arayın. Bir düzende *sayfaları* klasörü altında herhangi bir Razor sayfadan kullanılabilir *sayfaları* klasör.

Öneririz **değil** Düzen dosyası içine *görünümler/paylaşılan* klasör. *Görünümler/paylaşılan* bir MVC görünümleri deseni. Razor sayfalarının klasör hiyerarşisi, yol kuralları yararlanmayı yöneliktir.

Bir Razor sayfa görünümü aramadan içerir *sayfaları* klasör. Düzenleri, şablonları ve MVC denetleyicileri ve geleneksel Razor görünümleri ile kullanmakta olduğunuz kısmi *yalnızca iş*.

Ekleme bir *Pages/_ViewImports.cshtml* dosyası:

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`Daha sonra öğreticide açıklanmıştır. `@addTagHelper` Yönergesi getirir [yerleşik etiket Yardımcıları](xref:mvc/views/tag-helpers/builtin-th/Index) tüm sayfalar için *sayfaları* klasör.

<a name="namespace"></a>

Zaman `@namespace` yönergesi açıkça bir sayfada kullanılır:

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Yönergesi sayfa için ad alanı ayarlar. `@model` Yönergesi olmayan ad alanı içerecek şekilde gerekir.

Zaman `@namespace` yönergesi bulunduğu *_viewımports.cshtml*, belirtilen ad alanı içe aktaran sayfasındaki oluşturulan ad alanı öneki sağlayan `@namespace` yönergesi. Oluşturulan ad alanı (soneki bölümü) kalanı içeren klasör arasında nokta ayrılmış göreli yol olup *_viewımports.cshtml* ve sayfayı içeren klasör.

Örneğin, dosyanın arkasındaki kod *Pages/Customers/Edit.cshtml.cs* ad alanını açıkça ayarlar:

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

*Pages/_ViewImports.cshtml* dosyasını ayarlar aşağıdaki ad alanı:

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Oluşturulan ad alanı için *Pages/Customers/Edit.cshtml* Razor sayfasını aynıdır dosyanın arkasındaki kod. `@namespace` Yönergesi tasarlandığı bir proje ve sayfaları üretilen kod için C# sınıfları eklenmiş şekilde *yalnızca iş* eklemek zorunda kalmadan bir `@using` dosyanın arkasındaki kod için yönerge.

**Not:** `@namespace` geleneksel Razor görünümleri ile de çalışır.

Özgün *Pages/Create.cshtml* dosyasını görüntüle:

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Güncelleştirilmiş *Pages/Create.cshtml* dosyasını görüntüle:

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

[Razor sayfalarının başlangıç projesi](#rpvs17) içeren *Pages/_ValidationScriptsPartial.cshtml*, istemci tarafı doğrulama kanca oluşturur.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Sayfaları için URL oluşturma

`Create` Sayfasında, daha önce kullandığı gösterilen `RedirectToPage`:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Uygulama, aşağıdaki dosya/klasör yapısını sahiptir:

* */ Sayfaları*

  * *Index.cshtml*
  * */ Müşteri*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

*Pages/Customers/Create.cshtml* ve *Pages/Customers/Edit.cshtml* sayfaları yeniden yönlendirmek için *Pages/Index.cshtml* başarı sonra. Dize `/Index` önceki sayfaya erişmek için URI bir parçasıdır. Dize `/Index` için URI oluşturmak için kullanılan *Pages/Index.cshtml* sayfası. Örneğin:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Sayfa adı sayfasına kökünden yoludur */sayfaları* klasörü (başında dahil olmak üzere `/`, örneğin `/Index`). Önceki URL nesil yalnızca cmdlet'e kod bir URL daha çok daha zengin örneklerdir. URL oluşturma kullanan [yönlendirme](xref:mvc/controllers/routing) ve oluşturmak ve yol hedef yolunda nasıl tanımlandığına göre parametreleri kodlayın.

Göreli adlar sayfaları için URL oluşturmayı destekler. Aşağıdaki tabloda hangi dizin sayfası ile farklı seçildiğini gösterir `RedirectToPage` parametrelerinden *Pages/Customers/Create.cshtml*:

| RedirectToPage(x)| Sayfa |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Sayfa/dizini* |
| RedirectToPage("./Index"); | *Müşteriler/sayfalar/dizini* |
| RedirectToPage(".. / Dizin") | *Sayfa/dizini* |
| RedirectToPage("Index")  | *Müşteriler/sayfalar/dizini* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")`, ve `RedirectToPage("../Index")` olan *göreli adlar*. `RedirectToPage` Parametresi *birleştirilmiş* ile hedef sayfanın adını işlem için geçerli sayfasının yolu.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page. -- page name, not page path -->

Göreli adı bağlama karmaşık bir yapıyı siteleriyle oluştururken yararlıdır. Bir klasördeki sayfaları arasında bağlamak için göreli adları kullanıyorsa, bu klasörü yeniden adlandırabilirsiniz. Tüm bağlantılar hala çalışır, (bunlar klasör adı eklemediniz çünkü).

## <a name="tempdata"></a>TempData

ASP.NET Core sunan [TempData](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) özelliği bir [denetleyicisi](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller). Bu özellik, dosyayı okuma kadar verileri depolar. `Keep` Ve `Peek` yöntemleri, verileri silme olmadan incelemek için kullanılabilir. `TempData`birden çok tek bir istek için veri gerektiğinde yeniden yönlendirme için yararlıdır.

`[TempData]` Özniteliği ASP.NET Core 2. 0 ' yenidir ve denetleyicileri ve sayfaları desteklenir.

Aşağıdaki kodu değerini ayarlar `Message` kullanarak `TempData`:

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Aşağıdaki biçimlendirmede *Pages/Customers/Index.cshtml* dosyasını değerini görüntüler `Message` kullanarak `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

*Pages/Customers/Index.cshtml.cs* sayfa modeli uygular `[TempData]` özniteliğini `Message` özelliği.

```cs
[TempData]
public string Message { get; set; }
```

Bkz: [TempData](xref:fundamentals/app-state#temp) daha fazla bilgi için.

<a name="mhpp"></a>
## <a name="multiple-handlers-per-page"></a>Sayfa başına birden çok işleyicileri

İki kullanarak işleyicileri sayfa için aşağıdaki sayfayı biçimlendirmeleri oluşturur `asp-page-handler` etiket Yardımcısı:

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Önceki örnekte form iki düğmeleri, her kullanarak gönderme sahip `FormActionTagHelper` farklı bir URL'ye göndermek için. `asp-page-handler` Özniteliği için bir yardımcı olan `asp-page`. `asp-page-handler`bir sayfa tarafından tanımlanan işleyici yöntemlerin her biri için gönderme URL'leri oluşturur. `asp-page`Örnek geçerli sayfasına bağlantılandırma çünkü belirtilmedi.

Sayfa modeli:

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Önceki kod kullanan *işleyici yöntemleri adlı*. Adlandırılmış işleyici yöntemleri adından sonra metin gerçekleştirerek oluşturulur `On<HTTP Verb>` ve önce `Async` (varsa). Önceki örnekte OnPost sayfa yöntemlerdir**JoinList**zaman uyumsuz ve OnPost**JoinListUC**zaman uyumsuz. İle *OnPost* ve *zaman uyumsuz* kaldırıldı, işleyici adlardır `JoinList` ve `JoinListUC`.

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Yukarıdaki kullanan kod, gönderildiği URL yolunu `OnPostJoinListAsync` olan `http://localhost:5000/Customers/CreateFATH?handler=JoinList`. Gönderildiği URL yolunu `OnPostJoinListUCAsync` olan `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.

## <a name="customizing-routing"></a>Yönlendirme özelleştirme

Sorgu dizesi hoşlanmıyorsanız `?handler=JoinList` URL'de işleyicisi adı URL'nin yol kısmı koymak için rota değiştirebilirsiniz. Bir rota şablonu sonra çift tırnak içine ekleyerek rota özelleştirebilirsiniz `@page` yönergesi.

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Önceki yol işleyicisi adı ve URL yolunu sorgu dizesi yerine koyar. `?` Aşağıdaki `handler` rota parametresinin isteğe bağlı olduğu anlamına gelir.

Kullanabileceğiniz `@page` ek kesimleri ve parametreleri bir sayfanın rotaya eklemek için. Ne olursa olsun sahip **eklenmiş** sayfasının varsayılan yol için. Sayfanın yolu değiştirmek için bir mutlak ya da sanal yolu kullanarak (gibi `"~/Some/Other/Path"`) desteklenmiyor.

## <a name="configuration-and-settings"></a>Yapılandırma ve ayarları

Gelişmiş seçenekleri yapılandırmak için genişletme yöntemi kullanmak `AddRazorPagesOptions` MVC oluşturucu üzerinde:

[!code-cs[main](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Şu anda kullanabileceğiniz `RazorPagesOptions` sayfaları için kök dizini ayarlayın veya sayfaları için uygulama modeli kuralları eklemek için. Gelecekte bu şekilde daha fazla genişletilebilirlik etkinleştirme.

Görünümleri derleneceği bkz [Razor görünüm derleme](xref:mvc/views/view-compilation) .

[İndirme veya görüntüleme örnek kod](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/index/sample).

Bkz: [ASP.NET Core Razor sayfalarında ile çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start), ilgili bu girişi oluşturur.

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Razor sayfalarının içerik kök dizininde olduğunu belirtin

Razor sayfalarının olsunlar varsayılan olarak, */sayfaları* dizin. Ekleme [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) için [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) Razor sayfalarınızı içerik kök dizininde olduğunu belirtmek için ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) uygulamasının:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Razor sayfalarının özel kök dizininde olduğunu belirtin

Ekleme [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) için [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) Razor sayfalarınızı uygulamasında özel kök dizininde olduğunu belirtmek için (göreli bir yol belirtin):

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="see-also"></a>Ayrıca bkz.

* [Razor Sayfaları kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start)
* [Razor sayfalarının yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization)
* [Razor sayfalarının özel yolu ve sayfayı model sağlayıcıları](xref:mvc/razor-pages/razor-pages-convention-features)
* [Razor sayfalarının birim ve tümleştirme sınaması](xref:testing/razor-pages-testing)
