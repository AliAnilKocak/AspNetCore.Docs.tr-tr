---
title: ASP.NET Core Blazor barındırma modeli yapılandırması
author: guardrex
description: Razor bileşenlerini Razor Pages ve MVC uygulamalarına tümleştirme dahil olmak üzere Blazor barındırma modeli yapılandırması hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 1f71ac63bbe9dc9d56cfca2ded19a5b863be828f
ms.sourcegitcommit: 6ffb583991d6689326605a24565130083a28ef85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80306427"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor barındırma modeli yapılandırması

[Daniel Roth](https://github.com/danroth27) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Bu makalede barındırma modeli yapılandırması ele alınmaktadır.

## <a name="blazor-webassembly"></a>Blazor WebAssembly

ASP.NET Core 3,2 Preview 3 sürümünden itibaren, Blazor WebAssembly yapılandırmayı şunları destekler:

* *Wwwroot/appSettings. JSON*
* *Wwwroot/appSettings. {ENVIRONMENT}. JSON*

Blazor barındırılan bir uygulamada, [çalışma zamanı ortamı](xref:fundamentals/environments) , sunucu uygulamasının değeri ile aynıdır.

Uygulamayı yerel olarak çalıştırırken, ortam varsayılan olarak geliştirme aşamasındadır. Uygulama yayımlandığında, ortam varsayılan olarak üretim olur. Ortamın nasıl yapılandırılacağı dahil olmak üzere daha fazla bilgi için bkz. <xref:fundamentals/environments>.

> [!WARNING]
> Blazor WebAssembly uygulamasındaki yapılandırma kullanıcılar tarafından görülebilir. **Yapılandırma bölümünde uygulama gizli dizilerini veya kimlik bilgilerini depolamamayın.**

Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır. [Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz. Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:

* Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.
* PWA 'nın *Service-Worker. js* ve *Service-Worker-assets. js* dosyalarının derlemede yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi sitesinde uygulamaya işaret eden uygulamanın yeniden dağıtıldığını belirten, derleme üzerinde yeniden oluşturulmalıdır.

Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz. <xref:blazor/progressive-web-app#background-updates>.

## <a name="blazor-server"></a>Blazor Server

### <a name="reflect-the-connection-state-in-the-ui"></a>Kullanıcı arabirimindeki bağlantı durumunu yansıtır

İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir. Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.

Kullanıcı arabirimini özelleştirmek için *_Host. cshtml* Razor sayfasının `<body>` `components-reconnect-modal` `id` bir öğe tanımlayın:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Aşağıdaki tabloda `components-reconnect-modal` öğesine uygulanan CSS sınıfları açıklanmaktadır.

| CSS sınıfı                       | &hellip; gösterir |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Kayıp bir bağlantı. İstemci yeniden bağlanmaya çalışıyor. Kalıcı olarak göster. |
| `components-reconnect-hide`     | Etkin bir bağlantı sunucuya yeniden oluşturulur. Kalıcı olarak gizleyin. |
| `components-reconnect-failed`   | Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu. Yeniden bağlanmayı denemek için `window.Blazor.reconnect()`çağırın. |
| `components-reconnect-rejected` | Yeniden bağlantı reddedildi. Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu. Uygulamayı yeniden yüklemek için `location.reload()`çağırın. Bu bağlantı durumu şu durumlarda oluşabilir:<ul><li>Sunucu tarafında devre dışı bir kilitlenme oluşur.</li><li>Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil. Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</li><li>Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</li></ul> |

### <a name="render-mode"></a>Oluşturma modu

Blazor sunucu uygulamaları, sunucu bağlantısı oluşturulmadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar. Bu, *_Host. cshtml* Razor sayfasında ayarlanır:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`, bileşenin şunları yapıp kullanmadığını yapılandırır:

* , Sayfaya ön gönderilir.
* , Sayfada statik HTML olarak veya Kullanıcı aracısından bir Blazor uygulamasını önyüklemek için gerekli bilgileri içeriyorsa.

| `RenderMode`        | Açıklama |
| ------------------- | ----------- |
| `ServerPrerendered` | Bileşeni statik HTML olarak işler ve Blazor sunucusu uygulaması için bir işaret içerir. Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamasının önyüklemesi için kullanılır. |
| `Server`            | Blazor sunucusu uygulaması için bir işaret oluşturur. Bileşen çıkışı dahil değildir. Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamasının önyüklemesi için kullanılır. |
| `Static`            | Bileşeni statik HTML olarak işler. |

Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Razor sayfaları ve görünümlerinden durum bilgisi olan etkileşimli bileşenleri işleme

Durum bilgisi olan etkileşimli bileşenler Razor sayfasına veya görünümüne eklenebilir.

Sayfa veya görünüm şunları işler:

* Bileşen sayfa veya görünümle birlikte kullanılır.
* Prerendering için kullanılan ilk bileşen durumu kayboldu.
* SignalR bağlantısı oluşturulduğunda yeni bileşen durumu oluşturulur.

Aşağıdaki Razor sayfası bir `Counter` bileşeni işler:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Razor sayfaları ve görünümlerinden etkileşimsiz bileşenleri işleme

Aşağıdaki Razor sayfasında, `Counter` bileşen bir form kullanılarak belirtilen bir başlangıç değeri ile statik olarak işlenir:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

`MyComponent` statik olarak işlendiğinde, bileşen etkileşimli olamaz.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Blazor Server uygulamaları için SignalR istemcisini yapılandırma

Bazen Blazor Server uygulamaları tarafından kullanılan SignalR istemcisini yapılandırmanız gerekir. Örneğin, bir bağlantı sorununu tanılamak için SignalR istemcisinde günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.

*Pages/_Host. cshtml* dosyasında SignalR istemcisini yapılandırmak için:

* `blazor.server.js` betiği için `<script>` etiketine bir `autostart="false"` özniteliği ekleyin.
* `Blazor.start` çağırın ve SignalR oluşturucuyu belirten bir yapılandırma nesnesini geçirin.

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```
