---
title: ASP.NET Core Blazor yaşam döngüsü
author: guardrex
description: ASP.NET Core Blazor uygulamalarda bileşen yaşam Razor döngüsü yöntemlerini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 87c65776684f9cc91b868b8e88926e46b25592ff
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771526"
---
# <a name="aspnet-core-blazor-lifecycle"></a>ASP.NET Core Blazor yaşam döngüsü

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

Çerçeve Blazor , zaman uyumlu ve zaman uyumsuz yaşam döngüsü yöntemlerini içerir. Bileşen başlatma ve işleme sırasında bileşenlerde ek işlemler gerçekleştirmek için yaşam döngüsü yöntemlerini geçersiz kılın.

## <a name="lifecycle-methods"></a>Yaşam döngüsü yöntemleri

### <a name="component-initialization-methods"></a>Bileşen başlatma yöntemleri

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> , bileşen ilk parametrelerini ana bileşeninden aldıktan sonra başlatıldığında çağrılır. Bileşen `OnInitializedAsync` zaman uyumsuz bir işlem gerçekleştirdiğinde ve işlem tamamlandığında yenilenmesi gerektiğinde kullanın.

Zaman uyumlu bir işlem için, `OnInitialized`geçersiz kılın:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Zaman uyumsuz bir işlem gerçekleştirmek için, `OnInitializedAsync` işlem üzerinde `await` anahtar sözcüğünü geçersiz kılın ve kullanın:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor[İçerik](xref:blazor/hosting-model-configuration#render-mode) araması `OnInitializedAsync` yapan sunucu uygulamaları **_iki kez_**.

* Bir kez, bileşen sayfanın bir parçası olarak başlangıçta statik olarak işlendiğinde.
* Tarayıcı sunucuya geri bir bağlantı kurduğunda ikinci bir zaman.

İçindeki `OnInitializedAsync` geliştirici kodunun iki kez çalıştırılmasını engellemek için [prerendering sonrasında durum bilgisi olan yeniden bağlanma](#stateful-reconnection-after-prerendering) bölümüne bakın.

Blazor Sunucu uygulaması prerendering olsa da, tarayıcıyla bir bağlantı kurulmadığından, JavaScript 'e çağırma gibi bazı eylemler mümkün değildir. Bileşenler, ön işlenmiş olduğunda farklı şekilde işlenmesi gerekebilir. Daha fazla bilgi için bkz. [uygulamanın ne zaman prerendering](#detect-when-the-app-is-prerendering) bölümüne bakın.

Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır. Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.

### <a name="before-parameters-are-set"></a>Parametreler ayarlanmadan önce

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>işleme ağacındaki bileşenin üst öğesi tarafından sağlanan parametreleri ayarlar:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>her çağrılışında `SetParametersAsync` parametre değerleri kümesinin tamamını içerir.

Varsayılan `SetParametersAsync` uygulama, `[Parameter]` `ParameterView`her bir özelliğin değerini içinde karşılık gelen bir değere sahip `[CascadingParameter]` veya özniteliğiyle ayarlar. İçinde `ParameterView` karşılık gelen bir değere sahip olmayan parametreler değişmeden bırakılır.

Çağrılmadıysa `base.SetParametersAync` , özel kod gelen parametreler değerini gerekli herhangi bir şekilde yorumlayabilir. Örneğin, sınıftaki özelliklere gelen parametreleri atama gereksinimi yoktur.

Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır. Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.

### <a name="after-parameters-are-set"></a>Parametreler ayarlandıktan sonra

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> şu şekilde adlandırılır:

* Bileşen başlatıldığında ve üst bileşeninden ilk parametre kümesini aldığında.
* Üst bileşen yeniden oluşturup şunları sağlar:
  * Yalnızca en az bir parametresinin değiştiği, bilinen temel sabit türler.
  * Tüm karmaşık türsüz parametreler. Çerçeve, karmaşık yazılmış bir parametrenin değerlerinin dahili olarak değişip değişmediğini bilmez, bu yüzden parametre kümesini değiştirilmiş olarak değerlendirir.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> `OnParametersSetAsync` Yaşam döngüsü olayında parametre ve özellik değerleri uygulanırken zaman uyumsuz iş olması gerekir.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır. Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.

### <a name="after-component-render"></a>Bileşen oluşturulduktan sonra

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> bir bileşen işlemeyi tamamladıktan sonra çağrılır. Öğe ve bileşen başvuruları bu noktada doldurulur. İşlenmiş DOM öğelerinde çalışan üçüncü taraf JavaScript kitaplıklarını etkinleştirme gibi, işlenmiş içeriği kullanarak ek başlatma adımları gerçekleştirmek için bu aşamayı kullanın.

Ve `firstRender` `OnAfterRender`için `OnAfterRenderAsync` parametresi:

* , Bileşen örneği `true` ilk kez işlendiğinde olarak ayarlanır.
* Başlatma işinin yalnızca bir kez gerçekleştirildiğinden emin olmak için kullanılabilir.

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> `OnAfterRenderAsync` Yaşam döngüsü olayında işleme hemen sonra zaman uyumsuz çalışma gerçekleşmelidir.
>
> Bir <xref:System.Threading.Tasks.Task> öğesinden `OnAfterRenderAsync`döndürseniz bile, çerçeve bu görev tamamlandıktan sonra bileşen için başka bir işleme çevrimi zamanlayamaz. Bu, sonsuz bir işleme döngüsünden kaçınmaktır. Diğer yaşam döngüsü yöntemlerinden farklı olduğundan, döndürülen görev tamamlandığında daha fazla işleme döngüsü zamanlayabilirsiniz.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender`ve `OnAfterRenderAsync` *sunucuda prerendering çağrıldığında çağrılmaz.*

Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır. Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.

### <a name="suppress-ui-refreshing"></a>UI yenilemeyi bastır

UI <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> yenilemeyi gizlemek için geçersiz kılın. Uygulama döndürürse `true`, Kullanıcı arabirimi yenilenir:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender`bileşen her işlendiğinde çağrılır.

`ShouldRender` Geçersiz kılınsa bile, bileşen her zaman ilk olarak işlenir.

## <a name="state-changes"></a>Durum değişiklikleri

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>bileşene durumunun değiştiğini bildirir. Uygun olduğunda, çağırma `StateHasChanged` bileşenin yeniden yönlendirilmesine neden olur.

## <a name="handle-incomplete-async-actions-at-render"></a>İşleme sırasında tamamlanmamış zaman uyumsuz eylemleri işle

Yaşam döngüsü olaylarında gerçekleştirilen zaman uyumsuz eylemler, bileşen işlenmeden önce tamamlanmamış olabilir. Yaşam döngüsü yöntemi `null` yürütülürken nesneler, verilerle tamamen doldurulmuş olabilir. Nesnelerin başlatıldığını onaylamak için işleme mantığı sağlayın. Nesneler olduğunda `null`yer tutucu Kullanıcı arabirimi öğelerini (örneğin, bir yükleme iletisi) işleme.

Blazor Şablonların `FetchData` bileşeninde, `OnInitializedAsync` Asychronously (`forecasts`) tahmin verileri almak için geçersiz kılınır. Ne `forecasts` zaman `null`olduğunda, kullanıcıya bir yükleme iletisi görüntülenir. İşlem `OnInitializedAsync` tamamlandıktan `Task` sonra, bileşen güncelleştirilmiş duruma geri döner.

Blazor Sunucu şablonunda *Pages/fetchdata. Razor* :

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>IDisposable ile bileşen atma

Bir bileşen uygularsa <xref:System.IDisposable>, bileşen kullanıcı arabiriminden kaldırıldığında [Dispose yöntemi](/dotnet/standard/garbage-collection/implementing-dispose) çağırılır. Aşağıdaki bileşen ve `Dispose` yöntemini `@implements IDisposable` kullanır:

```razor
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

> [!NOTE]
> ' <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> In `Dispose` çağrılması desteklenmez. `StateHasChanged`oluşturucuyu aşağı doğru olarak çağrılabilir, bu nedenle bu noktada UI güncelleştirmelerinin kullanılması desteklenmez.

.NET etkinliklerinden olay işleyicilerini kaldırma. Aşağıdaki [ Blazor form](xref:blazor/forms-validation) örnekleri, `Dispose` yönteminde bir olay işleyicisinin nasıl geri yükleneceğini göstermektedir:

* Özel alan ve lambda yaklaşımı

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Özel yöntem yaklaşımı

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Hataları işleme

Yaşam döngüsü yöntemi yürütme sırasında hataları işleme hakkında bilgi için bkz <xref:blazor/handle-errors#lifecycle-methods>..

## <a name="stateful-reconnection-after-prerendering"></a>Prerendering sonrasında durum bilgisi olan yeniden bağlanma

Bir Blazor sunucu `RenderMode` `ServerPrerendered`uygulamasında,, bileşen başlangıçta sayfanın bir parçası olarak statik olarak işlenir. Tarayıcı sunucuya geri bir bağlantı kurduğunda, bileşen *yeniden*işlenir ve bileşen artık etkileşimli olur. Bileşeni başlatmak için [Onbaşlatılmış {Async}](#component-initialization-methods) yaşam döngüsü yöntemi varsa, yöntem *iki kez*yürütülür:

* Bileşen statik olarak önceden kullanılırken.
* Sunucu bağlantısı kurulduktan sonra.

Bu, bileşen son işlendiğinde Kullanıcı arabiriminde görünen verilerde fark edilebilir bir değişikliğe neden olabilir.

Bir Blazor sunucu uygulamasında çift işleme senaryosunu önlemek için:

* Prerendering sırasında durumu önbelleğe almak için kullanılabilecek bir tanımlayıcı geçirin ve uygulamayı yeniden başlattıktan sonra durumu alma.
* Bileşen durumunu kaydetmek için prerendering sırasında tanımlayıcıyı kullanın.
* Önbelleğe alınan durumu almak için prerendering öğesinden sonra tanımlayıcıyı kullanın.

Aşağıdaki kod, bir şablon tabanlı `WeatherForecastService` Blazor sunucu uygulamasında, Çift işlemeyi engelleyen güncelleştirilmiş bir güncelleme göstermektedir:

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

Hakkında `RenderMode`daha fazla bilgi için bkz <xref:blazor/hosting-model-configuration#render-mode>..

## <a name="detect-when-the-app-is-prerendering"></a>Uygulamanın ne zaman prerendering olduğunu Algıla

[!INCLUDE[](~/includes/blazor-prerendering.md)]
