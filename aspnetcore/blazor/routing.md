---
title: ASP.NET Core Blazor yönlendirme
author: guardrex
description: Uygulamalardaki istekleri yönlendirme ve gezinti bağlantısı bileşeni hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/routing
ms.openlocfilehash: ccc8231d1925d4a55eeef618800c10652f9ae36d
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211663"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor yönlendirme

Tarafından [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

İsteklerin nasıl yönlendirileceğini ve Blazor uygulamalarında gezinti bağlantıları oluşturmak için `NavLink` bileşenin nasıl kullanılacağını öğrenin.

## <a name="aspnet-core-endpoint-routing-integration"></a>Uç nokta yönlendirme tümleştirmesi ASP.NET Core

Blazor sunucusu [ASP.NET Core uç nokta yönlendirme](xref:fundamentals/routing)ile tümleşiktir. ASP.NET Core bir uygulama, `MapBlazorHub` içindeki `Startup.Configure`etkileşimli bileşenler için gelen bağlantıları kabul edecek şekilde yapılandırılmıştır:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

En yaygın yapılandırma, tüm istekleri Razor sayfasına yönlendirmesidir ve bu, Blazor Server uygulamasının sunucu tarafı bölümü için ana bilgisayar görevi görür. Kural gereği, *ana bilgisayar* sayfası genellikle *_host. cshtml*olarak adlandırılır. Ana bilgisayar dosyasında belirtilen yol, yol eşleştirilirken düşük bir öncelik ile çalıştığından bir *geri dönüş yolu* olarak adlandırılır. Geri dönüş yolu, diğer yollar eşleşmediği zaman kabul edilir. Bu, uygulamanın Blazor sunucu uygulamasıyla kesintiye uğramadan diğer denetleyicileri ve sayfaları kullanmasına izin verir.

## <a name="route-templates"></a>Rota şablonları

Bileşeni `Router` , belirtilen bir rota ile her bileşene yönlendirmeyi sağlar. Bileşen App. Razor dosyasında görünür: `Router`

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Bir`@page` yönergeyle bir *. Razor* dosyası derlendiğinde, oluşturulan sınıf, yol şablonunu belirten bir <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> olarak sağlanır.

Çalışma zamanında, `RouteView` bileşen:

* ' İ istediğiniz parametrelerle birlikte alır. `Router` `RouteData`
* Belirtilen parametreleri kullanarak belirtilen bileşeni düzeniyle (veya isteğe bağlı bir varsayılan düzende) işler.

İsteğe bağlı olarak, bir `DefaultLayout` düzen belirtmeyen bileşenler için kullanılacak düzen sınıfıyla bir parametre belirtebilirsiniz. Varsayılan Blazor şablonları `MainLayout` bileşeni belirler. *Mainlayout. Razor* , şablon projenin *paylaşılan* klasöründedir. Düzenler hakkında daha fazla bilgi için bkz <xref:blazor/layouts>.

Birden çok yol şablonu, bir bileşene uygulanabilir. Aşağıdaki bileşen ve `/BlazorRoute` `/DifferentBlazorRoute`için isteklere yanıt verir:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> URL 'lerin doğru şekilde çözülmesi için `<base>` , uygulamanın, `href` özniteliğinde belirtilen uygulama temel yolu ile *Wwwroot/index.html* File (Blazor webassembly) veya *Pages/_host. cshtml* dosyasında (Blazor Server) bir etiketi içermesi gerekir (`<base href="/">`). Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>İçerik bulunamadığında özel içerik sağla

`Router` Bileşen, istenen rota için içerik bulunmazsa uygulamanın özel içerik belirtmesini sağlar.

*App. Razor* dosyasında, `NotFound` `Router` bileşenin Şablon parametresinde özel içerik ayarlayın:

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

`<NotFound>` Etiketlerin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir. `NotFound` İçeriğe varsayılan bir düzen uygulamak için bkz <xref:blazor/layouts>.

## <a name="route-to-components-from-multiple-assemblies"></a>Birden çok derlemeden bileşenlere rota

Bileşen için yönlendirilebilir bileşenleri ararken dikkate alınması gereken ek derlemeleri belirtmek için parametresinikullanın.`AdditionalAssemblies` `Router` Belirtilen derlemeler, `AppAssembly`belirtilen derlemeye ek olarak değerlendirilir. Aşağıdaki örnekte, `Component1` başvurulan bir sınıf kitaplığında tanımlanan yönlendirilebilir bir bileşendir. Aşağıdaki `AdditionalAssemblies` örnek, için `Component1`yönlendirme desteği ile sonuçlanır:

< yönlendirici AppAssembly = "typeof (program). Derleme "AdditionalAssemblies =" New [] {typeof (Component1). Bütünleştirilmiş kod} >...</Router>

## <a name="route-parameters"></a>Rota parametreleri

Yönlendirici, karşılık gelen bileşen parametrelerini aynı ada (büyük/küçük harfe duyarsız) doldurmak için yol parametrelerini kullanır:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

ASP.NET Core 3,0 önizlemesinde Blazor uygulamaları için isteğe bağlı parametreler desteklenmez. Önceki `@page` örnekte iki yönergeler uygulanır. İlki, bir parametre olmadan bileşene gezinmesine izin verir. İkinci `@page` yönerge, `{text}` yol parametresini alır ve değeri `Text` özelliğine atar.

## <a name="route-constraints"></a>Yol kısıtlamaları

Yol kısıtlaması bir yönlendirme segmentinde bir bileşene tür eşleştirmeyi zorlar.

Aşağıdaki örnekte, `Users` bileşen yolu yalnızca şu durumlarda eşleşir:

* İstek `Id` URL 'sinde bir yol kesimi var.
* Segment bir tamsayıdır (`int`). `Id`

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Aşağıdaki tabloda gösterilen yol kısıtlamaları mevcuttur. Sabit kültür ile eşleşen yol kısıtlamaları için daha fazla bilgi için tablonun altındaki uyarıya bakın.

| Kısıtlaması | Örnek           | Örnek eşleşmeler                                                                  | Bilmesi<br>kültür<br>eşleştirme |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Hayır                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Evet                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Evet                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Evet                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Evet                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Hayır                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Evet                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Evet                              |

> [!WARNING]
> URL 'yi doğrulayan ve bir clr türüne ( `int` veya `DateTime`gibi) dönüştürülen yol kısıtlamaları, her zaman sabit kültürü kullanır. Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar.

### <a name="routing-with-urls-that-contain-dots"></a>Noktalar içeren URL 'lerle yönlendirme

Blazor Server uygulamalarında, *_Host. cshtml* `/` içindeki varsayılan yol (`@page "/"`). URL bir dosya isteyecek şekilde göründüğünden, nokta`.`() içeren bir istek URL 'si varsayılan yol tarafından eşleşmiyor. Bir Blazor uygulaması mevcut olmayan bir statik dosya için *404-Found* yanıtı döndürür. Bir nokta içeren yolları kullanmak için *_Host. cshtml* 'yi aşağıdaki yol şablonuyla yapılandırın:

```cshtml
@page "/{**path}"
```

`"/{**path}"` Şablon şunları içerir:

* Ters eğik çizgi (`/`) kodlaması olmadan birden`**`çok klasör sınırlarındaki yolu yakalamak için çift yıldız *catch-all* söz dizimi ().
* `path` Rota parametresi adı.

Daha fazla bilgi için bkz. <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Gezinti bağlantısı bileşeni

Gezinti bağlantıları `NavLink` oluştururken, HTML köprü öğelerinin (`<a>`) yerine bir bileşen kullanın. Bir `NavLink` bileşen `<a>` ,geçerli`href` URL ile eşleşip eşleşmediğini temel alarak bir CSSsınıfınageçişyaptığısürecebiröğesigibidavranır.`active` `active` Sınıfı, bir kullanıcının hangi sayfanın etkin sayfa olduğunu anladığı gezinti bağlantıları arasında yardımcı olur.

Aşağıdaki `NavMenu` bileşen, bileşenlerin nasıl [](https://getbootstrap.com/docs/) kullanılacağını `NavLink` gösteren bir önyükleme gezinti çubuğu oluşturur:

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Öğesinin özniteliğine atayabilmeniz için kullanabileceğiniz iki `NavLinkMatch` `Match`seçenekvardır `<NavLink>` :

* `NavLinkMatch.All`Tüm geçerli URL ile eşleştiğinde etkin olur.`NavLink` &ndash;
* `NavLinkMatch.Prefix`(*varsayılan*) Geçerli URL 'nin herhangi bir önekiyle eşleştiğinde etkin olur.`NavLink` &ndash;

`NavLink` Yukarıdaki örnekte, ana `href=""` giriş `active` URL 'siyle eşleşir ve yalnızca uygulamanın varsayılan temel yol URL 'sindeki CSS sınıfını alır (örneğin, `https://localhost:5001/`). `NavLink` İkincisi, Kullanıcı ön `active` eki olan herhangi bir `MyComponent` URL 'yi ziyaret ettiğinde sınıfı alır (örneğin, `https://localhost:5001/MyComponent` ve `https://localhost:5001/MyComponent/AnotherSegment`).

Ek `NavLink` bileşen öznitelikleri, işlenen tutturucu etiketine geçirilir. Aşağıdaki örnekte, `NavLink` bileşen `target` özniteliğini içerir:

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Aşağıdaki HTML biçimlendirmesi işlenir:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI ve gezinti durumu yardımcıları

Kod `Microsoft.AspNetCore.Components.NavigationManager` içinde C# URI ve gezinme ile çalışmak için kullanın. `NavigationManager`Aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.

| Üye | Açıklama |
| ------ | ----------- |
| `Uri` | Geçerli mutlak URI 'yi alır. |
| `BaseUri` | Mutlak bir URI oluşturmak için göreli URI yollarına eklenebilir olan temel URI 'yi (sondaki eğik çizgiyle birlikte) alır. Genellikle, `BaseUri` *Wwwroot/index.html* (Blazor `href` webassembly) veya `<base>` *Pages/_host. cshtml* (Blazor Server) içindeki belge öğesindeki özniteliğe karşılık gelir. |
| `NavigateTo` | Belirtilen URI 'ye gider. `forceLoad` Şu`true`ise:<ul><li>İstemci tarafı yönlendirme atlanır.</li><li>Bu tarayıcı, URI 'nin normalde istemci tarafı yönlendirici tarafından işlenip işlenmediğini sunucudan yeni sayfayı yüklemeye zorlanır.</li></ul> |
| `LocationChanged` | Gezinti konumu değiştiğinde harekete gelen bir olay. |
| `ToAbsoluteUri` | Göreli bir URI 'yi mutlak bir URI 'ye dönüştürür. |
| `ToBaseRelativePath` | Temel URI (örneğin, daha önce tarafından `GetBaseUri`döndürülen bir URI) verildiğinde, mutlak bir URI 'yi taban URI önekine göre bir URI 'ye dönüştürür. |

Aşağıdaki bileşen, düğme seçildiğinde uygulamanın `Counter` bileşenine gider:

```cshtml
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```
