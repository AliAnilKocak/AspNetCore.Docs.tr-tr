---
title: Blazor barındırma modelleri
author: guardrex
description: İstemci tarafı ve sunucu tarafı Blazor modelleri barındırma anlayın.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/05/2019
uid: blazor/hosting-models
ms.openlocfilehash: 27a0387990d4a268cde854583c76ec03cd50a026
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750139"
---
# <a name="blazor-hosting-models"></a>Blazor barındırma modelleri

Tarafından [Daniel Roth](https://github.com/danroth27)

Blazor istemci-tarafı çalışmak üzere tasarlanmış bir web çerçevesi olan tarayıcıda bir [WebAssembly](http://webassembly.org/)-.NET çalışma zamanı tabanlı (*Blazor istemci-tarafı*) veya sunucu tarafı ASP.NET Core (*Blazor sunucu tarafı* ). Barındırma modeli, uygulama ve bileşen modelleri bakılmaksızın *aynı*.

Bu makalede açıklanan barındırma modellerine yönelik bir proje oluşturmak için bkz: <xref:blazor/get-started>.

## <a name="client-side"></a>İstemci tarafı

Asıl barındırma için Blazor WebAssembly tarayıcıda çalışan istemci-tarafı modelidir. Tarayıcıya .NET çalışma zamanı Blazor uygulamayı ve bağımlılıkları indirilir. Uygulamayı doğrudan tarayıcıda kullanıcı Arabirimi iş parçacığında yürütülür. Kullanıcı Arabirimi güncelleştirmeleri ve olay işleme, aynı işlem içinde oluşur. Statik dosya olarak bir web sunucusu veya hizmeti statik içeriği istemcilere hizmet uygulamasının varlıklarını dağıtılır.

![Blazor istemci-tarafı: Tarayıcı içinde bir kullanıcı Arabirimi iş parçacığında Blazor uygulama çalışır.](hosting-models/_static/client-side.png)

İstemci tarafı barındırma modeli kullanarak bir Blazor uygulaması oluşturmak için aşağıdaki şablonlardan birini kullanın:

* **(İstemci-tarafı) Blazor** ([dotnet yeni blazor](/dotnet/core/tools/dotnet-new)) &ndash; statik dosyalar bir dizi dağıtılabilir.
* **Blazor (ASP.NET Core barındırılan)** ([dotnet yeni blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; bir ASP.NET Core sunucusu tarafından barındırılan. ASP.NET Core uygulaması Blazor uygulama istemcilere hizmet. Web API çağrıları kullanarak ağ üzerinden istemci-tarafı Blazor uygulama sunucusu ile etkileşim kurabilir veya [SignalR](xref:signalr/introduction).

Şablonları içerir *blazor.webassembly.js* işleyen betik:

* .NET çalışma zamanı, uygulama ve uygulamanın bağımlılıklarını karşıdan yükleniyor.
* Uygulamayı çalıştırmak için çalışma zamanı başlatma.

İstemci tarafı barındırma modeli, çeşitli avantajlar sunar. İstemci tarafı Blazor:

* .NET sunucu tarafı bağımlılığı yoktur.
* İstemci kaynakları ve özellikleri tam olarak yararlanır.
* Yük boşaltma, sunucudan istemciye çalışır.
* Çevrimdışı senaryolarını destekler.

İstemci tarafı barındırma downsides vardır. İstemci tarafı Blazor:

* Uygulama, tarayıcının yeteneklerini kısıtlar.
* Özellikli istemci donanım ve yazılım (örneğin, WebAssembly desteği) gerektirir.
* İndirme boyutu daha büyük ve uzun uygulama yükleme süresi vardır.
* .NET çalışma zamanı ve araç desteği yetişkin daha az olan (örneğin, kısıtlamaları [.NET Standard](/dotnet/standard/net-standard) desteği ve hata ayıklama).

## <a name="server-side"></a>Sunucu tarafı

Sunucu tarafı barındırma modeli ile uygulama sunucusunda bir ASP.NET Core uygulaması içinde yürütülür. Kullanıcı Arabirimi güncelleştirmeleri, olay işleme ve JavaScript çağrılarını üzerinden işlenir bir [SignalR](xref:signalr/introduction) bağlantı.

![Tarayıcı uygulaması (bir ASP.NET Core uygulaması içinde barındırılan) ile sunucu üzerinde bir SignalR bağlantısı üzerinden etkileşim kurar.](hosting-models/_static/server-side.png)

Sunucu tarafı barındırma modeli kullanarak bir Blazor uygulaması oluşturmak için ASP.NET Core kullanan **Blazor (sunucu tarafı)** şablonu ([dotnet yeni blazorserverside](/dotnet/core/tools/dotnet-new)). ASP.NET Core uygulaması, sunucu tarafı uygulamayı barındıran ve istemcilerin eriştikleri SignalR uç noktasını ayarlar.

ASP.NET Core uygulaması uygulamanın başvuran `Startup` sınıfı eklemek için:

* Sunucu tarafı hizmetler.
* Ardışık Düzen işleme isteği için uygulama.

*Blazor.server.js* betik&dagger; istemci bağlantı kurar. Bunu kalıcı hale getirmek ve gerektiğinde (örneğin, durumunda kayıp ağ bağlantısı) uygulama durumunu geri yüklemek için uygulamanın sorumluluğudur.

Sunucu tarafı barındırma modeli, çeşitli avantajlar sunar:

* Bir istemci-tarafı uygulaması daha önemli ölçüde daha küçük bir uygulama boyutuna sahiptir ve çok daha hızlı yükler.
* Sunucu özellikleri, herhangi bir .NET Core uyumlu API'sini kullanma dahil olmak üzere tüm avantajlarından alır.
* Araç, hata ayıklama gibi mevcut .NET beklendiği gibi çalışır. Bu nedenle .NET Core üzerinde sunucu üzerinde çalışır.
* İnce istemciler ile çalışır. Örneğin, WebAssembly ve kaynak desteklemeyen tarayıcılar ile çalışır, cihazları sınırlı.
* .NET /C# kod tabanına uygulama bileşeni kod dahil olmak üzere, istemcilere hizmet değil.

Sunucu tarafı barındırma downsides vardır:

* Daha yüksek gecikme süresi: Her bir kullanıcı etkileşimi bir ağ atlama içerir.
* Çevrimdışı desteği: İstemci bağlantı başarısız olursa, Uygulama çalışmayı durduruyor.
* Sınırlı ölçeklenebilirlik: Sunucu, birden çok istemci bağlantılarını yönetme ve istemci durumu işleme.
* Bir ASP.NET Core sunucusu uygulama hizmet vermek için gereklidir. Dağıtım bir sunucudan (örneğin, bir CDN) olmadan mümkün değildir.

&dagger;*Blazor.server.js* betik sunulan ASP.NET Core paylaşılan Framework katıştırılmış bir kaynaktan.

### <a name="reconnection-to-the-same-server"></a>Aynı sunucuya yeniden bağlanma

Sunucu tarafı uygulamalar Blazor sunucunun etkin bir SignalR bağlantısı gerektirir. Bir bağlantı kaybedilirse uygulamanın sunucuya yeniden dener. Bellekte hala istemcinin durum olduğu sürece, istemci oturum durumunu kaybetmeden sürdürür.
 
İstemci bağlantısı kesildi algıladığında, istemci yeniden bağlanmayı dener ancak bir varsayılan kullanıcı arabirimini kullanıcıya görüntülenir. Yeniden bağlanma başarısız olursa, kullanıcı yeniden denemek için seçeneği sağlanır. Kullanıcı arabirimini özelleştirmek için bir öğe ile tanımlama `components-reconnect-modal` olarak kendi `id`. İstemci bu öğe, bağlantı durumuna bağlı aşağıdaki CSS sınıflarının biri ile güncelleştirir:
 
* `components-reconnect-show` &ndash; Bağlantı kesildi ve istemci yeniden bağlanmayı deniyor göstermek için uygulamanın UI göstermesi.
* `components-reconnect-hide` &ndash; İstemci UI Gizle etkin bir bağlantı vardır.
* `components-reconnect-failed` &ndash; Yeniden bağlanma başarısız oldu. Yeniden bağlanmayı yeniden denemek için çağrı `window.Blazor.reconnect()`.

### <a name="stateful-reconnection-after-prerendering"></a>Prerendering sonra durum bilgisi olan yeniden bağlanma
 
Blazor sunucu tarafı uygulamalar varsayılan olarak sunucuya istemci bağlantı kurulmadan önce sunucuda UI prerender şekilde ayarlanmıştır. Bu, ayarlanır *_Host.cshtml* Razor sayfası:
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
İstemci uygulama prerender için kullanılan aynı duruma sunucusuna bağlanır. Uygulamanın durumu bellekte hala varsa, SignalR bağlantı kurulduktan sonra bileşen durumu rerendered değil.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Durum bilgisi olan etkileşimli bileşenleri Razor sayfaları ve görünümler oluşturma
 
Durum bilgisi olan etkileşimli bileşenleri bir Razor sayfası ya da Görünüm eklenebilir. Sayfa veya Görünüm oluşturulduğunda, bileşeni ile prerendered. Durumu bellekte hala olduğu sürece, istemci bağlantısı kurulduktan sonra uygulama için bileşen durumu daha sonra yeniden bağlanır.
 
Örneğin, bir sayaç bileşeni ile form kullanarak belirtilen bir başlangıç sayısı aşağıdaki Razor sayfası oluşturur:
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialCount" />
    <button type="submit">Set initial count</button>
</form>
 
@(await Html.RenderComponentAsync<Counter>(new { InitialCount = InitialCount }))
 
@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialCount { get; set; }
}
```

### <a name="detect-when-the-app-is-prerendering"></a>Uygulama prerendering Algıla
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a>SignalR istemcisi Blazor sunucu tarafı uygulamalar için yapılandırma
 
Bazen, Blazor sunucu tarafı uygulamalar tarafından kullanılan SignalR istemci yapılandırma gerekebilir. Örneğin, bir bağlantı sorunu tanılamak için SignalR istemci günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.
 
SignalR istemcisinde yapılandırmak zorunda *sayfaları /\_Host.cshtml* dosyası:

* Ekleme bir `autostart="false"` özniteliğini `<script>` etiketinde *blazor.server.js* betiği.
* Çağrı `Blazor.start` ve SignalR Oluşturucu belirten bir yapılandırma nesnesi içinde geçirin.
 
```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/get-started>
* <xref:signalr/introduction>
