---
title: ASP.NET Core uygulamalardaki hataları işleme Blazor
author: guardrex
description: Blazor Blazor İşlenmemiş özel durumları nasıl yönettiğini ve hataları algılayan ve işleyen uygulamalar geliştirme ASP.NET Core nasıl yapılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: cca4d8ce0c783f26f33cb7b2b1535a4bc53384d6
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424338"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a>ASP.NET Core uygulamalardaki hataları işleme Blazor

[Steve Sanderson](https://github.com/SteveSandersonMS) tarafından

Bu makalede Blazor , işlenmemiş özel durumların nasıl yönetildiği ve hataları algılayan ve işleyen uygulamalar geliştirileceği açıklanır.

## <a name="detailed-errors-during-development"></a>Geliştirme sırasında ayrıntılı hatalar

BlazorGeliştirme sırasında bir uygulama düzgün çalışmadığı zaman, uygulamanın ayrıntılı hata bilgilerini alma sorunu gidermeye ve soruna yardımcı olur. Bir hata oluştuğunda, Blazor uygulamalar ekranın alt kısmında altın bir çubuk görüntüler:

* Geliştirme sırasında altın çubuk, özel durumu görebileceğiniz tarayıcı konsoluna yönlendirir.
* Üretimde, altın çubuk kullanıcıya bir hata oluştuğunu bildirir ve tarayıcıyı yenilemeyi önerir.

Bu hata işleme deneyimi için Kullanıcı arabirimi, Blazor proje şablonlarının bir parçasıdır.

BlazorWebassembly uygulamasında, *Wwwroot/index.html* dosyasındaki deneyimi özelleştirin:

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

Bir Blazor sunucu uygulamasında, *Pages/_Host. cshtml* dosyasındaki deneyimi özelleştirin:

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

`blazor-error-ui`Öğe, şablonlara dahil edilen stillerle Blazor (*Wwwroot/CSS/site. css*) gizlenir ve bir hata oluştuğunda gösterilir:

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a>BlazorSunucu uygulamasının işlenmemiş özel durumlara nasıl yeniden davranması

BlazorSunucu, durum bilgisi olan bir çerçevedir. Kullanıcılar bir uygulamayla etkileşim kurarken, *devre*olarak bilinen sunucuya bir bağlantı sağlar. Devre, etkin bileşen örneklerini ve diğer birçok durum düzeyini barındırır; örneğin:

* Bileşenlerin en son işlenmiş çıktısı.
* İstemci tarafı olayları tarafından tetiklenebilecek geçerli olay işleme temsilcileri kümesi.

Bir Kullanıcı uygulamayı birden çok tarayıcı sekmelerinde açarsa, birden çok bağımsız devreler vardır.

Blazorişlenmemiş özel durumları, nerede gerçekleştikleri devreye göre önemli olarak değerlendirir. İşlenmeyen bir özel durum nedeniyle devre sonlandırılırsa, Kullanıcı yalnızca yeni bir bağlantı oluşturmak için sayfayı yeniden yükleyerek uygulamayla etkileşime geçerek devam edebilir. Diğer kullanıcılar veya diğer tarayıcı sekmeleri için devre dışı bırakılmış olan Sonlandırıcı dışındaki devreleri etkilenmez. Bu senaryo, çöktüğü bir masaüstü uygulamasına benzerdir. Kilitlenen uygulamanın yeniden başlatılması gerekiyor, ancak diğer uygulamalar etkilenmedi.

Aşağıdaki nedenlerden dolayı işlenmeyen bir özel durum oluştuğunda devre sonlandırılır:

* İşlenmeyen bir özel durum, genellikle devre dışı bir durumda bırakır.
* İşlenmemiş bir özel durumdan sonra uygulamanın normal işlemi garanti edilemez.
* Devre devam ederse uygulamada güvenlik açıkları görünebilir.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Geliştirici kodundaki işlenmemiş özel durumları yönetme

Bir uygulamanın bir hatadan sonra devam edebilmesi için, uygulamanın hata işleme mantığı olması gerekir. Bu makalenin sonraki bölümlerinde işlenmemiş özel durumların olası kaynakları açıklanır.

Üretimde, çerçeve özel durum iletilerini veya yığın izlemelerini Kullanıcı arabiriminde işlemez. Özel durum iletilerini veya yığın izlemelerini işleme şu şekilde olabilir:

* Hassas bilgileri son kullanıcılara açıklayın.
* Kötü niyetli bir kullanıcının uygulama, sunucu veya ağ güvenliğini tehlikeye atabilecek bir uygulamada zayıf yanları bulmasına yardımcı olun.

## <a name="log-errors-with-a-persistent-provider"></a>Kalıcı bir sağlayıcıyla hataları günlüğe kaydet

İşlenmeyen bir özel durum oluşursa, özel durum <xref:Microsoft.Extensions.Logging.ILogger> hizmet kapsayıcısında yapılandırılan örneklere kaydedilir. Varsayılan olarak, Blazor uygulamalar konsol günlüğü sağlayıcısı ile konsol çıktısına kaydedilir. Günlük boyutunu ve günlük döndürmesini yöneten bir sağlayıcı ile daha kalıcı bir konuma oturum açmayı düşünün. Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.

Geliştirme sırasında, Blazor hata ayıklamaya yardımcı olması için genellikle özel durumların tüm ayrıntılarını tarayıcı konsoluna gönderir. Üretimde, tarayıcı konsolundaki ayrıntılı hatalar varsayılan olarak devre dışıdır. Bu, hataların istemcilere gönderilmediği, ancak özel durumun tam ayrıntılarının hala sunucu tarafında günlüğe kaydedildiği anlamına gelir. Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.

Hangi olayların günlüğe kaydedileceğini ve günlüğe kaydedilen olayların önem düzeyi düzeyini karar vermelisiniz. Saldırgan kullanıcılar hataları kasıtlı olarak tetikleyebiliyor olabilir. Örneğin, `ProductId` ürün ayrıntılarını görüntüleyen bir BILEŞENIN URL 'sinde, bilinmeyen bir hatadan olay günlüğe kaydetme. Tüm hatalar günlüğe kaydetme için yüksek önem derecesine sahip olaylar olarak değerlendirilmemelidir.

Daha fazla bilgi için bkz. <xref:fundamentals/logging/index#create-logs-in-blazor>.

## <a name="places-where-errors-may-occur"></a>Hataların gerçekleşebileceği yerleri

Çerçeve ve uygulama kodu aşağıdaki konumlardan herhangi birinde işlenmeyen özel durumları tetikleyebiliriz:

* [Bileşen örneği oluşturma](#component-instantiation)
* [Yaşam döngüsü yöntemleri](#lifecycle-methods)
* [İşleme mantığı](#rendering-logic)
* [Olay işleyicileri](#event-handlers)
* [Bileşen elden çıkarma](#component-disposal)
* [JavaScript ile birlikte çalışma](#javascript-interop)
* [BlazorSunucu rerendering](#blazor-server-prerendering)

Önceki işlenmemiş özel durumlar, bu makalenin aşağıdaki bölümlerinde açıklanmıştır.

### <a name="component-instantiation"></a>Bileşen örneği oluşturma

BlazorBir bileşenin örneğini oluşturduğunda:

* Bileşenin Oluşturucusu çağrılır.
* Bileşen Oluşturucu ya da özniteliği aracılığıyla bileşen oluşturucusuna sağlanan tek başına olmayan hizmetlerin oluşturucuları [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) çağırılır.

BlazorHerhangi bir özellik için yürütülen herhangi bir Oluşturucu veya ayarlayıcı `[Inject]` işlenmeyen bir özel durum oluşturduğunda sunucu devresi başarısız olur. Framework bileşeni örneklemediğinden özel durum önemlidir. Oluşturucu mantığı özel durumlar oluşturmayabilir, uygulama hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesini kullanarak özel durumları yakalemelidir.

### <a name="lifecycle-methods"></a>Yaşam döngüsü yöntemleri

Bir bileşenin ömrü boyunca Blazor aşağıdaki [yaşam döngüsü yöntemlerini](xref:blazor/lifecycle)çağırır:

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

Herhangi bir yaşam döngüsü yöntemi, zaman uyumlu veya zaman uyumsuz olarak bir özel durum oluşturursa, özel durum sunucu devresi için önemli olur Blazor . Bileşenler için yaşam döngüsü yöntemlerinde hatalarla başa çıkmak için hata işleme mantığı ekleyin.

Aşağıdaki örnekte, bir `OnParametersSetAsync` ürünü elde etmek için bir yöntemi çağırır:

* Yönteminde oluşan bir özel durum, `ProductRepository.GetProductByIdAsync` bir `try-catch` ifadesiyle işlenir.
* `catch`Blok yürütüldüğünde:
  * `loadFailed``true`, kullanıcıya bir hata iletisi göstermek için kullanılan olarak ayarlanır.
  * Hata günlüğe kaydedilir.

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>İşleme mantığı

Bir bileşen dosyasındaki bildirim temelli biçimlendirme `.razor` adlı bir C# yöntemine derlenir `BuildRenderTree` . Bir bileşen oluşturduğunda, `BuildRenderTree` oluşturulan bileşenin öğelerini, metnini ve alt bileşenlerini açıklayan bir veri yapısını yürütür ve oluşturur.

İşleme mantığı bir özel durum oluşturabilir. Bu senaryonun bir örneği `@someObject.PropertyName` değerlendirilir, ancak `@someObject` ise oluşur `null` . Oluşturma mantığı tarafından oluşturulan işlenmeyen bir özel durum, sunucu devresi için önemli bir durumdur Blazor .

Oluşturma mantığındaki null başvuru özel durumunu engellemek için, `null` üyelerine erişmeden önce bir nesne denetleyin. Aşağıdaki örnekte, şu ise `person.Address` Özellikler erişilmez `person.Address` `null` :

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

Önceki kod olmadığını varsayar `person` `null` . Genellikle, kodun yapısı, bileşenin işlendiği sırada bir nesnenin var olmasını garanti eder. Bu durumlarda, işleme mantığını denetlemek gerekli değildir `null` . Önceki örnekte, `person` `person` bileşen örneklendiği zaman oluşturulduğu için, mevcut olması garantisini alabilir.

### <a name="event-handlers"></a>Olay işleyicileri

İstemci tarafı kod, kullanarak olay işleyicileri oluşturulurken C# kodu etkinleştirmeleri tetikler:

* `@onclick`
* `@onchange`
* Diğer `@on...` öznitelikler
* `@bind`

Olay işleyici kodu, bu senaryolarda işlenmeyen bir özel durum oluşturabilir.

Bir olay işleyicisi işlenmeyen bir özel durum oluşturursa (örneğin, bir veritabanı sorgusu başarısız olursa), özel durum sunucu devresi için önemli olur Blazor . Uygulama, dış nedenlerle başarısız olabilecek kodu çağırırsa, hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesini kullanarak özel durumlar yakalayın.

Kullanıcı kodu yakalanmazsa ve özel durumu işlemezse çerçeve özel durumu günlüğe kaydeder ve devre sonlandırır.

### <a name="component-disposal"></a>Bileşen elden çıkarma

Örneğin, Kullanıcı başka bir sayfaya gezindiği için, bir bileşen kullanıcı arabiriminden kaldırılabilir. Uygulayan bir bileşen <xref:System.IDisposable?displayProperty=fullName> kullanıcı arabiriminden kaldırıldığında, çerçeve bileşenin <xref:System.IDisposable.Dispose%2A> yöntemini çağırır.

Bileşenin `Dispose` yöntemi işlenmeyen bir özel durum oluşturursa, özel durum sunucu devresi için önemli olur Blazor . Çıkarma mantığı özel durumlar oluşturmayabilir, uygulama hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesini kullanarak özel durumları yakalemelidir.

Bileşen elden çıkarma hakkında daha fazla bilgi için bkz <xref:blazor/lifecycle#component-disposal-with-idisposable> ..

### <a name="javascript-interop"></a>JavaScript ile birlikte çalışma

`IJSRuntime.InvokeAsync<T>`.NET kodunun Kullanıcı tarayıcısında JavaScript çalışma zamanına zaman uyumsuz çağrılar yapmasına izin verir.

Aşağıdaki koşullar ile hata işleme için geçerlidir `InvokeAsync<T>` :

* Bir çağrı `InvokeAsync<T>` zaman uyumlu başarısız olursa, .NET özel durumu oluşur. `InvokeAsync<T>`Sağlanan bağımsız değişkenler seri hale getirilemediğinden, bir çağrı başarısız olabilir. Geliştirici kodu özel durumu yakalamalı. Bir olay işleyicisindeki veya bileşen yaşam döngüsü yöntemindeki uygulama kodu bir özel durumu işlemezse, ortaya çıkan özel durum sunucu devresi için önemli olur Blazor .
* Bir çağrı `InvokeAsync<T>` zaman uyumsuz olarak başarısız olursa, .net <xref:System.Threading.Tasks.Task> başarısız olur. `InvokeAsync<T>`Örneğin, JavaScript tarafı kodu bir özel durum oluşturduğundan veya olarak tamamlanan bir döndürürse, ' a çağrı başarısız olabilir `Promise` `rejected` . Geliştirici kodu özel durumu yakalamalı. [Await](/dotnet/csharp/language-reference/keywords/await) işleci kullanılıyorsa, yöntem çağrısını hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesinde sarmalamalı olarak düşünün. Aksi takdirde, hata veren kod, bir sunucu devresi için önemli olan işlenmemiş bir özel durumla sonuçlanır Blazor .
* Varsayılan olarak, çağrısı `InvokeAsync<T>` belirli bir süre içinde tamamlanmalıdır veya çağrı zaman aşımına uğrar. Varsayılan zaman aşımı süresi bir dakikadır. Zaman aşımı, kodu ağ bağlantısında veya hiçbir zaman bir tamamlanma iletisi göndermeme JavaScript kodundaki bir kaybına karşı korur. Çağrı zaman aşımına uğrarsa sonuç `Task` bir ile başarısız olur <xref:System.OperationCanceledException> . Günlüğe kaydetme ile özel durumu yakalar ve işleyin.

Benzer şekilde, JavaScript kodu özniteliği tarafından gösterilen .NET yöntemlerine çağrıları başlatabilir [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) . Bu .NET yöntemleri işlenmeyen bir özel durum oluşturur:

* Özel durum, bir sunucu devresi için önemli olarak değerlendirilmez Blazor .
* JavaScript tarafı `Promise` reddedilir.

.NET tarafında ya da yöntem çağrısının JavaScript tarafında hata işleme kodu kullanma seçeneğiniz vardır.

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a>BlazorSunucu prerendering

Blazorbileşenler, işlenen HTML işaretlemesi kullanıcının ilk HTTP isteğinin bir parçası olarak döndürüldüğünden, [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) kullanılarak önceden uygulanabilir. Bu şu şekilde geçerlidir:

* Aynı sayfanın parçası olan tüm ön işlenmiş bileşenler için yeni bir devre oluşturma.
* İlk HTML oluşturuluyor.
* `disconnected`Kullanıcı tarayıcısının aynı sunucuya geri bir bağlantı kuruncaya kadar devreyi kabul etmek SignalR . Bağlantı oluşturulduğunda, devre üzerindeki etkileşim sürdürülür ve bileşenlerin HTML işaretlemesi güncelleştirilir.

Herhangi bir bileşen prerendering sırasında, örneğin bir yaşam döngüsü yöntemi veya işleme mantığı sırasında işlenmeyen bir özel durum oluşturursa:

* Bu, devre için önemli bir durumdur.
* Özel durum, etiket yardımcısından çağrı yığınını ortaya atılır `Component` . Bu nedenle, özel durum geliştirici kodu tarafından açıkça yakalanmadığı takdirde tüm HTTP isteği başarısız olur.

Normal koşullarda, prerendering başarısız olduğunda bileşeni oluşturma ve işleme devam etmek, çalışan bir bileşen işlenemediği için mantıklı değildir.

Prerendering sırasında oluşabilecek hatalara tolerans sağlamak için hata işleme mantığı özel durum oluşturabilecek bir bileşenin içine yerleştirilmelidir. [Try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimlerini hata işleme ve günlüğe kaydetme ile kullanın. `Component`Etiket yardımcısını bir deyime sarmalama yerine `try-catch` , etiket Yardımcısı tarafından işlenen bileşene hata işleme mantığını koyun `Component` .

## <a name="advanced-scenarios"></a>Gelişmiş senaryolar

### <a name="recursive-rendering"></a>Özyinelemeli işleme

Bileşenler yinelemeli olarak iç içe olabilir. Bu, özyinelemeli veri yapılarını temsil etmek için kullanışlıdır. Örneğin, bir `TreeNode` bileşen `TreeNode` düğüm alt öğelerinin her biri için daha fazla bileşen işleyebilir.

Yinelemeli olarak işlenirken, sonsuz özyineleme sonucu veren kodlama desenlerinden kaçının:

* Bir döngüyü içeren bir veri yapısını yinelemeli olarak işlemez. Örneğin, alt öğeleri içeren bir ağaç düğümünü işlemez.
* Bir döngüyü içeren bir düzen zinciri oluşturmayın. Örneğin, düzeni kendisi olan bir düzen oluşturmayın.
* Son kullanıcının, kötü amaçlı veri girişi veya JavaScript birlikte çalışabilirlik çağrıları aracılığıyla özyineleme/varyantları (kurallar) ihlal etmemenizi izin verme.

Oluşturma sırasında sonsuz döngüler:

* İşleme işleminin süresiz olarak devam etmesine neden olur.
* Sonlandırılmamış bir döngü oluşturmaya eşdeğerdir.

Bu senaryolarda, etkilenen Blazor sunucu devresi başarısız olur ve iş parçacığı genellikle şunları yapmayı dener:

* Süresiz olarak işletim sisteminin izin verdiği CPU süresini çok fazla kullanın.
* Sınırsız miktarda sunucu belleği tükettin. Sınırsız bellek tüketme, Sonlandırılmamış bir döngünün her yinelemede bir koleksiyona giriş eklediği senaryoya eşdeğerdir.

Sonsuz özyineleme desenlerinin önüne geçmek için, özyinelemeli işleme kodunun uygun durdurma koşullarını içerdiğinden emin olun.

### <a name="custom-render-tree-logic"></a>Özel işleme ağacı mantığı

Çoğu Blazor bileşen *. Razor* dosyaları olarak uygulanır ve çıktısını işlemek için bir üzerinde çalışan Logic üretmek için derlenir `RenderTreeBuilder` . Bir geliştirici, `RenderTreeBuilder` yordamsal C# kodu kullanarak Logic el ile uygulayabilir. Daha fazla bilgi için bkz. <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> El ile işleme ağacı Oluşturucu mantığının kullanımı, genel bileşen geliştirme için önerilmeyen gelişmiş ve güvenli olmayan bir senaryo olarak değerlendirilir.

`RenderTreeBuilder`Kod yazılmışsa, geliştirici kodun doğruluğunu garanti etmelidir. Örneğin, geliştirici şunları sağlamalıdır:

* Ve çağrıları `OpenElement` `CloseElement` doğru şekilde dağıtılır.
* Öznitelikler yalnızca doğru yerlere eklenir.

Hatalı elle işleme ağacı Oluşturucu mantığı kilitlenmeler, sunucu askıda kalma ve güvenlik açıkları dahil rastgele tanımsız davranışa neden olabilir.

El ile işleme ağacı Oluşturucu mantığını aynı karmaşıklık düzeyinde ve aynı *tehlike* düzeyiyle, el ile derleme kodu veya MSIL yönergeleri yazarak değerlendirin.
