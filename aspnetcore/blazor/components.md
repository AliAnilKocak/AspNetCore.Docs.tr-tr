---
title: ASP.NET Core Razor bileşenleri oluşturma ve kullanma
author: guardrex
description: Veri bağlama, olayları işleme ve bileşen yaşam döngülerini yönetme dahil Razor bileşenleri oluşturmayı ve kullanmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: blazor/components
ms.openlocfilehash: 438b3802087e2ac3df4cbe69a700b878c1cbbf63
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037426"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>ASP.NET Core Razor bileşenleri oluşturma ve kullanma

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))

Blazor uygulamaları, *bileşenleri*kullanılarak oluşturulmuştur. Bir bileşen, bir sayfa, iletişim veya form gibi bir kullanıcı arabirimi (UI) öbekidir. Bir bileşen, veri eklemek veya UI olaylarına yanıt vermek için gereken HTML işaretlemesini ve işleme mantığını içerir. Bileşenler esnek ve hafif. Bunlar, iç içe geçmiş, yeniden kullanılabilir ve projeler arasında paylaşılabilir.

## <a name="component-classes"></a>Bileşen sınıfları

Bileşenler, C# ve HTML Işaretlemesi kullanılarak [Razor](xref:mvc/views/razor) bileşen dosyalarında ( *. Razor*) uygulanır. Blazor içindeki bir bileşen, bir *Razor bileşeni*olarak adlandırılır.

Bir bileşenin adı, büyük harfle başlamalıdır. Örneğin, *mycoolcomponent. Razor* geçerlidir ve *mycoolcomponent. Razor* geçersizdir.

Bir bileşen için Kullanıcı arabirimi HTML kullanılarak tanımlanır. Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) C# [Razor](xref:mvc/views/razor)adlı gömülü bir sözdizimi kullanılarak eklenir. Bir uygulama derlendiğinde, HTML biçimlendirme ve C# işleme mantığı bir bileşen sınıfına dönüştürülür. Oluşturulan sınıfın adı, dosyanın adıyla eşleşir.

Bileşen sınıfının üyeleri `@code` bloğunda tanımlanır. @No__t-0 bloğunda, bileşen durumu (özellikler, alanlar) olay işleme yöntemleriyle veya diğer bileşen mantığını tanımlamaya yönelik yöntemlerle belirtilir. Birden fazla `@code` bloğu izin verilir.

> [!NOTE]
> ASP.NET Core 3,0 ' nin önceki önizlemelerinde, `@functions` blokları Razor bileşenlerinde `@code` bloklarında aynı amaçla kullanılmıştır. `@functions` blokları Razor bileşenlerinde çalışmaya devam eder, ancak ASP.NET Core 3,0 Preview 6 veya sonraki bir sürümünde `@code` bloğunu kullanmanızı öneririz.

Bileşen üyeleri, `@` ile başlayan ifadeler kullanılarak C# bileşen işleme mantığının bir parçası olarak kullanılabilir. Örneğin, bir C# alan `@` ' i alan adı ' na önek olarak işlenir. Aşağıdaki örnek değerlendirilir ve işler:

* `font-style` için CSS özellik değerine `_headingFontStyle`.
* `<h1>` öğesinin içeriğine `_headingText`.

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Bileşen ilk olarak işlendikten sonra, bileşen işleme ağacını olaylara yanıt olarak yeniden oluşturur. Blazor ardından yeni işleme ağacını önceki bir ile karşılaştırır ve tarayıcının Belge Nesne Modeli (DOM) üzerinde herhangi bir değişiklik uygular.

Bileşenler sıradan C# sınıflardır ve bir proje içinde herhangi bir yere yerleştirilebilir. Web sayfalarını üreten bileşenler genellikle *Sayfalar* klasöründe bulunur. Sayfa olmayan bileşenler sıklıkla *paylaşılan* klasöre veya projeye eklenen özel bir klasöre yerleştirilir. Özel bir klasör kullanmak için, özel klasörün ad alanını üst bileşene ya da uygulamanın *_ımports. Razor* dosyasına ekleyin. Örneğin, aşağıdaki ad alanı, uygulamanın kök ad alanı `WebApplication` olduğunda bir *Bileşenler* klasöründeki bileşenleri kullanılabilir yapar:

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>Bileşenleri Razor Pages ve MVC uygulamalarıyla tümleştirme

Mevcut Razor Pages ve MVC uygulamalarıyla bileşenleri kullanın. Razor bileşenleri kullanmak için mevcut sayfaları veya görünümleri yeniden yazmanız gerekmez. Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden işlenir.

Bir sayfadan veya görünümden bir bileşeni işlemek için `RenderComponentAsync<TComponent>` HTML yardımcı yöntemini kullanın:

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

Sayfalar ve görünümler bileşenleri kullanırken, listesiyse doğru değildir. Bileşenler, kısmi görünümler ve bölümler gibi görüntüleme ve sayfaya özgü senaryolar kullanamaz. Bir bileşende kısmi görünümden mantığı kullanmak için kısmi görünüm mantığını bir bileşene ayırın.

Bileşenlerin nasıl işlendiği ve bileşen durumunun Blazor sunucu uygulamalarında nasıl yönetildiği hakkında daha fazla bilgi için <xref:blazor/hosting-models> makalesine bakın.

## <a name="use-components"></a>Bileşenleri kullanma

Bileşenler, HTML öğesi söz dizimini kullanarak bildirerek diğer bileşenleri içerebilir. Bir bileşeni kullanmak için biçimlendirme, etiket adının bileşen türü olduğu bir HTML etiketi gibi görünür.

Öznitelik bağlama büyük/küçük harfe duyarlıdır. Örneğin, `@bind` geçerli ve `@Bind` geçersiz.

*Index. Razor* dosyasında aşağıdaki biçimlendirme `HeadingComponent` örneğini işler:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

*Bileşenler/HeadingComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

Bir bileşen bir bileşen adıyla eşleşmeyen büyük harfle yazılmış bir HTML öğesi içeriyorsa, öğenin beklenmeyen bir adı olduğunu gösteren bir uyarı yayınlanır. Bileşenin ad alanı için `@using` ifadesinin eklenmesi, bileşenin kullanılabilir olmasını sağlar ve bu da uyarıyı kaldırır.

## <a name="component-parameters"></a>Bileşen parametreleri

Bileşenler, bileşen sınıfında `[Parameter]` özniteliğiyle ortak özellikler kullanılarak tanımlanan *bileşen parametrelerine*sahip olabilir. Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.

*Bileşenler/ChildComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

Aşağıdaki örnekte, `ParentComponent` `ChildComponent` ' nin `Title` özelliğinin değerini ayarlar.

*Pages/ParentComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a>Alt içerik

Bileşenler, başka bir bileşenin içeriğini ayarlayabilir. Atama bileşeni, alıcı bileşeni belirten Etiketler arasında içerik sağlar.

Aşağıdaki örnekte, `ChildComponent` ' ı @no__t temsil eden bir `ChildContent` özelliği vardır. Bu bir kullanıcı arabirimi, işlemek için bir segmenti temsil eder. @No__t-0 değeri, içeriğin işlenmesi gereken bileşenin biçimlendirmesinde konumlandırılır. @No__t-0 değeri üst bileşenden alınır ve önyükleme panelinin `panel-body` ' in içinde işlenir.

*Bileşenler/ChildComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> @No__t-0 içeriğini alan özelliğin kurala göre `ChildContent` olarak adlandırılması gerekir.

Aşağıdaki `ParentComponent`, içeriği `<ChildComponent>` etiketlerinin içine yerleştirerek `ChildComponent` ' i işlemek için içerik sağlayabilir.

*Pages/ParentComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Öznitelik döndürme ve rastgele parametreler

Bileşenler, bileşen tarafından tanımlanan parametrelere ek olarak ek öznitelikler yakalayabilir ve işleyebilir. Ek öznitelikler bir sözlükte yakalanabilir *ve sonra bileşen* [@attributes](xref:mvc/views/razor#attributes) Razor yönergesi kullanılarak işlendiğinde bir öğe üzerine bırakılabilir. Bu senaryo, çeşitli özelleştirmeleri destekleyen bir işaretleme öğesi üreten bir bileşen tanımlarken yararlıdır. Örneğin, çok sayıda parametreyi destekleyen bir `<input>` için öznitelikleri ayrı olarak tanımlamak sıkıcı olabilir.

Aşağıdaki örnekte, ilk `<input>` öğesi (`id="useIndividualParams"`) tek tek bileşen parametrelerini kullanır, ancak ikinci `<input>` öğesi (`id="useAttributesDict"`) öznitelik splatesini kullanır:

```cshtml
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

Parametrenin türü dize anahtarlarıyla `IEnumerable<KeyValuePair<string, object>>` uygulamalıdır. @No__t-0 kullanmak Bu senaryoda da bir seçenektir.

Her iki yaklaşımın de kullanıldığı işlenen @no__t 0 öğeleri aynıdır:

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

Rastgele öznitelikleri kabul etmek için, `CaptureUnmatchedValues` özelliği `true` ' ye ayarlanmış `[Parameter]` özniteliğini kullanarak bir bileşen parametresi tanımlayın:

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

@No__t-1 ' deki `CaptureUnmatchedValues` özelliği, parametrenin diğer bir parametreyle eşleşmeyen tüm özniteliklerle eşleşmesini sağlar. Bir bileşen yalnızca `CaptureUnmatchedValues` olan tek bir parametre tanımlayabilir. @No__t-0 ile kullanılan özellik türü, `Dictionary<string, object>` ' den dize anahtarlarıyla atanabilir olmalıdır. `IEnumerable<KeyValuePair<string, object>>` veya `IReadOnlyDictionary<string, object>` Ayrıca bu senaryodaki seçeneklerdir.

## <a name="data-binding"></a>Veri bağlama

Hem bileşenlere hem de DOM öğelerine veri bağlama [@bind](xref:mvc/views/razor#bind) özniteliğiyle gerçekleştirilir. Aşağıdaki örnekte `_italicsCheck` alanı onay kutusunun işaretli durumuna bağlanır:

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

Onay kutusu seçildiğinde ve kaldırıldığında, özelliğin değeri sırasıyla `true` ve `false` olarak güncelleştirilir.

Onay kutusu kullanıcı arabiriminde, özelliğin değerini değiştirme yanıt olarak değil, yalnızca bileşen işlendiğinde güncelleştirilir. Bileşenler olay işleyicisi kodu yürütüldükten sonra kendilerini oluşturduğundan, özellik güncelleştirmeleri genellikle kullanıcı arabirimine hemen yansıtılır.

@No__t-1 özelliği (`<input @bind="CurrentValue" />`) ile `@bind` kullanmak, temelde aşağıdakilere eşdeğerdir:

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

Bileşen işlendiğinde, giriş öğesinin `value` ' ı `CurrentValue` özelliğinden gelir. Kullanıcı metin kutusunda yazdığında, `onchange` olayı tetiklenir ve `CurrentValue` özelliği değiştirilen değere ayarlanır. @No__t-0, tür dönüştürmelerin gerçekleştirildiği birkaç durumu işlediği için, gerçekte kod oluşturma biraz daha karmaşıktır. İlke ' de, `@bind` bir ifadenin geçerli değerini bir `value` özniteliğiyle ilişkilendirir ve kayıtlı işleyiciyi kullanarak değişiklikleri işler.

@No__t-1 sözdizimiyle `onchange` olaylarını işlemenin yanı sıra, bir özellik veya alan, `event` parametresiyle bir [@bind-value](xref:mvc/views/razor#bind) özniteliği belirterek diğer olaylar kullanılarak da bağlanabilir ([@bind-value:event](xref:mvc/views/razor#bind)). Aşağıdaki örnek, `CurrentValue` özelliğini `oninput` olayı için bağlar:

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

@No__t-0 ' dan farklı olarak, öğe odağı kaybettiğinde harekete geçirilir `oninput`, metin kutusunun değeri değiştiğinde harekete geçirilir.

**Ayrıştırılamayan değerler**

Bir Kullanıcı, bir veri sınırlama öğesine ayrıştırılamayan bir değer sağlıyorsa, bağlama olayı tetiklendiğinde, çözümlenemeyen değer otomatik olarak önceki değerine döndürülür.

Aşağıdaki senaryoyu göz önünde bulundurun:

* @No__t-0 öğesi, ilk değeri `123` olan `int` türüne bağlanır:

  ```cshtml
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Kullanıcı, öğe değerini sayfada `123.45` olarak güncelleştirir ve öğe odağını değiştirir.

Önceki senaryoda, öğenin değeri `123` ' a geri döndürülür. @No__t-0 değeri, `123` ' in özgün değeri yararına reddedildiğinde, Kullanıcı değerinin kabul edilmediğini anlamıştır.

Varsayılan olarak, bağlama öğenin `onchange` olayına uygulanır (`@bind="{PROPERTY OR FIELD}"`). Farklı bir olay ayarlamak için `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` kullanın. @No__t-0 olayı (`@bind-value:event="oninput"`) için, yeniden sürüm, ayrıştırılamayan bir değer sunan herhangi bir tuş vuruşu sonrasında oluşur. @No__t-0 olayını @no__t -1 ile bağlantılı bir türle hedeflerken, kullanıcının `.` karakteri yazmasının engellenmiş olması engellenir. @No__t-0 karakteri hemen kaldırılır, bu nedenle Kullanıcı yalnızca tam sayılara izin verilen anında geri bildirim alır. @No__t-0 olaylarındaki değerin geri döndürülebileceği senaryolar vardır; örneğin, kullanıcının çözümlenemeyen bir `<input>` değerini temizme izni verilmesi gerektiği durumlar gibi. Alternatifler şunlardır:

* @No__t-0 olayını kullanmayın. Öğe odağı kaybederene kadar geçersiz bir değer geri döndürülmediğinde, varsayılan `onchange` olayını (`@bind="{PROPERTY OR FIELD}"`) kullanın.
* @No__t-0 veya `string` gibi null yapılabilir bir türe bağlayın ve geçersiz girdileri işlemek için özel mantık sağlayın.
* @No__t-1 veya `InputDate` gibi bir [form doğrulama bileşeni](xref:blazor/forms-validation)kullanın. Form doğrulama bileşenlerinde geçersiz girişleri yönetmek için yerleşik destek vardır. Form doğrulama bileşenleri:
  * Kullanıcının, ilişkili `EditContext` ' da geçersiz giriş sağlamasına ve doğrulama hataları almasına izin verin.
  * Kullanıcı ek WebForm verisi girmeye uğramadan doğrulama hatalarını Kullanıcı ARABIRIMINDE görüntüleyin.

**Genelleştirme**

`@bind` değerleri, geçerli kültürün kuralları kullanılarak görüntülenmek üzere biçimlendirilir ve ayrıştırılır.

Geçerli kültüre <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> özelliğinden erişilebilir.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) aşağıdaki alan türleri için kullanılır (`<input type="{TYPE}" />`):

* `date`
* `number`

Yukarıdaki alan türleri:

* , Uygun tarayıcı tabanlı biçimlendirme kuralları kullanılarak görüntülenir.
* Serbest biçimli metin içeremez.
* Tarayıcının uygulamasına göre Kullanıcı etkileşimi özellikleri sağlar.

Aşağıdaki alan türleri özel biçimlendirme gereksinimlerine sahiptir ve şu anda tüm büyük tarayıcılarda desteklenmediği için Blazor tarafından desteklenmemektedir:

* `datetime-local`
* `month`
* `week`

`@bind`, bir değeri ayrıştırmak ve biçimlendirmek için <xref:System.Globalization.CultureInfo?displayProperty=fullName> sağlamak üzere `@bind:culture` parametresini destekler. @No__t-0 ve `number` alan türleri kullanılırken bir kültür belirtilmesi önerilmez. `date` ve `number`, gerekli kültürü sağlayan yerleşik Blazor desteğine sahiptir.

Kullanıcının kültürünü ayarlama hakkında daha fazla bilgi için [Yerelleştirme](#localization) bölümüne bakın.

**Biçim dizeleri**

Veri bağlama, [@bind:format](xref:mvc/views/razor#bind)kullanılarak <xref:System.DateTime> biçim dizeleriyle birlikte kullanılabilir. Para birimi veya sayı biçimleri gibi diğer biçim ifadeleri şu anda kullanılamaz.

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Yukarıdaki kodda `<input>` öğesinin alan türü (`type`) varsayılan olarak `text` ' dir. `@bind:format`, aşağıdaki .NET türlerini bağlamak için desteklenir:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

@No__t-0 özniteliği, `<input>` öğesinin `value` ' e uygulanacak tarih biçimini belirtir. Biçim, bir `onchange` olayı gerçekleştiğinde değeri ayrıştırmak için de kullanılır.

Blazor 'in tarihleri biçimlendirmek için yerleşik desteği olduğundan `date` alan türü için biçim belirtme önerilmez.

**Bileşen parametreleri**

Bağlama, `@bind-{property}` ' ın bileşenler arasında bir özellik değeri bağlayabileceği bileşen parametrelerini tanır.

Aşağıdaki alt bileşende (`ChildComponent`) bir `Year` bileşen parametresi ve `YearChanged` geri çağırması vardır:

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

`EventCallback<T>`, [Eventcallback](#eventcallback) bölümünde açıklanmaktadır.

Aşağıdaki üst bileşen `ChildComponent` kullanır ve `ParentYear` parametresini alt bileşendeki `Year` parametresine bağlar:

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

@No__t yüklemek-0 aşağıdaki biçimlendirmeyi oluşturur:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

@No__t-0 özelliğinin değeri `ParentComponent` ' deki düğme seçilerek değiştirilirse, `ChildComponent` ' ün `Year` özelliği güncellenir. @No__t-0 ' ın yeni değeri, `ParentComponent` tekrar eklendiğinde Kullanıcı arabiriminde işlenir:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

@No__t-0 parametresi, `Year` parametresinin türüyle eşleşen bir yardımcı `YearChanged` olayına sahip olduğundan bağlanabilir.

Kurala göre `<ChildComponent @bind-Year="ParentYear" />` temelde yazmaya eşdeğerdir:

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Genel olarak, bir özellik `@bind-property:event` özniteliği kullanılarak karşılık gelen bir olay işleyicisine bağlanabilir. Örneğin, `MyProp` özelliği aşağıdaki iki öznitelik kullanılarak `MyEventHandler` ' e bağlanabilir:

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a>Olay işleme

Razor bileşenleri olay işleme özellikleri sağlar. Temsilci türü belirtilmiş bir değerle `on{event}` (örneğin, `onclick` ve `onsubmit`) adlı bir HTML öğesi özniteliği için, Razor bileşenleri özniteliğin değerini bir olay işleyicisi olarak değerlendirir. Özniteliğin adı her zaman [@on {Event}](xref:mvc/views/razor#onevent)olarak biçimlendirilir.

Aşağıdaki kod, Kullanıcı arabiriminde düğme seçildiğinde `UpdateHeading` yöntemini çağırır:

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

Aşağıdaki kod, Kullanıcı arabiriminde onay kutusu değiştirildiğinde `CheckChanged` yöntemini çağırır:

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Olay işleyicileri Ayrıca zaman uyumsuz olabilir ve bir @no__t döndürebilir. @No__t-0 ' yı el ile çağırmanız gerekmez. Özel durumlar oluştuğunda günlüğe kaydedilir.

Aşağıdaki örnekte, düğme seçildiğinde `UpdateHeading` zaman uyumsuz olarak çağrılır:

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a>Olay bağımsız değişken türleri

Bazı olaylar için olay bağımsız değişkeni türlerine izin verilir. Bu olay türlerinden birine erişim gerekmiyorsa, yöntem çağrısında gerekli değildir.

Desteklenen `EventArgs` aşağıdaki tabloda gösterilmiştir.

| Olay | Sınıf |
| ----- | ----- |
| Pano        | `ClipboardEventArgs` |
| Sürükle             | `DragEventArgs` &ndash; `DataTransfer` ve @no__t 3 saklama öğe verilerini sürüklemiş. |
| Hata            | `ErrorEventArgs` |
| Çı            | `FocusEventArgs` &ndash; `relatedTarget` desteğini içermez. |
| `<input>` değişiklik | `ChangeEventArgs` |
| Klavye         | `KeyboardEventArgs` |
| Tığında            | `MouseEventArgs` |
| Fare işaretçisi    | `PointerEventArgs` |
| Fare tekerleği      | `WheelEventArgs` |
| İlerleme durumu         | `ProgressEventArgs` |
| Dokunma            | `TouchEventArgs` &ndash; `TouchPoint`, dokunmaya duyarlı bir cihazdaki tek bir iletişim noktasını temsil eder. |

Önceki tablodaki olayların özellikleri ve olay işleme davranışı hakkında bilgi için bkz. [başvuru kaynağında EventArgs sınıfları (ASPNET/AspNetCore Release/3.0 dalı)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).

### <a name="lambda-expressions"></a>Lambda ifadeleri

Lambda ifadeleri de kullanılabilir:

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Genellikle, bir dizi öğe üzerinde yineleme yaparken olduğu gibi ek değerlerin üzerinde kapatılabilir. Aşağıdaki örnek, her biri Kullanıcı arabiriminde seçildiğinde `UpdateHeading` ' ı bir olay bağımsız değişkenini (`MouseEventArgs`) ve düğme numarasını (`buttonNumber`) geçen üç düğme oluşturur:

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> Döngü değişkenini (`i`) doğrudan bir lambda ifadesinde bir `for` **döngüsünde kullanmayın.** Aksi halde aynı değişken tüm lambda ifadeleri tarafından, `i` ' ın değerinin tüm Lambdalar ile aynı olmasına neden olur. Her zaman değerini yerel bir değişkende yakala (önceki örnekte `buttonNumber`) ve ardından kullanın.

### <a name="eventcallback"></a>EventCallback

İç içe bileşenler içeren yaygın bir senaryo, alt bileşen olayı olduğunda bir üst bileşen yönteminin (örneğin, bir `onclick`) bir ' no__t-0' ı bir olay gerçekleştiği zaman çalıştırılması yöntemidir. Olayları bileşenler arasında ortaya çıkarmak için `EventCallback` kullanın. Bir üst bileşen, bir alt bileşenin `EventCallback` ' a bir geri çağırma yöntemi atayabilir.

Örnek uygulamadaki `ChildComponent` ' ın, bir düğmenin `onclick` işleyicisinin, örneğin `ParentComponent` ' ten `EventCallback` temsilcisini almak üzere nasıl ayarlandığını gösterir. @No__t-0 `MouseEventArgs` ile yazılır ve bu, çevrebirim cihazından gelen @no__t 2 olayına uygundur:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

@No__t-0, çocuğun `EventCallback<T>` ' i `ShowMessage` yöntemine ayarlar:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

Düğme @no__t seçildiğinde-0:

* @No__t-0 ' ın `ShowMessage` yöntemi çağırılır. `messageText`, `ParentComponent` ' de güncellenir ve görüntülenir.
* Geri çağırma yönteminde `StateHasChanged` çağrısı gerekli değildir (`ShowMessage`). `StateHasChanged` `ParentComponent` ' i yeniden çalıştırmak için otomatik olarak çağrılır. Örneğin, alt olaylar, alt öğe içinde yürütülen olay işleyicilerinde bileşen rerendering tetikler.

`EventCallback` ve `EventCallback<T>` zaman uyumsuz temsilcilere izin verir. `EventCallback<T>` kesin bir şekilde türdedir ve belirli bir bağımsız değişken türü gerektirir. `EventCallback`, kesin olarak yazılmış ve herhangi bir bağımsız değişken türüne izin veriyor.

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

@No__t-0 veya `EventCallback<T>` ' i `InvokeAsync` ile çağırın ve <xref:System.Threading.Tasks.Task> ' i await:

```csharp
await callback.InvokeAsync(arg);
```

Olay işleme ve bağlama bileşeni parametrelerini `EventCallback` ve `EventCallback<T>` kullanın.

@No__t-1 üzerinde türü kesin belirlenmiş `EventCallback<T>` tercih edin. `EventCallback<T>`, bileşenin kullanıcılarına daha iyi hata geri bildirimi sağlar. Diğer UI olay işleyicileriyle benzer şekilde, olay parametresini belirtmek isteğe bağlıdır. Geri çağırmaya hiçbir değer geçirilmemişse `EventCallback` kullanın.

## <a name="chained-bind"></a>Zincirleme bağlama

Yaygın bir senaryo, bir veri bağlama parametresini bileşen çıkışında bir sayfa öğesine zincirlemesini sağlar. Birden çok bağlama düzeyi aynı anda gerçekleştiğinden, bu senaryoya *zincirleme bağlama* denir.

Bir zincir bağlama, sayfanın öğesinde `@bind` sözdizimi ile uygulanamaz. Olay işleyicisi ve değeri ayrı olarak belirtilmelidir. Ancak bir üst bileşen, bileşen parametresiyle `@bind` sözdizimini kullanabilir.

Aşağıdaki `PasswordField` bileşeni (*Passwordfield. Razor*):

* @No__t-0 öğesinin değerini bir `Password` özelliğine ayarlar.
* @No__t-0 özelliğindeki değişiklikleri [Eventcallback](#eventcallback)ile bir üst bileşene gösterir.

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

@No__t-0 bileşeni başka bir bileşende kullanılır:

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Önceki örnekteki parolada denetim veya tuzak hataları gerçekleştirmek için:

* @No__t-0 (Aşağıdaki örnek kodda `password`) için bir yedekleme alanı oluşturun.
* @No__t-0 ayarlayıcısından denetimleri veya yakalama hatalarını gerçekleştirin.

Aşağıdaki örnek, parolanın değerinde bir boşluk kullanılmışsa kullanıcıya anında geri bildirim sağlar:

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="capture-references-to-components"></a>Bileşenlere başvuruları yakala

Bileşen başvuruları bir bileşen örneğine başvurmak için bir yol sağlar, böylece bu örneğe `Show` veya `Reset` gibi komutlar verebilirsiniz. Bir bileşen başvurusunu yakalamak için:

* Alt bileşene bir [@ref](xref:mvc/views/razor#ref) özniteliği ekleyin.
* Alt bileşenle aynı türde bir alan tanımlayın.

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Bileşen işlendiğinde `loginDialog` alanı `MyLoginDialog` alt bileşen örneğiyle doldurulur. Daha sonra bileşen örneğinde .NET yöntemlerini çağırabilirsiniz.

> [!IMPORTANT]
> @No__t-0 değişkeni yalnızca bileşen işlendikten sonra ve çıktısı `MyLoginDialog` öğesini içerdiğinde doldurulur. Bu noktaya kadar başvurulmasına hiçbir şey yok. Bileşen işlemesini tamamladıktan sonra bileşen başvurularını işlemek için `OnAfterRenderAsync` veya `OnAfterRender` yöntemlerini kullanın.

Bileşen başvurularını yakalama, [öğe başvurularını yakalamak](xref:blazor/javascript-interop#capture-references-to-elements)için benzer bir sözdizimi kullanın, bir [JavaScript birlikte çalışma](xref:blazor/javascript-interop) özelliği değildir. Bileşen başvuruları, yalnızca .NET kodunda kullanılan @ no__t-0thei JavaScript koduna aktarılmaz.

> [!NOTE]
> Alt bileşenlerin durumunu bulunmamalıdır için bileşen **başvurularını kullanmayın.** Bunun yerine, alt bileşenlere veri geçirmek için normal bildirime dayalı parametreleri kullanın. Normal bildirime dayalı parametrelerin kullanımı, otomatik olarak doğru zamanların yeniden yönlendirmesi için alt bileşenlerde oluşur.

## <a name="invoke-component-methods-externally-to-update-state"></a>Durumu güncelleştirmek için bileşen yöntemlerini dışarıdan çağır

Blazor, yürütmenin tek bir mantıksal iş parçacığını zorlamak için `SynchronizationContext` kullanır. Bir bileşenin yaşam döngüsü yöntemleri ve Blazor tarafından oluşturulan tüm olay geri çağırmaları bu @no__t yürütülür-0. Bir bileşenin, Zamanlayıcı veya diğer bildirimler gibi dış bir olaya göre güncellenmesi gerekir, bu, @no__t Blazor ' a gönderilen `InvokeAsync` yöntemini kullanır.

Örneğin, güncelleştirilmiş durumdaki herhangi bir dinleme bileşenine bildirimde bulunan bir *bildirim hizmeti* düşünün:

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

Bir bileşeni güncelleştirmek için `NotifierService` kullanımı:

```cshtml
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

Yukarıdaki örnekte, `NotifierService` bileşenin `OnNotify` yöntemini Blazor `SynchronizationContext` dışında çağırır. `InvokeAsync`, doğru bağlama geçmek ve bir işlemeyi kuyruğa almak için kullanılır.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Öğelerin ve bileşenlerin korunmasını denetlemek için \@key kullanın

Bir öğe veya bileşen listesi işlenirken ve öğeler ya da bileşenler daha sonra değiştiğinde, Blazor 'in yayılma algoritması, önceki öğelerin veya bileşenlerin ne zaman tutulacağına ve model nesnelerinin bunlara nasıl eşleneceğine karar vermelidir. Normalde, bu işlem otomatiktir ve yoksayılabilir, ancak işlemi denetlemek isteyebileceğiniz durumlar vardır.

Aşağıdaki örnek göz önünde bulundurun:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

@No__t-0 koleksiyonunun içeriği, ekli, silinmiş veya yeniden sıralanmış girdilerle değişebilir. Bileşen yeniden oluşturulduğunda, `<DetailsEditor>` bileşeni farklı `Details` parametre değerleri almak için değişebilir. Bu, beklenenden daha karmaşık rerendering oluşmasına neden olabilir. Bazı durumlarda rerendering, kayıp öğe odağı gibi görünür davranış farklılıklarına yol açabilir.

Eşleme işlemi `@key` yönergesi özniteliğiyle denetlenebilir. `@key`, anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için dağıtılmış algoritmaya neden olur:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

@No__t-0 koleksiyonu değiştiğinde, yayılma algoritması `<DetailsEditor>` örnekleri ile `person` örnekleri arasındaki ilişkilendirmeyi korur:

* @No__t-0 `People` listesinden silinirse, yalnızca ilgili `<DetailsEditor>` örneği kullanıcı arabiriminden kaldırılır. Diğer örnekler değişmeden bırakılır.
* @No__t-0 listedeki bir konuma eklenirse, ilgili konuma yeni bir `<DetailsEditor>` örnek eklenir. Diğer örnekler değişmeden bırakılır.
* @No__t-0 girdileri yeniden sıralandıysanız, karşılık gelen `<DetailsEditor>` örnekleri korunur ve Kullanıcı arabiriminde yeniden sıralanır.

Bazı senaryolarda `@key` kullanımı, rerendering karmaşıklığını en aza indirir ve odak konumu gibi DOM 'ın durum bilgisi olan kısımlarıyla ilgili olası sorunları önler.

> [!IMPORTANT]
> Anahtarlar her kapsayıcı öğesi veya bileşeni için yereldir. Anahtarlar belge genelinde küresel olarak karşılaştırılmaz.

### <a name="when-to-use-key"></a>@No__t-0key ne zaman kullanılır?

Genellikle, bir liste işlendiğinde (örneğin, bir `@foreach` bloğunda) ve `@key` tanımlamak için uygun bir değer olduğunda `@key` ' ı kullanmak mantıklı olur.

Bir nesne değiştiğinde Blazor 'in bir öğeyi veya bileşen alt ağacını engellemesini engellemek için `@key` ' yı da kullanabilirsiniz:

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

@No__t-0 değişirse `@key` öznitelik yönergesi, Blazor tüm `<div>` ve alt öğelerini atmayı ve yeni öğeler ve bileşenlerle Kullanıcı arabiriminde alt ağacı yeniden oluşturmayı zorlar. Bu, `@currentPerson` değiştiğinde hiçbir Kullanıcı arabirimi durumunun korunmayacağını garanti etmeniz gerektiğinde yararlı olabilir.

### <a name="when-not-to-use-key"></a>@No__t-0key ne zaman kullanılmaz

@No__t-0 ile dağıtılmış bir performans maliyeti vardır. Performans maliyeti büyük değildir, ancak öğe veya bileşen koruma kuralları denetlenmediğinde yalnızca `@key` belirtin.

@No__t-0 kullanılmasa bile, Blazor alt öğe ve bileşen örneklerini mümkün olduğunca korur. @No__t-0 ' ı kullanmanın tek avantajı, model örneklerinin eşlemeyi seçme algoritması yerine, korunan bileşen örneklerine *nasıl* eşlendiğine ilişkin bir denetimdir.

### <a name="what-values-to-use-for-key"></a>@No__t-0key için kullanılacak değerler

Genellikle, `@key` için aşağıdaki değer türlerinden birini sağlamak mantıklı olur:

* Model nesne örnekleri (örneğin, önceki örnekte olduğu gibi `Person` örneği). Bu, nesne başvurusu eşitliğine göre koruma sağlar.
* Benzersiz tanımlayıcılar (örneğin, `int`, `string` veya `Guid`) türündeki birincil anahtar değerleri.

@No__t-0 için kullanılan değerlerin çakışmayın olduğundan emin olun. Aynı üst öğe içinde çakışan değerler algılanırsa, eski öğeleri veya bileşenleri yeni öğe veya bileşenlere kesin bir şekilde eşlemediğinden Blazor bir özel durum oluşturur. Yalnızca nesne örnekleri veya birincil anahtar değerleri gibi farklı değerleri kullanın.

## <a name="lifecycle-methods"></a>Yaşam döngüsü yöntemleri

`OnInitializedAsync` ve `OnInitialized` bileşeni başlatmak için kodu yürütün. Zaman uyumsuz bir işlem gerçekleştirmek için işlem üzerinde `OnInitializedAsync` ve `await` anahtar sözcüğünü kullanın:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Zaman uyumlu bir işlem için `OnInitialized` kullanın:

```csharp
protected override void OnInitialized()
{
    ...
}
```

`OnParametersSetAsync` ve `OnParametersSet`, bir bileşen üst öğeden parametreleri aldığında ve değerler özelliklere atandığında çağrılır. Bu yöntemler bileşen başlatıldıktan sonra ve bileşen her işlendiğinde yürütülür:

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

`OnAfterRenderAsync` ve `OnAfterRender` bir bileşen işlemeyi tamamladıktan sonra çağrılır. Öğe ve bileşen başvuruları bu noktada doldurulur. İşlenmiş DOM öğelerinde çalışan üçüncü taraf JavaScript kitaplıklarını etkinleştirme gibi, işlenmiş içeriği kullanarak ek başlatma adımları gerçekleştirmek için bu aşamayı kullanın.

`OnAfterRender`, *sunucuda prerendering çağrıldığında çağrılmaz.*

@No__t-1 ve `OnAfterRender` için `firstRender` parametresi:

* Bileşen örneği ilk kez çağrıldığında `true` olarak ayarlayın.
* Başlatma işinin yalnızca bir kez gerçekleştirildiğinden emin olur.

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a>İşleme sırasında tamamlanmamış zaman uyumsuz eylemleri işle

Yaşam döngüsü olaylarında gerçekleştirilen zaman uyumsuz eylemler, bileşen işlenmeden önce tamamlanmamış olabilir. Yaşam döngüsü yöntemi yürütülürken nesneler `null` veya verilerle tamamen doldurulmuş olabilir. Nesnelerin başlatıldığını onaylamak için işleme mantığı sağlayın. Nesneler `null` olduğunda yer tutucu Kullanıcı arabirimi öğelerini (örneğin, bir yükleme iletisi) işleme.

Blazor şablonlarının `FetchData` bileşeninde, `OnInitializedAsync`, Asychronously tahmin verileri al (`forecasts`) için geçersiz kılınır. @No__t-0 `null` olduğunda, kullanıcıya bir yükleme iletisi görüntülenir. @No__t-1 tarafından döndürülen `Task` ' ı tamamladıktan sonra, bileşen güncelleştirilmiş durumla yeniden yapılır.

*Pages/FetchData. Razor*:

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a>Parametreler ayarlanmadan önce kodu yürütün

Parametreler ayarlanmadan önce kodu yürütmek için `SetParameters` geçersiz kılınabilir:

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

@No__t-0 çağrılmazsa, özel kod gelen parametreler değerini gerekli herhangi bir şekilde yorumlayabilir. Örneğin, gelen parametrelerin, sınıftaki özelliklere atanması gerekmez.

### <a name="suppress-refreshing-of-the-ui"></a>Kullanıcı arabiriminin yenilenmesini gösterme

`ShouldRender`, Kullanıcı arabiriminin yenilenmesini gizlemek için geçersiz kılınabilir. Uygulama `true` döndürürse, Kullanıcı arabirimi yenilenir. @No__t-0 geçersiz kılınsa bile, bileşen her zaman ilk olarak işlenir.

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>IDisposable ile bileşen atma

Bir bileşen <xref:System.IDisposable> ' ı uygularsa, bileşen kullanıcı arabiriminden kaldırıldığında [Dispose yöntemi](/dotnet/standard/garbage-collection/implementing-dispose) çağrılır. Aşağıdaki bileşen `@implements IDisposable` ve `Dispose` yöntemini kullanır:

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a>Yönlendirme

Blazor ' de yönlendirme, uygulamadaki her erişilebilir bileşene bir rota şablonu sunarak elde edilir.

@No__t-0 yönergesi içeren bir Razor dosyası derlendiğinde, oluşturulan sınıfa yol şablonunu belirten bir <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> verilir. Çalışma zamanında, yönlendirici `RouteAttribute` ile bileşen sınıflarına bakar ve hangi bileşenin istenen URL ile eşleşen bir rota şablonuna sahip olduğunu işler.

Birden çok yol şablonu, bir bileşene uygulanabilir. Aşağıdaki bileşen `/BlazorRoute` ve `/DifferentBlazorRoute` isteklerine yanıt verir:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>Rota parametreleri

Bileşenler, `@page` yönergesinde belirtilen yol şablonundan rota parametreleri alabilir. Yönlendirici, karşılık gelen bileşen parametrelerini doldurmak için yol parametrelerini kullanır.

*Rota parametresi bileşeni*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

İsteğe bağlı parametreler desteklenmez, bu nedenle yukarıdaki örnekte iki `@page` yönergesi uygulanır. İlki, bir parametre olmadan bileşene gezinmesine izin verir. İkinci `@page` yönergesi `{text}` yol parametresini alır ve değeri `Text` özelliğine atar.

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>"Arka plan kod" deneyimi için temel sınıf devralma

Bileşen dosyaları aynı dosyada HTML biçimlendirme C# ve işleme kodu karışımını. @No__t-0 yönergesi, bileşen işaretlemesini işleme kodundan ayıran "arka plan kod" deneyimiyle Blazor uygulamalar sağlamak için kullanılabilir.

[Örnek uygulama](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) , bileşenin özelliklerini ve yöntemlerini sağlamak için bir bileşenin `BlazorRocksBase` temel sınıfını nasıl devralmasını gösterir.

*Pages/BlazorRocks. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

*BlazorRocksBase.cs*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

Temel sınıf `ComponentBase` ' dan türetilmelidir.

## <a name="import-components"></a>Bileşenleri içeri aktar

Razor ile yazılan bir bileşenin ad alanı temel alınarak belirlenir (öncelik sırasına göre):

* Razor dosyası ( *. Razor*) biçimlendirmesinde [@namespace](xref:mvc/views/razor#namespace) atama (`@namespace BlazorSample.MyNamespace`).
* Projenin proje dosyasında `RootNamespace` (`<RootNamespace>BlazorSample</RootNamespace>`).
* Proje dosyasının dosya adından ( *. csproj*) ve proje kökünden bileşen yolundan alınan proje adı. Örneğin Framework, *{Project root}/Pages/Index.Razor* (*BlazorSample. csproj*) ad alanına `BlazorSample.Pages` ' yi çözer. Bileşenler ad C# bağlama kurallarını izler. Bu örnekteki `Index` bileşeni için, kapsamdaki bileşenler tüm bileşenlerdir:
  * Aynı klasörde, *sayfalarda*.
  * Proje kökündeki, açıkça farklı bir ad alanı belirtmeyen bileşenler.

Farklı bir ad alanında tanımlanan bileşenler, Razor 'nin [@using](xref:mvc/views/razor#using) yönergesi kullanılarak kapsam içine getirilir.

@No__t-0 adlı başka bir bileşen *BlazorSample/Shared/* klasöründe varsa, bileşen aşağıdaki `@using` ifadesiyle `Index.razor` ' de kullanılabilir:

```cshtml
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Bileşenlere, [@using](xref:mvc/views/razor#using) yönergesini gerektirmeyen kendi tam adları kullanılarak da başvurulabilir:

```cshtml
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> @No__t-0 niteliği desteklenmez.
>
> Diğer ad `using` deyimleriyle bileşenleri içeri aktarma (örneğin, `@using Foo = Bar`) desteklenmez.
>
> Kısmen nitelenmiş adlar desteklenmez. Örneğin, `@using BlazorSample` ' ı ekleme ve `<Shared.NavMenu></Shared.NavMenu>` ile `NavMenu.razor` ' i eklemek desteklenmez.

## <a name="conditional-html-element-attributes"></a>Koşullu HTML öğesi öznitelikleri

HTML öğesi öznitelikleri, .NET değerine göre koşullu olarak işlenir. Değer `false` veya `null` ise, öznitelik işlenmez. Değer `true` ise, öznitelik küçültülmüş olarak işlenir.

Aşağıdaki örnekte `IsCompleted` öğesi öğenin biçimlendirmesinde `checked` ' in işlenip işlenmeyeceğini belirler:

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

@No__t-0 `true` ise, onay kutusu şu şekilde işlenir:

```html
<input type="checkbox" checked />
```

@No__t-0 `false` ise, onay kutusu şu şekilde işlenir:

```html
<input type="checkbox" />
```

Daha fazla bilgi için bkz. <xref:mvc/views/razor>.

> [!WARNING]
> .NET türü `bool` olduğunda, [Aria-basılan](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)gıbı bazı HTML öznitelikleri düzgün şekilde çalışmaz. Bu durumlarda, `bool` yerine `string` türü kullanın.

## <a name="raw-html"></a>Ham HTML

Dizeler normalde DOM metin düğümleri kullanılarak işlenir. Bu, içerdikleri tüm biçimlendirmenin yok sayıldığı ve değişmez değer olarak kabul edildiği anlamına gelir. Ham HTML işlemek için, HTML içeriğini `MarkupString` değerinde sarın. Değer HTML veya SVG olarak ayrıştırılır ve DOM 'a eklenir.

> [!WARNING]
> Güvenilmeyen bir kaynaktan oluşturulan ham HTML işleme bir **güvenlik riskidir** ve kaçınılması gerekir!

Aşağıdaki örnek, bir bileşenin işlenmiş çıktısına statik HTML içeriği bloğunu eklemek için `MarkupString` türünü kullanmayı gösterir:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>Şablonlu bileşenler

Şablonlu bileşenler, bir veya daha fazla UI şablonunu parametre olarak kabul eden bileşenlerdir, daha sonra bileşen işleme mantığının bir parçası olarak kullanılabilir. Şablonlu bileşenler, normal bileşenlerden daha yeniden kullanılabilir olan üst düzey bileşenleri yazmanıza izin verir. Birkaç örnek şunlardır:

* Kullanıcının tablo üst bilgisi, satırları ve altbilgisi için şablon belirtmesini sağlayan tablo bileşeni.
* Bir kullanıcının bir listedeki öğeleri işlemek için şablon belirlemesine izin veren bir liste bileşenidir.

### <a name="template-parameters"></a>Şablon parametreleri

Şablonlu bir bileşen, `RenderFragment` veya `RenderFragment<T>` türünde bir veya daha fazla bileşen parametresi belirtilerek tanımlanır. Bir işleme parçası, işlenecek Kullanıcı arabiriminin bir kesimini temsil eder. `RenderFragment<T>`, işleme parçası çağrıldığında belirtilebildiği bir tür parametresi alır.

`TableTemplate` bileşeni:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

Şablonlu bir bileşen kullanırken, şablon parametreleri parametre adlarıyla eşleşen alt öğeler kullanılarak belirtilebilir (`TableHeader` ve `RowTemplate` aşağıdaki örnekte):

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a>Şablon bağlam parametreleri

Öğe olarak geçirilen `RenderFragment<T>` türündeki bileşen bağımsız değişkenleri, `context` adlı bir örtük parametreye sahiptir (örneğin, yukarıdaki kod örneğinden, `@context.PetId`), ancak parametre adını alt öğedeki `Context` özniteliğini kullanarak değiştirebilirsiniz. Aşağıdaki örnekte, `RowTemplate` öğesinin `Context` özniteliği `pet` parametresini belirtir:

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

Alternatif olarak, bileşen öğesi üzerinde `Context` özniteliğini de belirtebilirsiniz. Belirtilen `Context` özniteliği belirtilen tüm şablon parametreleri için geçerlidir. Bu, örtük alt içerik (herhangi bir sarmalama alt öğesi olmadan) için içerik parametre adını belirtmek istediğinizde yararlı olabilir. Aşağıdaki örnekte, `Context` özniteliği `TableTemplate` öğesinde görünür ve tüm şablon parametreleri için geçerlidir:

```cshtml
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a>Genel olarak yazılmış bileşenler

Şablonlu bileşenler çoğunlukla genel olarak türdedir. Örneğin, `IEnumerable<T>` değerlerini işlemek için genel `ListViewTemplate` bir bileşen kullanılabilir. Genel bir bileşen tanımlamak için [@typeparam](xref:mvc/views/razor#typeparam) yönergesini kullanarak tür parametrelerini belirtin:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

Genel türsüz bileşenleri kullanırken tür parametresi mümkünse algılanır:

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Aksi halde tür parametresi, tür parametresinin adıyla eşleşen bir öznitelik kullanılarak açıkça belirtilmelidir. Aşağıdaki örnekte, `TItem="Pet"` türü belirtir:

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>Değerleri ve parametreleri basamaklama

Bazı senaryolarda, özellikle birden çok bileşen katmanı olduğunda, [bileşen parametreleri](#component-parameters)kullanarak bir üst bileşenden bir alt bileşene veri akışı yapmak uygun değildir. Değerleri ve parametreleri basamaklama, bir üst bileşenin tüm alt bileşenlerine değer sağlaması için kullanışlı bir yol sağlayarak bu sorunu çözebilir. Basamaklı değerler ve parametreler, bileşenlerin koordinasyonu için bir yaklaşım sağlar.

### <a name="theme-example"></a>Tema örneği

Örnek uygulamadan aşağıdaki örnekte, `ThemeInfo` sınıfı, uygulamanın belirli bir bölümü içindeki tüm düğmelerin aynı stili paylaşması için bileşen hiyerarşisinin akmasını sağlayacak tema bilgilerini belirtir.

*Uıthemeclasses/Themeınfo. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Bir üst bileşen basamaklı değer bileşeni kullanılarak basamaklı bir değer sağlayabilir. @No__t-0 bileşeni, bileşen hiyerarşisinin bir alt ağacını sarmalanmış ve bu alt ağaçta bulunan tüm bileşenlere tek bir değer sağlar.

Örneğin, örnek uygulama, `@Body` özelliğinin düzen gövdesini oluşturan tüm bileşenler için bir geçişli parametre olarak uygulamanın düzenlerindeki tema bilgilerini (`ThemeInfo`) belirtir. `ButtonClass` ' a, düzen bileşeninde `btn-success` değeri atanır. Tüm alt bileşenler, `ThemeInfo` basamaklı nesne aracılığıyla bu özelliği kullanabilir.

`CascadingValuesParametersLayout` bileşeni:

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Basamaklı değerleri kullanmak için, bileşenler `[CascadingParameter]` özniteliğini kullanarak Geçişli Parametreler bildirir. Basamaklı değerler, türe göre basamaklı parametrelere bağlanır.

Örnek uygulamada `CascadingValuesParametersTheme` bileşeni, `ThemeInfo` geçişli değeri basamaklı bir parametreye bağlar. Parametresi, bileşen tarafından görünen düğmelerden birine ait CSS sınıfını ayarlamak için kullanılır.

`CascadingValuesParametersTheme` bileşeni:

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Aynı alt ağaçta aynı türdeki birden çok değeri basamakla, her bir `CascadingValue` bileşenine ve karşılık gelen `CascadingParameter` ' ye benzersiz bir `Name` dizesi sağlayın. Aşağıdaki örnekte, iki `CascadingValue` bileşeni, ada göre farklı `MyCascadingType` örneklerini basamakla:

```cshtml
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

Alt bileşende, basamaklı parametreler değerlerini, üst bileşendeki ilgili basamaklı değerleri ada göre alır:

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>TabSet örneği

Basamaklı parametreler, bileşenlerin bileşen hiyerarşisinde işbirliği yapmasına de olanak tanır. Örneğin, örnek uygulamada aşağıdaki *Tabset* örneğini göz önünde bulundurun.

Örnek uygulama, sekmelerin uygulandığı `ITab` arabirimine sahiptir:

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

@No__t-0 bileşeni, çeşitli `Tab` bileşenleri içeren `TabSet` bileşenini kullanır:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

Alt `Tab` bileşenleri, `TabSet` ' e açıkça parametre olarak geçirilmemektedir. Bunun yerine, alt `Tab` bileşenleri, `TabSet` ' in alt içeriğinin bir parçasıdır. Ancak, `TabSet` ' ın, üst bilgileri ve etkin sekmeyi işleyebilmesi için her bir `Tab` bileşeni hakkında hala bilmeleri gerekir. Ek kod gerektirmeden bu koordinasyonu etkinleştirmek için `TabSet` bileşeni, kendisini daha sonra alt `Tab` bileşenleri tarafından çekilen *basamaklı bir değer olarak sağlayabilir* .

`TabSet` bileşeni:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

@No__t-0 bileşenleri, kapsayan `TabSet` ' i basamaklı bir parametre olarak yakalar, bu nedenle `Tab` bileşenleri kendilerini `TabSet` ' e ekler ve sekmenin etkin olduğu koordine edilir.

`Tab` bileşeni:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor şablonları

Oluşturma parçaları Razor şablonu sözdizimi kullanılarak tanımlanabilir. Razor şablonları, bir UI parçacığı tanımlamak ve aşağıdaki biçimi varsaymak için bir yoldur:

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

Aşağıdaki örnek, `RenderFragment` ve `RenderFragment<T>` değerlerinin nasıl belirtildiğini ve şablonlarının doğrudan bir bileşende nasıl işleneceğini gösterir. Oluşturma parçaları, [şablonlu bileşenlere](#templated-components)bağımsız değişken olarak da geçirilebilir.

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Önceki kodun işlenmiş çıktısı:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a>El ile RenderTreeBuilder mantığı

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`, bileşenleri ve öğeleri işlemek için, C# kodda el ile bileşen oluşturma gibi yöntemler sağlar.

> [!NOTE]
> Bileşen oluşturmak için `RenderTreeBuilder` kullanımı gelişmiş bir senaryodur. Hatalı biçimlendirilmiş bir bileşen (örneğin, kapatılmamış bir biçimlendirme etiketi) tanımsız davranışa neden olabilir.

Başka bir bileşende el ile kullanılabilecek aşağıdaki `PetDetails` bileşenini göz önünde bulundurun:

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Aşağıdaki örnekte, `CreateComponent` yöntemindeki döngü üç `PetDetails` bileşeni oluşturur. Bileşenleri oluşturmak için `RenderTreeBuilder` yöntemleri çağrılırken (`OpenComponent` ve `AddAttribute`), dizi numaraları kaynak kodu satır sayılarıdır. Blazor fark algoritması, ayrı çağrı etkinleştirmeleri değil ayrı kod satırlarına karşılık gelen sıra numaralarına dayanır. @No__t-0 yöntemleriyle bir bileşen oluştururken, dizi numaralarına yönelik bağımsız değişkenleri sabit olarak kodlayın. **Sıra numarasını oluşturmak için bir hesaplama veya sayaç kullanmak kötü performansa neden olabilir.** Daha fazla bilgi için bkz. [kod satırı numaralarıyla Ilgili sıra numaraları ve yürütme sırası çalışmıyor](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) bölümü.

`BuiltContent` bileşeni:

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> ! WARNING @No__t-0 ' daki türler, işleme işlemlerinin *sonuçlarının* işlenmesine izin verir. Bunlar, Blazor Framework uygulamasının iç ayrıntılardır. Bu türlerin *dengesizleşilmesi* ve gelecekteki sürümlerde değişikliğe tabi olması gerekir.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Sıra numaraları, kod satırı numaralarıyla ilgilidir ve yürütme sırası değildir

Blazor `.razor` dosyaları her zaman derlenir. Derleme adımı, çalışma zamanında uygulama performansını geliştiren bilgileri eklemek için kullanılabilir olduğundan, bu büyük olasılıkla `.razor` için harika bir avantajdır.

Bu geliştirmelerin önemli bir örneği *sıra numarası*içerir. Sıra numaraları, hangi çıkışların ayrı ve sıralı kod satırlarından geldiğini çalışma zamanına işaret ediyor. Çalışma zamanı, doğrusal bir zamanda, genel ağaç farkı algoritması için genellikle mümkün olandan çok daha hızlı olan etkili ağaç SLA 'ları oluşturmak için bu bilgileri kullanır.

Aşağıdaki basit `.razor` dosyasını göz önünde bulundurun:

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

Yukarıdaki kod, aşağıdakine benzer şekilde derlenir:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Kod ilk kez yürütüldüğünde, `someFlag` `true` ise, Oluşturucu şunları alır:

| Dizisi | Type      | Data   |
| :------: | --------- | :----: |
| 0        | Metin düğümü | Adı  |
| 1\.        | Metin düğümü | Saniye |

@No__t-0 `false` ' in olduğunu düşünün ve biçimlendirme yeniden işlenir. Bu kez, Oluşturucu şunları alır:

| Dizisi | Type       | Data   |
| :------: | ---------- | :----: |
| 1\.        | Metin düğümü  | Saniye |

Çalışma zamanı bir fark gerçekleştirdiğinde, sırasıyla `0` olan öğenin kaldırıldığını görür, bu nedenle aşağıdaki önemsiz *düzenleme betiğini*oluşturur:

* İlk metin düğümünü kaldırın.

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a>Program aracılığıyla sıra numaraları oluşturursanız ne yanlış gider

Aşağıdaki işleme Ağacı Oluşturucusu mantığını yazmanız yerine düşünün:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Şimdi ilk çıktı:

| Dizisi | Type      | Data   |
| :------: | --------- | :----: |
| 0        | Metin düğümü | Adı  |
| 1\.        | Metin düğümü | Saniye |

Bu sonuç önceki bir durum ile aynıdır, bu nedenle olumsuz bir sorun yoktur. `someFlag`, ikinci işlemede `false` ' dir ve çıktı:

| Dizisi | Type      | Data   |
| :------: | --------- | ------ |
| 0        | Metin düğümü | Saniye |

Bu kez, fark algoritması *iki* değişikliğin oluştuğunu görür ve algoritma aşağıdaki düzenleme betiğini üretir:

* İlk metin düğümünün değerini `Second` olarak değiştirin.
* İkinci metin düğümünü kaldırın.

Sıra numaralarının oluşturulması, `if/else` dallarının ve döngülerinin özgün kodda bulunduğu yer hakkındaki tüm yararlı bilgileri kaybetti. Bu, daha önce olduğu gibi bir fark **ile iki kez** sonuçlanır.

Bu, önemsiz bir örnektir. Karmaşık ve derin iç içe yapıları ve özellikle döngülerle daha gerçekçi durumlarda performans maliyeti daha önemlidir. Hangi döngü bloklarının veya dallarının eklendiğini veya kaldırıldığını hemen belirlemek yerine, fark algoritmasının işleme ağaçlarına katmaları ve genellikle eski ve yeni yapıların nasıl olduğu hakkında yanlış bir biçimde düzenleme betikleri oluşturması birbirleriyle ilişkilendir.

#### <a name="guidance-and-conclusions"></a>Kılavuz ve ekibinizle

* Sıra numaraları dinamik olarak oluşturulursa uygulama performansı de vardır.
* Altyapı, derleme zamanında yakalanmadığı takdirde gerekli bilgiler bulunmadığından, çalışma zamanında kendi sıra numaralarını otomatik olarak oluşturamaz.
* El ile uygulanan `RenderTreeBuilder` Logic uzun bloklar yazmayın. @No__t-0 dosyalarını tercih edin ve derleyicinin sıra numaralarıyla uğraşmak için izin verin. @No__t-0 mantığının el ile kurtulamadıysanız, uzun kod bloklarını `OpenRegion` @ no__t-2 @ no__t-3 çağrılarında kaydırılmış daha küçük parçalara ayırın. Her bölge kendi ayrı dizi numaralarına sahiptir, bu nedenle her bölge içinde sıfırdan (veya herhangi bir rastgele sayıdan) yeniden başlatabilirsiniz.
* Dizi numaraları sabit kodluysa, fark algoritması yalnızca değer değerinde sıra numaralarının artırılmasını gerektirir. İlk değer ve boşluklar ilgisiz. Tek bir seçenek, kod satırı numarasını sıra numarası olarak kullanmak veya sıfırdan başlayıp bir ya da yüzlerce (ya da tercih edilen aralığa) artırmak için kullanılır. 
* Blazor, sıra numaralarını kullanır, diğer ağaç dağıtma Kullanıcı arabirimi çerçeveleri bunları kullanmaz. Dizi numaraları kullanıldığında ve Blazor, `.razor` dosyalarını yazan geliştiriciler için otomatik olarak sıra numaralarıyla ilgilenen bir derleme adımının avantajına sahiptir.

## <a name="localization"></a>Yerelleştirme

Blazor Server uygulamaları, [Yerelleştirme ara yazılımı](xref:fundamentals/localization#localization-middleware)kullanılarak yerelleştirilir. Ara yazılım, uygulamadan kaynak isteyen kullanıcılar için uygun kültürü seçer.

Kültür aşağıdaki yaklaşımlardan biri kullanılarak ayarlanabilir:

* [Çerezler](#cookies)
* [Kültürü seçmek için Kullanıcı arabirimi sağlama](#provide-ui-to-choose-the-culture)

Daha fazla bilgi ve örnek için bkz. <xref:fundamentals/localization>.

### <a name="cookies"></a>Özgü

Yerelleştirme kültürü tanımlama bilgisi kullanıcının kültürünü kalıcı hale getirebilirler. Tanımlama bilgisi, uygulamanın ana bilgisayar sayfasının `OnGet` yöntemiyle oluşturulur (*Pages/Host. cshtml. cs*). Yerelleştirme ara yazılımı, sonraki isteklerde Kullanıcı kültürünü ayarlamak için tanımlama bilgilerini okur. 

Tanımlama bilgisinin kullanımı, WebSocket bağlantısının kültürü doğru şekilde yaymasını sağlar. Yerelleştirme şemaları URL yolunu veya sorgu dizesini temel alıyorsa, düzen WebSockets ile çalışmayabilir, bu nedenle kültürü kalıcı hale getiremeyebilir. Bu nedenle, yerelleştirme kültürü tanımlama bilgisinin kullanılması önerilen yaklaşımdır.

Kültür bir yerelleştirme tanımlama bilgisinde kalıcı hale getirilir kültür atamak için herhangi bir teknik kullanılabilir. Uygulamanın zaten sunucu tarafı ASP.NET Core için bir yerelleştirme şeması varsa, uygulamanın var olan yerelleştirme altyapısını kullanmaya devam edin ve uygulamanın şeması içinde yerelleştirme kültür tanımlama bilgisini ayarlayın.

Aşağıdaki örnekte, yerelleştirme ara yazılımı tarafından okunabilen bir tanımlama bilgisinde geçerli kültürün nasıl ayarlanacağı gösterilmektedir. Blazor Server uygulamasında aşağıdaki içeriklerle bir *Pages/Host. cshtml. cs* dosyası oluşturun:

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

Yerelleştirme uygulamada işlenir:

1. Tarayıcı, uygulamaya bir ilk HTTP isteği gönderir.
1. Kültür, yerelleştirme ara yazılımı tarafından atanır.
1. *_Host. cshtml. cs* içindeki `OnGet` yöntemi, yanıtın bir parçası olarak bir tanımlama bilgisinde kültürü devam ettirir.
1. Tarayıcı, etkileşimli bir Blazor Server oturumu oluşturmak için bir WebSocket bağlantısı açar.
1. Yerelleştirme ara yazılımı tanımlama bilgisini okur ve kültürü atar.
1. Blazor sunucusu oturumu doğru kültür ile başlar.

## <a name="provide-ui-to-choose-the-culture"></a>Kültürü seçmek için Kullanıcı arabirimi sağlama

Bir kullanıcının bir kültür seçmesine izin vermek için kullanıcı ARABIRIMI sağlamak üzere bir *yeniden yönlendirme tabanlı yaklaşım* önerilir. Bu işlem, bir Kullanıcı güvenli bir kaynağa erişmeye çalıştığında bir Web uygulamasında gerçekleşmelere benzer @ no__t-0kullanıcı oturum açma sayfasına yönlendirilir ve ardından özgün kaynağa yeniden yönlendirilir. 

Uygulama, bir denetleyiciye yeniden yönlendirme yoluyla kullanıcının seçili kültürünü devam ettirir. Denetleyici kullanıcının seçili kültürünü bir tanımlama bilgisine ayarlar ve kullanıcıyı özgün URI 'ye yeniden yönlendirir.

Bir tanımlama bilgisinde kullanıcının seçili kültürünü ayarlamak ve özgün URI 'ye yeniden yönlendirmeyi gerçekleştirmek için sunucuda bir HTTP uç noktası oluşturun:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Açık yeniden yönlendirme saldırılarını engellemek için `LocalRedirect` eylem sonucunu kullanın. Daha fazla bilgi için bkz. <xref:security/preventing-open-redirects>.

Aşağıdaki bileşen, Kullanıcı bir kültür seçtiğinde ilk yeniden yönlendirmenin nasıl gerçekleştirileceği hakkında bir örnek göstermektedir:

```cshtml
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a>Blazor uygulamalarında .NET yerelleştirme senaryolarını kullanma

Blazor uygulamaları içinde, aşağıdaki .NET yerelleştirme ve genelleştirme senaryoları kullanılabilir:

* . NET ' in kaynak sistemi
* Kültüre özgü sayı ve Tarih biçimlendirmesi

Blazor 'ın `@bind` işlevselliği, kullanıcının geçerli kültürüne göre Genelleştirme gerçekleştirir. Daha fazla bilgi için [veri bağlama](#data-binding) bölümüne bakın.

Sınırlı bir ASP.NET Core yerelleştirme senaryosu kümesi şu anda desteklenmektedir:

* `IStringLocalizer<>` *,* Blazor uygulamalarında desteklenir.
* `IHtmlLocalizer<>`, `IViewLocalizer<>` ve veri ek açıklamaları yerelleştirme ASP.NET Core MVC senaryolarından ve Blazor uygulamalarında **desteklenmez** .

Daha fazla bilgi için bkz. <xref:fundamentals/localization>.

## <a name="scalable-vector-graphics-svg-images"></a>Ölçeklenebilir vektör grafik (SVG) görüntüleri

Blazor tarafından oluşturulduğundan, ölçeklenebilir vektör grafik (SVG) görüntüleri ( *. SVG*) dahil olmak üzere tarayıcıda desteklenen görüntüler `<img>` etiketi aracılığıyla desteklenir:

```html
<img alt="Example image" src="some-image.svg" />
```

Benzer şekilde, SVG görüntüleri bir stil sayfası dosyasının ( *. css*) CSS kurallarında desteklenir:

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Ancak, satır içi SVG işaretlemesi tüm senaryolarda desteklenmez. Bir `<svg>` etiketini doğrudan bir bileşen dosyasına ( *. Razor*) yerleştirirseniz, temel görüntü işleme desteklenir, ancak birçok gelişmiş senaryo henüz desteklenmez. Örneğin, `<use>` etiketleri şu anda dikkate alınamaz ve `@bind` bazı SVG etiketleriyle kullanılamaz. Gelecekteki bir sürümde bu sınırlamaları ele almayı bekliyoruz.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/blazor/server> &ndash; kaynak tükenmesi ile Çekişmek zorunda olması gereken Blazor sunucu uygulamaları oluşturmaya yönelik yönergeler Içerir.
