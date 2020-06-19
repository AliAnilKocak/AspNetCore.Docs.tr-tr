---
title: ASP.NET Core Blazor şablonları
author: guardrex
description: ASP.NET Core Blazor uygulama şablonları ve proje yapısı hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 3a491e43aec8291fcf03696b53240bd33e0deda1
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102488"
---
# <a name="aspnet-core-blazor-templates"></a>ASP.NET Core Blazor şablonları

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

BlazorFramework, barındırma modellerinin her biri için uygulama geliştirmeye yönelik şablonlar sağlar Blazor :

* BlazorWebAssembly ( `blazorwasm` )
* BlazorSunucu ( `blazorserver` )

Barındırma modelleri hakkında daha fazla bilgi için Blazor bkz <xref:blazor/hosting-models> ..

Şablondan uygulama oluşturmaya yönelik adım adım yönergeler için Blazor , bkz <xref:blazor/get-started> ..

Şablon seçenekleri, bu `--help` seçeneği [DotNet New](/dotnet/core/tools/dotnet-new) CLI komutuna geçirerek kullanılabilir:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazorproje yapısı

Aşağıdaki dosya ve klasörler Blazor bir şablondan oluşturulan bir uygulamayı yapar Blazor :

* *Program.cs*: uygulamanın şunları ayarlayan giriş noktası:

  * ASP.NET Core [ana bilgisayar](xref:fundamentals/host/generic-host) ( Blazor sunucu)
  * WebAssembly Host ( Blazor webassembly): Bu dosyadaki kod, Blazor webassembly şablonundan () oluşturulan uygulamalar için benzersizdir `blazorwasm` .
    * `App`Uygulamanın kök bileşeni olan bileşeni, `app` yönteminin DOM öğesi olarak belirtilir `Add` .
    * Hizmetler, `ConfigureServices` ana bilgisayar Oluşturucu 'daki yöntemiyle yapılandırılabilir (örneğin, `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).
    * Yapılandırma, ana bilgisayar Oluşturucu () yoluyla sağlanabilir `builder.Configuration` .

* *Startup.cs* ( Blazor sunucu): uygulamanın başlangıç mantığını içerir. `Startup`Sınıfı iki yöntemi tanımlar:

  * `ConfigureServices`: Uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmetlerini yapılandırır. BlazorSunucu uygulamalarında, Hizmetleri çağırarak eklenir <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> ve `WeatherForecastService` örnek bileşen tarafından kullanılmak üzere hizmet kapsayıcısına eklenir `FetchData` .
  * `Configure`: Uygulamanın istek işleme ardışık düzenini yapılandırır:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>, tarayıcıya gerçek zamanlı bağlantı için bir uç nokta ayarlamak üzere çağırılır. Bağlantı, [SignalR](xref:signalr/introduction) uygulamalarına gerçek zamanlı Web işlevselliği ekleme çerçevesi olan ile oluşturulur.
    * [Mapfallbacktopage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) , uygulamanın kök sayfasını (*Pages/_Host. cshtml*) ayarlamak ve gezinmeyi etkinleştirmek için çağırılır.

* *Wwwroot/index.html* ( Blazor webassembly): bir HTML sayfası olarak uygulanan uygulamanın kök sayfası:
  * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
  * Bu sayfa, kök bileşenin nerede `App` işleneceğini belirtir. `App`Bileşen (*app. Razor*), `app` içindeki metoduna DOM öğesi olarak belirtilir `AddComponent` `Startup.Configure` .
  * `_framework/blazor.webassembly.js`JavaScript dosyası yüklenir ve şunları yapın:
    * .NET çalışma zamanını, uygulamayı ve uygulamanın bağımlılıklarını indirir.
    * Uygulamayı çalıştırmak için çalışma zamanını başlatır.

* *App. Razor*: bileşeni kullanarak istemci tarafı yönlendirmeyi ayarlayan uygulamanın kök bileşeni <xref:Microsoft.AspNetCore.Components.Routing.Router> . <xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen tarayıcı gezintisini karşılar ve istenen adresle eşleşen sayfayı işler.

* *Sayfalar* klasörü:*.razor* Blazor uygulamayı ve Razor bir sunucu uygulamasının kök sayfasını oluşturan yönlendirilebilir bileşenleri/sayfaları (. Razor) içerir Blazor . Her sayfanın yolu, yönergesi kullanılarak belirtilir [`@page`](xref:mvc/views/razor#page) . Şablon şunları içerir:
  * *_Host. cshtml* ( Blazor sunucu): bir sayfa olarak uygulanan uygulamanın kök sayfası Razor :
    * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
    * `_framework/blazor.server.js`Tarayıcı ve sunucu arasındaki gerçek zamanlı bağlantıyı ayarlayan JavaScript dosyası yüklenir SignalR .
    * Ana bilgisayar sayfası, kök bileşeni 'nin `App` (*app. Razor*) nerede işleneceğini belirtir.
  * `Counter`(*Counter. Razor*): sayaç sayfasını uygular.
  * `Error`(*Error. Razor*, Blazor Yalnızca sunucu uygulaması): uygulamada işlenmeyen bir özel durum oluştuğunda Işlenir.
  * `FetchData`(*Fetchdata. Razor*): veri getirme sayfasını uygular.
  * `Index`(*Index. Razor*): giriş sayfasını uygular.

* *Paylaşılan* klasör: uygulama tarafından kullanılan diğer Kullanıcı Arabirimi bileşenlerini (*. Razor*) içerir:
  * `MainLayout`(*Mainlayout. Razor*): uygulamanın Düzen bileşeni.
  * `NavMenu`(*Navmenu. Razor*): kenar çubuğu gezintisini uygular. Diğer bileşenlere yönelik gezinti bağlantılarını işleyen [Navlink bileşenini](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ) içerir Razor . Bileşeni, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.

* *_Imports. Razor*: Razor ad alanları için yönergeler gibi uygulamanın bileşenlerine (*. Razor*) dahil etmek için ortak yönergeleri içerir [`@using`](xref:mvc/views/razor#using) .

* *Veri* klasörü ( Blazor sunucu): `WeatherForecast` `WeatherForecastService` uygulamanın bileşenine örnek Hava durumu verileri sağlayan sınıfını ve uygulamasını içerir `FetchData` .

* *Wwwroot*: uygulamanın ortak statik varlıklarını Içeren uygulamanın [Web kök](xref:fundamentals/index#web-root) klasörü.

* *appsettings.js* ( Blazor sunucu): uygulamanın yapılandırma ayarları.
