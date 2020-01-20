---
title: ASP.NET Core Blazor yönlendirme
author: guardrex
description: Uygulamalardaki istekleri yönlendirme ve gezinti bağlantısı bileşeni hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 0cd15f25ff7975cae3f63a739212aa23062ece23
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160164"
---
# <a name="aspnet-core-opno-locblazor-routing"></a>ASP.NET Core Blazor yönlendirme

Tarafından [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

İstekleri yönlendirmeyi ve Blazor uygulamalarda gezinti bağlantıları oluşturmak için `NavLink` bileşenini kullanmayı öğrenin.

## <a name="aspnet-core-endpoint-routing-integration"></a>Uç nokta yönlendirme tümleştirmesi ASP.NET Core

Blazor sunucusu [ASP.NET Core uç nokta yönlendirmesinde](xref:fundamentals/routing)tümleşiktir. ASP.NET Core bir uygulama, `Startup.Configure``MapBlazorHub` etkileşimli bileşenlere yönelik gelen bağlantıları kabul edecek şekilde yapılandırılmıştır:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

En yaygın yapılandırma, tüm istekleri Razor sayfasına yönlendirmesidir ve bu, Blazor sunucu uygulamasının sunucu tarafı bölümü için ana bilgisayar görevi görür. Kurala göre, *ana bilgisayar* sayfası genellikle *_Host. cshtml*olarak adlandırılır. Ana bilgisayar dosyasında belirtilen yol, yol eşleştirilirken düşük bir öncelik ile çalıştığından bir *geri dönüş yolu* olarak adlandırılır. Geri dönüş yolu, diğer yollar eşleşmediği zaman kabul edilir. Bu, uygulamanın Blazor sunucu uygulamasıyla kesintiye uğramadan diğer denetleyicileri ve sayfaları kullanmasına izin verir.

## <a name="route-templates"></a>Rota şablonları

`Router` bileşeni, belirtilen bir rota ile her bileşene yönlendirmeyi sağlar. `Router` bileşeni *app. Razor* dosyasında görünür:

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

`@page` yönergesine sahip bir *. Razor* dosyası derlendiğinde, oluşturulan sınıf yol şablonunu belirten bir <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> sağlanır.

Çalışma zamanında `RouteView` bileşeni:

* Tüm istenen parametrelerle birlikte `Router` `RouteData` alır.
* Belirtilen parametreleri kullanarak belirtilen bileşeni düzeniyle (veya isteğe bağlı bir varsayılan düzende) işler.

İsteğe bağlı olarak, düzen belirtmeyen bileşenler için kullanılacak düzen sınıfıyla bir `DefaultLayout` parametresi belirtebilirsiniz. Varsayılan Blazor şablonları `MainLayout` bileşenini belirtir. *Mainlayout. Razor* , şablon projenin *paylaşılan* klasöründedir. Düzenler hakkında daha fazla bilgi için bkz. <xref:blazor/layouts>.

Birden çok yol şablonu, bir bileşene uygulanabilir. Aşağıdaki bileşen `/BlazorRoute` ve `/DifferentBlazorRoute`isteklerine yanıt verir:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> URL 'Lerin doğru bir şekilde çözülmesi için, uygulamanın, `href` özniteliğinde belirtilen uygulama temel yolu ile *Wwwroot/index.html* dosyasına (Blazor WebAssembly) veya *Pages/_Host. cshtml* dosyasına (Blazor Server) bir `<base>` etiketi içermesi gerekir (`<base href="/">`). Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>İçerik bulunamadığında özel içerik sağla

`Router` bileşeni, istenen rota için içerik bulunmazsa uygulamanın özel içerik belirtmesini sağlar.

*App. Razor* dosyasında, `Router` bileşeninin `NotFound` Şablon parametresinde özel içerik ayarlayın:

```razor
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

`<NotFound>` etiketlerinin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir. `NotFound` içeriğe varsayılan bir düzen uygulamak için bkz. <xref:blazor/layouts>.

## <a name="route-to-components-from-multiple-assemblies"></a>Birden çok derlemeden bileşenlere rota

`Router` bileşenin yönlendirilebilir bileşenleri ararken dikkate alınması için ek derlemeler belirtmek üzere `AdditionalAssemblies` parametresini kullanın. Belirtilen derlemeler `AppAssembly`belirtilen derlemeye ek olarak değerlendirilir. Aşağıdaki örnekte `Component1`, başvurulan bir sınıf kitaplığında tanımlanan yönlendirilebilir bir bileşendir. Aşağıdaki `AdditionalAssemblies` örnek, `Component1`için yönlendirme desteğiyle sonuçlanır:

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Rota parametreleri

Yönlendirici, karşılık gelen bileşen parametrelerini aynı ada (büyük/küçük harfe duyarsız) doldurmak için yol parametrelerini kullanır:

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

İsteğe bağlı parametreler desteklenmez. Önceki örnekte iki `@page` yönergesi uygulanır. İlki, bir parametre olmadan bileşene gezinmesine izin verir. İkinci `@page` yönergesi `{text}` Route parametresini alır ve değeri `Text` özelliğine atar.

## <a name="route-constraints"></a>Yol kısıtlamaları

Yol kısıtlaması bir yönlendirme segmentinde bir bileşene tür eşleştirmeyi zorlar.

Aşağıdaki örnekte, `Users` bileşene olan yol yalnızca şu durumlarda eşleşir:

* İstek URL 'sinde bir `Id` yol kesimi var.
* `Id` segmenti bir tamsayıdır (`int`).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

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
> URL 'YI doğrulayan ve bir CLR türüne (örneğin `int` veya `DateTime`) dönüştürülen yol kısıtlamaları her zaman sabit kültürü kullanır. Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar.

### <a name="routing-with-urls-that-contain-dots"></a>Noktalar içeren URL 'lerle yönlendirme

Blazor Server uygulamalarında, *_Host. cshtml* içindeki varsayılan yol `/` (`@page "/"`). URL bir dosya isteyecek şekilde göründüğünden, nokta (`.`) içeren bir istek URL 'SI varsayılan yol tarafından eşleşmiyor. Blazor bir uygulama, var olmayan bir statik dosya için *404-Found* yanıtı döndürür. Bir nokta içeren yolları kullanmak için, *_Host. cshtml* 'yi aşağıdaki yol şablonuyla yapılandırın:

```cshtml
@page "/{**path}"
```

`"/{**path}"` şablonu şunları içerir:

* Ters eğik çizgi (`/`) kodlaması olmadan birden çok klasör sınırlarındaki yolu yakalamak için çift yıldız *catch-all* söz dizimi (`**`).
* `path` Route parametresi adı.

> [!NOTE]
> *Catch-all* parametre sözdizimi (`*`/`**`) Razor bileşenlerinde ( *. Razor* **) desteklenmez.**

Daha fazla bilgi için bkz. <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Gezinti bağlantısı bileşeni

Gezinti bağlantıları oluştururken, HTML köprü öğelerinin yerine bir `NavLink` bileşeni kullanın (`<a>`). Bir `NavLink` bileşeni, `href` geçerli URL ile eşleşip eşleşmediğini temel alan `active` CSS sınıfına geçiş yaparken, bir `<a>` öğesi gibi davranır. `active` sınıfı, bir kullanıcının hangi sayfanın etkin sayfa olduğunu anlamasına yardımcı olan gezinti bağlantıları.

Aşağıdaki `NavMenu` bileşeni, `NavLink` bileşenlerinin nasıl kullanılacağını gösteren bir [önyükleme](https://getbootstrap.com/docs/) gezinti çubuğu oluşturur:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

`<NavLink>` öğesinin `Match` özniteliğine atayabilmeniz için iki `NavLinkMatch` seçeneği vardır:

* `NavLinkMatch.All` &ndash; geçerli URL 'nin tamamı eşleştiğinde etkin `NavLink`.
* `NavLinkMatch.Prefix` (*varsayılan*) `NavLink` geçerli URL 'nin herhangi bir ön ekiyle eşleştiğinde etkin &ndash;.

Yukarıdaki örnekte, giriş `NavLink` `href=""` giriş URL 'siyle eşleşir ve yalnızca uygulamanın varsayılan temel yol URL 'sindeki `active` CSS sınıfını alır (örneğin, `https://localhost:5001/`). İkinci `NavLink`, Kullanıcı `MyComponent` ön ekine sahip herhangi bir URL 'YI ziyaret ettiğinde `active` sınıfını alır (örneğin, `https://localhost:5001/MyComponent` ve `https://localhost:5001/MyComponent/AnotherSegment`).

Ek `NavLink` bileşen öznitelikleri, işlenen tutturucu etiketine geçirilir. Aşağıdaki örnekte `NavLink` bileşeni `target` özniteliğini içerir:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Aşağıdaki HTML biçimlendirmesi işlenir:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI ve gezinti durumu yardımcıları

Kod içinde C# , URI 'ler ve gezinme ile çalışmak için `Microsoft.AspNetCore.Components.NavigationManager` kullanın. `NavigationManager`, aşağıdaki tabloda gösterilen olay ve yöntemleri sağlar.

| Üye | Açıklama |
| ------ | ----------- |
| `Uri` | Geçerli mutlak URI 'yi alır. |
| `BaseUri` | Mutlak bir URI oluşturmak için göreli URI yollarına eklenebilir olan temel URI 'yi (sondaki eğik çizgiyle birlikte) alır. Genellikle `BaseUri`, belgenin *Wwwroot/index.html* (Blazor WebAssembly) veya *Pages/_Host. cshtml* (Blazor Server) içindeki `<base>` öğesinde `href` özniteliğine karşılık gelir. |
| `NavigateTo` | Belirtilen URI 'ye gider. `forceLoad` `true`:<ul><li>İstemci tarafı yönlendirme atlanır.</li><li>Bu tarayıcı, URI 'nin normalde istemci tarafı yönlendirici tarafından işlenip işlenmediğini sunucudan yeni sayfayı yüklemeye zorlanır.</li></ul> |
| `LocationChanged` | Gezinti konumu değiştiğinde harekete gelen bir olay. |
| `ToAbsoluteUri` | Göreli bir URI 'yi mutlak bir URI 'ye dönüştürür. |
| `ToBaseRelativePath` | Temel URI (örneğin, daha önce `GetBaseUri`tarafından döndürülen bir URI) verildiğinde, mutlak bir URI 'yi taban URI ön ekine göre bir URI 'ye dönüştürür. |

Aşağıdaki bileşen, düğme seçildiğinde uygulamanın `Counter` bileşenine gider:

```razor
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
