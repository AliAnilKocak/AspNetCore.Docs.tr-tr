---
title: ASP.NET Core Blazor durum yönetimi
author: guardrex
description: Blazor sunucu tarafı uygulamalarında durumu kalıcı hale getirme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/state-management
ms.openlocfilehash: 01f32130e43b7235cb438ad71321256882f53573
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310297"
---
# <a name="aspnet-core-blazor-state-management"></a>ASP.NET Core Blazor durum yönetimi

[Steve Sanderson](https://github.com/SteveSandersonMS) tarafından

Blazor sunucu tarafı, durum bilgisi olan bir uygulama çerçevesidir. Çoğu zaman, uygulama sunucuya devam eden bir bağlantı sağlar. Kullanıcının durumu, sunucu belleğinde bir *devrende*tutulur. 

Bir kullanıcının devresi için durum tutulan örnekler şunlardır:

* İşlenmiş Kullanıcı arabirimi&mdash;bileşen örneklerinin hiyerarşisi ve en son işleme çıktısı.
* Bileşen örneklerinde alanların ve özelliklerin değerleri.
* Devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veriler.

> [!NOTE]
> Bu makale, Blazor sunucu tarafı uygulamalarında durum kalıcılığını ele alınmaktadır. Blazor istemci tarafı uygulamalar [, tarayıcıda istemci tarafı durum kalıcılığından](#client-side-in-the-browser) yararlanabilir, ancak bu makalenin kapsamı dışında özel çözümler veya üçüncü taraf paketleri gerektirebilir.

## <a name="blazor-circuits"></a>Blazor devreleri

Bir Kullanıcı geçici bir ağ bağlantısı kaybıyla karşılaşıyorsa, Blazor uygulamayı kullanmaya devam edebilmek için kullanıcıyı özgün devresine yeniden bağlamaya çalışır. Ancak, bir kullanıcıyı sunucunun belleğindeki özgün devresine yeniden bağlamak her zaman mümkün değildir:

* Sunucu, bağlantısı kesilen bir devreni süresiz olarak sürdüremez. Sunucu, bir zaman aşımından sonra veya sunucu bellek baskısı altında olduğunda, bağlantısı kesilen bir bağlantı hattını serbest bırakmalıdır.
* Çoklu sunucu, yük dengeli dağıtım ortamlarında, herhangi bir zamanda herhangi bir sunucu işleme isteği kullanılamaz hale gelebilir. Tek tek sunucular, tüm istek hacmini işlemek için artık gerekli olmadığında veya otomatik olarak kaldırılabilir. Kullanıcı yeniden bağlanmaya çalıştığında, özgün sunucu kullanılamayabilir.
* Kullanıcı tarayıcıyı kapatabilir ve yeniden açabilir veya sayfayı yeniden yükleyerek tarayıcı belleğinde tutulan tüm durumları kaldırır. Örneğin, JavaScript birlikte çalışabilirlik çağrıları aracılığıyla ayarlanan değerler kaybolur.

Kullanıcı özgün devresine yeniden bağlanalınmayacaksa, Kullanıcı boş duruma sahip yeni bir devre alır. Bu, bir masaüstü uygulamasını kapatıp yeniden açmaya eşdeğerdir.

## <a name="preserve-state-across-circuits"></a>Devreler arasında durumu koru

Bazı senaryolarda, devre genelinde durum koruma istenebilir. Bir uygulama, şu durumlarda bir kullanıcı için önemli verileri koruyabilir:

* Web sunucusu kullanılamaz hale gelir.
* Kullanıcının tarayıcısı yeni bir Web sunucusuyla yeni bir devre başlatmaya zorlanır.

Genel olarak, devrelerde durumu korumak, kullanıcıların zaten var olan verileri okurken değil, etkin bir şekilde veri oluşturmakta olduğu senaryolar için geçerlidir.

Tek bir devrenin ötesinde durumu korumak için, *verileri yalnızca sunucunun belleğine depolamayın*. Uygulama, verileri başka bir depolama konumuna kalıcı hale vermelidir. Durum kalıcılığı otomatik&mdash;değil durum bilgisi olan veri kalıcılığını uygulamak üzere uygulamayı geliştirirken adımları uygulamanız gerekir.

Veri kalıcılığı genellikle yalnızca kullanıcıların oluşturma çabasında olduğu yüksek değerli durum için gereklidir. Aşağıdaki örneklerde, kalıcı durum ticari etkinliklerdeki zaman veya yardımlarını kaydeder:

* Çok adımlı WebForm &ndash; , bir kullanıcının, durumları kaybedilmişse çok adımlı bir işlemin birkaç tamamlanmış adımı için verileri yeniden girmesi için zaman alan bir işlemdir. Kullanıcı, çok adımlı formdan uzaklaştıklarında ve daha sonra forma geri döndüğünüzde bu senaryodaki durumu kaybeder.
* Alışveriş sepeti &ndash; olası geliri temsil eden bir uygulamanın ticari olarak önemli bir bileşeni olabilir. Durumlarını kaybettikleri bir Kullanıcı ve bu nedenle alışveriş sepeti, siteye daha sonra geri döntiklerinde daha az ürün veya hizmet satın alabilir.

Genellikle, gönderilmemiş bir oturum açma iletişim kutusuna girilen Kullanıcı adı gibi, kolayca yeniden oluşturulmuş durumu korumak gerekli değildir.

> [!IMPORTANT]
> Uygulama, *uygulama durumunu*yalnızca kalıcı hale getirebilirler. Usıs, bileşen örnekleri ve bunların işleme ağaçları gibi kalıcı hale getirilir. Bileşenler ve işleme ağaçları genellikle seri hale getirilebilir değildir. Bir TreeView 'un genişletilmiş düğümleri gibi kullanıcı arabirimi durumuna benzer bir şeyi sürdürmek için, uygulamanın davranışı seri hale getirilebilir uygulama durumu olarak modelleyeceği özel kodu olmalıdır.

## <a name="where-to-persist-state"></a>Durumun nerede kalıcı olduğu

Blazor sunucu tarafı uygulamasındaki kalıcı durum için üç ortak konum vardır. Her yaklaşım farklı senaryolara en iyi şekilde uygundur ve farklı uyarılar içerir:

* [Veritabanında sunucu tarafı](#server-side-in-a-database)
* [URL](#url)
* [Tarayıcıda istemci tarafı](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Veritabanında sunucu tarafı

Kalıcı veri kalıcılığı veya birden çok kullanıcı veya cihaza yayılması gereken veriler için, bağımsız bir sunucu tarafı veritabanı neredeyse en iyi seçenektir. Şu seçenekler mevcuttur:

* İlişkisel SQL veritabanı
* Anahtar-değer deposu
* Blob deposu
* Tablo deposu

Veriler veritabanına kaydedildikten sonra, bir kullanıcı tarafından herhangi bir zamanda yeni bir devre başlatılabilir. Kullanıcının verileri korunur ve yeni bir devrede kullanılabilir.

Azure veri depolama seçenekleri hakkında daha fazla bilgi için bkz. [Azure depolama belgeleri](/azure/storage/) ve [Azure veritabanları](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>URL

Gezinti durumunu temsil eden geçici veriler için, verileri URL 'nin bir parçası olarak modelleyin. URL 'de modellenen durum örnekleri şunları içerir:

* Görüntülenen varlığın KIMLIĞI.
* Disk belleğine alınmış bir kılavuzdaki geçerli sayfa numarası.

Tarayıcının adres çubuğunun içeriği korunur:

* Kullanıcı sayfayı el ile yeniden yükler.
* Web sunucusu kullanılamaz&mdash;hale gelirse, Kullanıcı farklı bir sunucuya bağlanmak için sayfayı yeniden yüklemeye zorlanır.

`@page` Yönergeyle URL desenleri tanımlama hakkında bilgi için bkz <xref:blazor/routing>.

### <a name="client-side-in-the-browser"></a>Tarayıcıda istemci tarafı

Kullanıcının etkin şekilde oluşturmakta olduğu geçici veriler için, yaygın bir yedekleme deposu tarayıcının `localStorage` ve `sessionStorage` koleksiyonlarıdır. Devre dışı bırakılırsa, sunucu tarafı depolama alanının avantajlarından yararlanan uygulama, saklı durumu yönetmek veya temizlemek için gerekli değildir.

> [!NOTE]
> Bu bölümdeki "istemci tarafı", [Blazor istemci tarafı barındırma modelinde](xref:blazor/hosting-models#client-side)değil, tarayıcıdaki istemci tarafı senaryolarına başvurur. `localStorage`ve `sessionStorage` yalnızca özel kod yazarak ya da 3. taraf paketini kullanarak Blazor istemci tarafı uygulamalarda kullanılabilir.

`localStorage`ve `sessionStorage` aşağıdaki gibi farklılık gösterir:

* `localStorage`, kullanıcının tarayıcısına kapsamlandırılır. Kullanıcı sayfayı yeniden yüklediğinde veya tarayıcıyı kapatıp yeniden açarsa durum devam ettirir. Kullanıcı birden çok tarayıcı sekmesi açarsa, durum sekmeler arasında paylaşılır. Veriler açık olarak `localStorage` temizlenene kadar içinde devam ediyor.
* `sessionStorage`kullanıcının tarayıcı sekmesi kapsamıdır. Kullanıcı sekmeyi yeniden yüklediğinde durum devam ettirir. Kullanıcı sekmeyi veya tarayıcıyı kapatırsa durum kaybedilir. Kullanıcı birden çok tarayıcı sekmesi açarsa, her sekmenin kendi bağımsız bir veri sürümü vardır.

Genellikle, `sessionStorage` kullanmak daha güvenlidir. `sessionStorage`bir kullanıcının birden çok sekme açmasını ve aşağıdaki gibi karşılaştığı riskleri önler:

* Sekmelerde durum depolamadaki hatalar.
* Sekme diğer sekmelerin durumunun üzerine yazdığınızda kafa karıştırıcı davranışı.

`localStorage`uygulamanın kapatma ve tarayıcıyı yeniden açma genelinde durumu kalıcı hale getirilmesi gerekiyorsa, daha iyi bir seçenektir.

Tarayıcı depolamayı kullanmaya yönelik uyarılar:

* Sunucu tarafı veritabanının kullanımına benzer şekilde veri yükleme ve kaydetme zaman uyumsuzdur.
* Sunucu tarafı veritabanının aksine, istenen sayfa prerendering aşamasında tarayıcıda bulunmadığından, depolama alanı prerendering sırasında kullanılamaz.
* Birkaç kilobayt veri depolaması, Blazor sunucu tarafı uygulamalar için kalıcı hale getiriyoruz. Birkaç kilobayt dışında, veriler ağ üzerinden yüklenip kaydedildiğinden performans etkilerini göz önünde bulundurmanız gerekir.
* Kullanıcılar verileri görüntüleyebilir veya bunlarla karşılaşabilir. ASP.NET Core [veri koruma](xref:security/data-protection/introduction) riski azaltabilirler.

## <a name="third-party-browser-storage-solutions"></a>Üçüncü taraf tarayıcı depolama çözümleri

Üçüncü taraf NuGet paketleri ve `localStorage` `sessionStorage`ile çalışmaya yönelik API 'ler sağlar.

ASP.NET Core [veri korumasını](xref:security/data-protection/introduction)saydam olarak kullanan bir paket seçmeyi düşünülüyor. ASP.NET Core veri koruma, depolanan verileri şifreler ve depolanan verilerle yapılan değişikliklere karşı olası riskleri azaltır. JSON seri hale getirilmiş veriler düz metin halinde depolanıyorsa, kullanıcılar tarayıcı geliştirici araçlarını kullanarak verileri görebilir ve depolanan verileri de değiştirebilir. Verilerin güvenliğini sağlamak her zaman bir sorun değildir çünkü veriler önemsiz olarak olabilir. Örneğin, bir kullanıcı ARABIRIMI öğesinin saklı rengini okumak veya değiştirmek, Kullanıcı veya kuruluş için önemli bir güvenlik riski değildir. Kullanıcıların *hassas verileri*incelemesine veya değiştirmesine izin vermeyi önleyin.

## <a name="protected-browser-storage-experimental-package"></a>Korumalı tarayıcı depolaması deneysel paket

[Microsoft. aspnetcore. protectedbrowserstorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)ve `sessionStorage` için `localStorage` [veri koruması](xref:security/data-protection/introduction) sağlayan bir NuGet paketi örneği.

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`, şu anda üretim kullanımı için uygun olmayan, desteklenmeyen bir deneysel paket.

### <a name="installation"></a>Yükleme

`Microsoft.AspNetCore.ProtectedBrowserStorage` Paketi yüklemek için:

1. Blazor sunucu tarafı uygulama projesinde, [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)öğesine bir paket başvurusu ekleyin.
1. Üst düzey HTML 'de (örneğin, varsayılan Proje şablonundaki *Pages/_host. cshtml* dosyasında) aşağıdaki `<script>` etiketi ekleyin:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. Yönteminde, hizmet koleksiyonuna Ekle `AddProtectedBrowserStorage` `localStorage` ve `sessionStorage` hizmetler ' i çağırın: `Startup.ConfigureServices`

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Bir bileşen içindeki verileri kaydetme ve yükleme

Tarayıcı depolamaya veri yüklemeyi veya kaydetmeyi gerektiren herhangi bir bileşende, aşağıdakilerden birinin bir [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) örneğini eklemek için kullanın:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Seçim, hangi yedekleme deposunu kullanmak istediğinize bağlıdır. Aşağıdaki örnekte, `sessionStorage` kullanılır:

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

İfade, bileşen yerine bir *_ımports. Razor* dosyasına yerleştirilebilir. `@using` *_Imports. Razor* dosyası kullanımı, ad alanını uygulamanın daha büyük kesimlerine veya uygulamanın tamamına sağlar.

Proje şablonunun `Counter` bileşenindeki `currentCount` değeri kalıcı hale getirmek için, kullanmak `ProtectedSessionStore.SetAsync`üzere yöntemideğiştirin:`IncrementCount`

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Daha büyük, daha gerçekçi uygulamalar, tek tek alanların depolanması ise olası bir senaryodur. Uygulamalar karmaşık durum içeren tüm model nesnelerini depolamaya daha olasıdır. `ProtectedSessionStore`JSON verilerini otomatik olarak serileştirir ve seri hale getirir.

Yukarıdaki kod örneğinde, `currentCount` veriler kullanıcının tarayıcısında olarak `sessionStorage['count']` depolanır. Veriler düz metin biçiminde depolanmaz, bunun yerine ASP.NET Core [veri koruma](xref:security/data-protection/introduction)kullanılarak korunur. Şifrelenmiş veriler, tarayıcının geliştirici konsolunda değerlendirildiğinde `sessionStorage['count']` görülebilir.

Kullanıcı daha sonra `currentCount` `Counter` bileşene geri dönerse verileri kurtarmak için (tamamen yeni bir devrede olanlar dahil), şunu kullanın `ProtectedSessionStore.GetAsync`:

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Bileşenin parametreleri gezinti durumu içeriyorsa, sonucunu çağırın `ProtectedSessionStore.GetAsync` ve ' `OnInitializedAsync`de `OnParametersSetAsync`atayın. `OnInitializedAsync`Yalnızca bileşenin ilk örneği oluşturulduğunda bir kez çağırılır. `OnInitializedAsync`Kullanıcı aynı sayfada kaldığında farklı bir URL 'ye gittiğinde daha sonra yeniden çağrılmaz.

> [!WARNING]
> Bu bölümdeki örnekler yalnızca sunucuda prerendering etkinleştirilmemişse çalışır. Prerendering etkinken şuna benzer bir hata oluşturulur:
>
> > JavaScript birlikte çalışabilirlik çağrıları şu an için verilemez. Bunun nedeni, bileşenin ön işlenmiş olmasından kaynaklanır.
>
> Prerendering 'ı devre dışı bırakın ya da prerendering ile çalışmak için ek kod ekleyin. Prerendering ile birlikte çalışarak kodu yazma hakkında daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.

### <a name="handle-the-loading-state"></a>Yükleme durumunu işle

Tarayıcı depolaması zaman uyumsuz olduğundan (bir ağ bağlantısı üzerinden erişilir), veriler yüklenmeden ve bir bileşen tarafından kullanıma sunulmadan önce her zaman bir zaman dilimi vardır. En iyi sonuçlar için, yükleme sırasında, boş veya varsayılan verileri görüntülemek yerine bir yükleme durumu iletisi işleme devam ediyor.

Bir yaklaşım, verilerin `null` (hala yükleme) olup olmadığını izlemedir. Varsayılan `Counter` bileşende, sayı bir `int`içinde tutulur. Türe `currentCount` `?`()`int`bir soru işareti () ekleyerek null yapılabilir yapın:

```csharp
private int? currentCount;
```

Sayı ve **artış** düğmesini koşullu olarak görüntülemediğinizden, bu öğeleri yalnızca veriler yüklenmişse görüntülemeyi seçin:

```cshtml
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Tanıtıcı prerendering

Prerendering sırasında:

* Kullanıcının tarayıcısına etkileşimli bir bağlantı yok.
* Tarayıcıda, JavaScript kodunu çalıştırabildiği bir sayfa yok.

`localStorage`veya `sessionStorage` prerendering sırasında kullanılamaz. Bileşen depolama ile etkileşim kurmayı denerse şuna benzer bir hata oluşturulur:

> JavaScript birlikte çalışabilirlik çağrıları şu an için verilemez. Bunun nedeni, bileşenin ön işlenmiş olmasından kaynaklanır.

Hatayı çözmek için bir yol prerendering devre dışı bırakılır. Bu genellikle uygulama tarayıcı tabanlı depolamanın yoğun bir şekilde kullanımını yapıyorsa en iyi seçenektir. Prerendering karmaşıklık ekler ve uygulama, kullanılabilir olana kadar `localStorage` `sessionStorage` faydalı içeriğe gidemediği için uygulamaya yarar.

Prerendering 'yi devre dışı bırakmak için, *Pages/_Host. cshtml* dosyasını açın ve çağrısını `Html.RenderComponentAsync<App>(RenderMode.Server)`değiştirin.

Prerendering, veya `localStorage` `sessionStorage`kullanmayan diğer sayfalar için yararlı olabilir. Prerendering etkin tutmak için, tarayıcı devreye bağlanana kadar yükleme işlemini erteleyin. Aşağıda, bir sayaç değeri depolamak için bir örnek verilmiştir:

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore
@inject IComponentContext ComponentContext

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isWaitingForConnection;

    protected override async Task OnInitializedAsync()
    {
        if (ComponentContext.IsConnected)
        {
            // It looks like the app isn't prerendering, so the data can be
            // immediately loaded from browser storage.
            await LoadStateAsync();
        }
        else
        {
            // Prerendering is in progress, so the app defers the load operation
            // until later.
            isWaitingForConnection = true;
        }
    }

    protected override async Task OnAfterRenderAsync()
    {
        // By this stage, the client has connected back to the server, and
        // browser services are available. If the app didn't load the data earlier,
        // the app should do so now and then trigger a new render.
        if (isWaitingForConnection)
        {
            isWaitingForConnection = false;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Durum korumasını ortak bir konuma ayırın

Birçok bileşen tarayıcı tabanlı depolamaya güveniyorsa, durum sağlayıcısı kodu birçok kez yeniden uygulama kod yinelemesi oluşturur. Kod çoğaltmaktan kaçınmanın bir seçeneği, durum sağlayıcısı mantığını kapsülleyen bir *durum sağlayıcısı ana bileşeni* oluşturmaktır. Alt bileşenler, durum kalıcılığı mekanizmasına bakılmaksızın kalıcı verilerle çalışabilir.

Aşağıdaki bir `CounterStateProvider` bileşen örneğinde, sayaç verileri kalıcıdır:

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider` Bileşen, yükleme tamamlanana kadar alt içeriğini işlemeden Yükleme aşamasını işler.

`CounterStateProvider` Bileşeni kullanmak için, bileşenin bir örneğini sayaç durumuna erişimi gerektiren diğer tüm bileşenler etrafında sarmalayın. Durumu bir uygulamadaki tüm bileşenler için erişilebilir hale getirmek üzere bileşeni `CounterStateProvider` `App` bileşende (*app. Razor*) `Router` içine sarmalayın:

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Sarmalanan bileşenler, kalıcı sayaç durumunu alır ve değiştirebilir. Aşağıdaki bileşen `Counter` , bu kalıbı uygular:

```cshtml
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Yukarıdaki bileşen, ile `ProtectedBrowserStorage`etkileşimde bulunmak veya bir "yükleme" aşaması ile uğraşmak için gerekli değildir.

Daha önce açıklandığı gibi prerendering ile başa çıkmak `CounterStateProvider` için, sayaç verilerini kullanan tüm bileşenlerin prerendering ile otomatik olarak çalışmasını sağlayacak şekilde değiştirilebilir. Ayrıntılar için bkz. [Handle prerendering](#handle-prerendering) bölümü.

Genel olarak, *durum sağlayıcısı üst bileşen* deseninin kullanılması önerilir:

* Diğer birçok bileşenin durumunu kullanmak için.
* Kalıcı olacak yalnızca bir üst düzey durum nesnesi varsa.

Birçok farklı durum nesnesini kalıcı hale getirmek ve farklı yerlerde nesnelerin farklı alt kümelerini kullanmak için, durumu küresel olarak yükleme ve kaydetme işlemlerini yapmaktan kaçınmak daha iyidir.
