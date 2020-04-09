---
title: Core Razor bileşenleriASP.NET Razor Pages ve MVC uygulamalarına entegre edin
author: guardrex
description: Uygulamalardaki Blazor bileşenler ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: cf6056e0985d5433bddecac8dd183ca3f4c2af5b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218940"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Core Razor bileşenleriASP.NET Razor Pages ve MVC uygulamalarına entegre edin

Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)

Razor bileşenleri Razor Pages ve MVC uygulamalarına entegre edilebilir. Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden oluşturulabilir.

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a>Uygulamayı sayfa ve görünümlerde bileşenleri kullanacak şekilde hazırlama

Mevcut bir Razor Pages veya MVC uygulaması, Razor bileşenlerini sayfalara ve görünümlere entegre edebilir:

1. Uygulamanın düzen dosyasında (*_Layout.cshtml):*

   * `<head>` Öğeye aşağıdaki `<base>` etiketi ekleyin:

     ```html
     <base href="~/" />
     ```

     Yukarıdaki `href` örnekteki değer *(uygulama temel yolu)* uygulamanın kök URL yolunda bulunduğunu`/`varsayar ( ). Uygulama bir alt uygulamaysa, <xref:host-and-deploy/blazor/index#app-base-path> makalenin Uygulama temel *yolu* bölümündeki kılavuzu izleyin.

     *_Layout.cshtml* dosyası, Bir Jilet Sayfaları uygulamasındaki *Sayfalar/Paylaşılan* klasöründe veya Bir MVC *uygulamasındaki Görünümler/Paylaşılan* klasörde bulunur.

   * Kapanış `</body>` `<script>` etiketinden hemen önce *blazor.server.js* komut dosyası için bir etiket ekleyin:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Çerçeve uygulamaya *blazor.server.js* komut dosyası ekler. Komut dosyasını uygulamaya el ile eklemenize gerek yoktur.

1. Aşağıdaki içeriği içeren projenin kök klasörüne bir *_Imports.razor* dosyası ekleyin (son ad alanını, `MyAppNamespace`uygulamanın ad alanına değiştirin):

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. Sunucu `Startup.ConfigureServices`hizmetini Blazor kaydedin:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. In `Startup.Configure`, Blazor Hub bitiş `app.UseEndpoints`noktasını ekleyin:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Bileşenleri herhangi bir sayfaya veya görünüme tümleştirin. Daha fazla bilgi için [bir sayfa veya görünüm bölümünden Render bileşenlerine](#render-components-from-a-page-or-view) bakın.

## <a name="use-routable-components-in-a-razor-pages-app"></a>Razor Pages uygulamasında routable bileşenlerini kullanma

*Bu bölüm, kullanıcı isteklerinden doğrudan routable bileşenleri ekleme ile ilgilidir.*

Razor Pages uygulamalarında routable Razor bileşenlerini desteklemek için:

1. Sayfaları ve [görünümleri bölümündebileşenleri kullanmak için uygulamayı Hazırla'daki](#prepare-the-app-to-use-components-in-pages-and-views) kılavuzu izleyin.

1. Proje köküne aşağıdaki içerikle bir *App.razor* dosyası ekleyin:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Aşağıdaki içeriği içeren *Sayfalar* klasörüne *bir _Host.cshtml* dosyası ekleyin:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Bileşenler, düzenleri için paylaşılan *_Layout.cshtml* dosyasını kullanır.

1. *_Host.cshtml* sayfası için bitiş noktası yapılandırmasına `Startup.Configure`düşük öncelikli bir rota ekleyin:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Uygulamaya routable bileşenleri ekleyin. Örneğin:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Ad alanları hakkında daha fazla bilgi için [Bileşen ad alanları](#component-namespaces) bölümüne bakın.

## <a name="use-routable-components-in-an-mvc-app"></a>Bir MVC uygulamasında routable bileşenleri ni kullanma

*Bu bölüm, kullanıcı isteklerinden doğrudan routable bileşenleri ekleme ile ilgilidir.*

MVC uygulamalarında routable Razor bileşenlerini desteklemek için:

1. Sayfaları ve [görünümleri bölümündebileşenleri kullanmak için uygulamayı Hazırla'daki](#prepare-the-app-to-use-components-in-pages-and-views) kılavuzu izleyin.

1. Aşağıdaki içerikle projenin köküne bir *App.razor* dosyası ekleyin:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. *Görünümler/Ev* klasörüne aşağıdaki içeriği içeren bir *_Host.cshtml* dosyası ekleyin:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Bileşenler, düzenleri için paylaşılan *_Layout.cshtml* dosyasını kullanır.

1. Giriş denetleyicisine bir eylem ekleyin:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. *_Host.cshtml* görünümünü bitiş noktası yapılandırmasına döndüren denetleyici eylemi için `Startup.Configure`düşük öncelikli bir rota ekleyin:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Bir *Sayfalar* klasörü oluşturun ve uygulamaya routable bileşenleri ekleyin. Örneğin:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Ad alanları hakkında daha fazla bilgi için [Bileşen ad alanları](#component-namespaces) bölümüne bakın.

## <a name="component-namespaces"></a>Bileşen ad alanları

Uygulamanın bileşenlerini tutmak için özel bir klasör kullanırken, klasörü temsil eden ad alanını sayfaya/görünüme veya *_ViewImports.cshtml* dosyasına ekleyin. Aşağıdaki örnekte:

* Uygulamanın ad alanına değiştirin. `MyAppNamespace`
* Bileşenleri tutmak için *Bileşenler* adlı bir klasör kullanılmazsa, bileşenlerin bulunduğu klasöre değiştirin. `Components`

```cshtml
@using MyAppNamespace.Components
```

*_ViewImports.cshtml* dosyası, Bir Razor Pages uygulamasının *Sayfalar* klasöründe veya bir MVC uygulamasının *Görünümler* klasöründe bulunur.

Daha fazla bilgi için bkz. <xref:blazor/components#import-components>.

## <a name="render-components-from-a-page-or-view"></a>Bileşenleri bir sayfaveya görünümden oluşturma

*Bu bölüm, bileşenlerin kullanıcı isteklerinden doğrudan çıkarılabildiği sayfalara veya görünümlere bileşenler eklemekle ilgilidir.*

Bir bileşeni bir sayfa veya görünümden işlemek için [Bileşen Etiket Yardımcısı'nı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)kullanın.

Bileşenlerin nasıl işlendiği, bileşen durumu ve `Component` Etiket Yardımcısı hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
