---
title: "ASP.NET Core uygulama başlangıç"
author: ardalis
description: "ASP.NET Core başlangıç sınıfında Hizmetleri ve uygulamanın istek ardışık düzenini yapılandırır nasıl bulur."
manager: wpickett
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/08/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/startup
ms.openlocfilehash: cf9e6a25f5b9cc8395c803a11c15622349620a07
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/30/2018
---
# <a name="application-startup-in-aspnet-core"></a>ASP.NET Core uygulama başlangıç

Tarafından [Steve Smith](https://ardalis.com), [zel Dykstra](https://github.com/tdykstra), ve [Luke Latham](https://github.com/guardrex)

`Startup` Sınıfı, hizmetleri ve uygulamanın istek ardışık düzenini yapılandırır.

## <a name="the-startup-class"></a>Başlangıç sınıfı

ASP.NET Core uygulamaları kullanımı bir `Startup` adlı sınıfı `Startup` kural tarafından. `Startup` Sınıfı:

* İsteğe bağlı olarak dahil edebileceğiniz bir [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) yöntemi uygulamanın Hizmetleri'ni yapılandırmak için.
* İçermelidir bir [yapılandırma](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) uygulamanın istek işleme ardışık düzenini oluşturmak için yöntemi.

`ConfigureServices`ve `Configure` uygulama başlatıldığında çalışma zamanı tarafından adı verilir:

[!code-csharp[Main](startup/snapshot_sample/Startup1.cs)]

Belirtin `Startup` ile sınıf [WebHostBuilderExtensions](/dotnet/api/Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions) [UseStartup&lt;TStartup&gt; ](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup#Microsoft_AspNetCore_Hosting_WebHostBuilderExtensions_UseStartup__1_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) yöntemi:

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=10)]

`Startup` Sınıf oluşturucu ana bilgisayar tarafından tanımlanan bağımlılıklar kabul eder. Yaygın kullanımı [bağımlılık ekleme](xref:fundamentals/dependency-injection) içine `Startup` sınıftır eklemesine:

* [IHostingEnvironment](/dotnet/api/Microsoft.AspNetCore.Hosting.IHostingEnvironment) Hizmetleri ortamı tarafından yapılandırılır.
* [IConfiguration](/dotnet/api/microsoft.extensions.configuration.iconfiguration) uygulama başlatma sırasında yapılandırılır.

[!code-csharp[Main](startup/snapshot_sample/Startup2.cs)]

İnjecting alternatif `IHostingEnvironment` kuralları tabanlı bir yaklaşım kullanmaktır. Uygulama ayrı tanımlayabilirsiniz `Startup` farklı ortamlar için sınıflar (örneğin, `StartupDevelopment`), ve uygun başlangıç sınıfı çalışma zamanında seçilir. Geçerli ortamda, bir ad soneki eşleşen sınıfı öncelik. Uygulama geliştirme ortamında çalıştırın ve her ikisi de içeriyorsa bir `Startup` sınıfı ve bir `StartupDevelopment` sınıfı, `StartupDevelopment` sınıfı kullanılır. Daha fazla bilgi için bkz: [birden çok ortamlarıyla çalışma](xref:fundamentals/environments#startup-conventions).

Daha fazla bilgi edinmek için `WebHostBuilder`, bkz: [barındırma](xref:fundamentals/hosting) konu. Başlatma sırasında hata işleme hakkında daha fazla bilgi için bkz: [başlangıç özel durum işleme](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>ConfigureServices yöntemi

[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) yöntemi:

* İsteğe bağlı.
* Önce web ana bilgisayarı tarafından çağrılan `Configure` yöntemi uygulamanın Hizmetleri'ni yapılandırmak için.
* Burada [yapılandırma seçenekleri](xref:fundamentals/configuration/index) kurala göre ayarlanır.

Hizmetler için hizmet kapsayıcı ekleme yapar bunları uygulama içinde ve kullanılabilir `Configure` yöntemi. Hizmetler aracılığıyla çözümlenmiş [bağımlılık ekleme](xref:fundamentals/dependency-injection) veya [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices).

Web ana bilgisayarı önce bazı hizmetler yapılandırabilirsiniz `Startup` yöntemleri çağrılır. Ayrıntılar kullanılabilir [barındırma](xref:fundamentals/hosting) konu. 

Önemli kurulum gerektiren özellikleri vardır `Add[Service]` genişletme yöntemleri [IServiceCollection](/dotnet/api/Microsoft.Extensions.DependencyInjection.IServiceCollection). Tipik web uygulaması için Entity Framework, kimlik ve MVC Hizmetleri kaydeder:

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=4,7,11&start=40&end=55)]

## <a name="services-available-in-startup"></a>Başlangıç kullanılabilir hizmetler

Web ana bilgisayarı tarafından kullanılabilen bazı hizmetler sağlar `Startup` sınıfı oluşturucusu. Ek hizmetler aracılığıyla uygulama ekler `ConfigureServices`. Hem konak hem de uygulama hizmetleri sonra kullanılabilir olan `Configure` ve uygulama boyunca.

## <a name="the-configure-method"></a>Yapılandırma yöntemi

[Yapılandırma](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) yöntemi uygulama HTTP isteklerine nasıl yanıt vereceğini belirtmek için kullanılır. İstek ardışık düzenini ekleyerek yapılandırılmış [ara yazılımı](xref:fundamentals/middleware) bileşenleri için bir [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) örneği. `IApplicationBuilder`kullanılabilir `Configure` yöntemi, ancak hizmet kapsayıcısında kayıtlı değil. Barındırma oluşturur bir `IApplicationBuilder` ve doğrudan geçirir `Configure` ([başvuru kaynağı](https://github.com/aspnet/Hosting/blob/release/2.0.0/src/Microsoft.AspNetCore.Hosting/Internal/WebHost.cs#L179-L192)).

[ASP.NET Core şablonları](/dotnet/core/tools/dotnet-new) bir geliştirici özel durum sayfası desteğiyle ardışık düzenini yapılandırmak [BrowserLink](http://vswebessentials.com/features/browserlink), hata sayfaları, statik dosyalar ve ASP.NET MVC:

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Startup.cs?range=28-48&highlight=5,6,10,13,15)]

Her `Use` genişletme yöntemi, istek ardışık düzenine bir ara yazılım bileşeni ekler. Örneğin, `UseMvc` genişletme yöntemi ekler [yönlendirme ara yazılımı](xref:fundamentals/routing) istek ardışık düzenine ve yapılandırır [MVC](xref:mvc/overview) varsayılan işleyici olarak.

Gibi ek hizmetleri `IHostingEnvironment` ve `ILoggerFactory`, yöntem imzası da belirtilebilir. Kullanılabilir ise ek hizmetler belirtildiğinde, eklenmiş.

Nasıl kullanılacağı hakkında daha fazla bilgi için `IApplicationBuilder`, bkz: [Ara](xref:fundamentals/middleware).

## <a name="convenience-methods"></a>Kullanışlı yöntemler

[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder.configureservices) ve [yapılandırma](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) belirtmek yerine kullanışlı yöntemler kullanılabilir bir `Startup` sınıfı. Birden çok çağrılar `ConfigureServices` birbirine ekleyin. Birden çok çağrılar `Configure` son yöntem çağrısı kullanın.

[!code-csharp[Main](startup/snapshot_sample/Program.cs?highlight=18,22)]

## <a name="startup-filters"></a>Başlangıç filtreleri

Kullanım [IStartupFilter](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) bir uygulamanın başında veya sonunda ara yazılımını yapılandırma [yapılandırma](#the-configure-method) ara yazılım ardışık düzenini. `IStartupFilter`bir ara yazılım önce veya sonra Başlat veya uygulamanın istek işleme ardışık düzen sonunda kitaplıkları tarafından eklenen Ara çalıştığından emin olmak kullanışlıdır.

`IStartupFilter`tek bir yöntem uygulayan [yapılandırma](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter.configure), alır ve döndüren bir `Action<IApplicationBuilder>`. Bir [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) bir uygulamanın istek ardışık düzenini yapılandırmak için bir sınıf tanımlar. Daha fazla bilgi için bkz: [IApplicationBuilder ile Ara yazılım ardışık düzenini oluşturma](xref:fundamentals/middleware#creating-a-middleware-pipeline-with-iapplicationbuilder).

Her `IStartupFilter` istek ardışık düzeninde bir veya daha fazla middlewares uygular. Filtreler, hizmet kapsayıcısı eklendikleri sırayla çağrılır. Filtreleri önce ara ekleyebilir veya sonraki filtre denetimini geçtikten sonra bu nedenle bunlar başına veya uygulama ardışık sonuna ekleyin.

[Örnek uygulaması](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/startup/sample/) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample)) bir ara yazılımı ile kaydetmek gösterilmiştir `IStartupFilter`. Örnek uygulaması bir sorgu dizesi parametresi bir seçenek değeri ayarlar bir ara yazılımı içerir:

[!code-csharp[Main](startup/sample/RequestSetOptionsMiddleware.cs?name=snippet1)]

`RequestSetOptionsMiddleware` Yapılandırılan `RequestSetOptionsStartupFilter` sınıfı:

[!code-csharp[Main](startup/sample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter` Hizmet kapsayıcısında kayıtlı `ConfigureServices`:

[!code-csharp[Main](startup/sample/Startup.cs?name=snippet1&highlight=3)]

Bir sorgu dizesi parametresi için zaman `option` MVC ara yazılımın yanıt işlemeden önce ara değer atama işler sağlanır:

![İşlenen dizin sayfasını gösteren bir tarayıcı penceresi. 'Den ara yazılımı' seçenek kümesi 'Nden Ara' değerini ve sorgu dizesi parametresi sayfasıyla isteyen tabanlı olarak seçeneğinin değerini işlenir.](startup/_static/index.png)

Ara yazılım yürütme sırası olarak ayarlanmış tarafından sırasına göre `IStartupFilter` kayıtlar:

* Birden çok `IStartupFilter` uygulamaları aynı nesnelerle etkileşim. Sıralama önemliyse, sipariş kendi `IStartupFilter` hizmet kendi middlewares çalışması gerektiğini sıraya uyacak şekilde kayıtlar.
* Kitaplık, bir veya daha fazla ile Ara yazılım ekleyin `IStartupFilter` önce veya diğer uygulama ara yazılımı ile kaydedildikten sonra çalışan uygulamaları `IStartupFilter`. Çağrılacak bir `IStartupFilter` Ara kitaplığın tarafından eklenen bir ara yazılım önce `IStartupFilter`, kitaplık hizmet kapsayıcısı eklenmeden önce hizmet kaydı getirin. Daha sonra çağırmak için kitaplık eklendikten sonra hizmet kaydı getirin.

## <a name="additional-resources"></a>Ek kaynaklar

* [Barındırma](xref:fundamentals/hosting)
* [Birden çok ortamı ile çalışma](xref:fundamentals/environments)
* [Ara Yazılım](xref:fundamentals/middleware)
* [Günlüğe kaydetme](xref:fundamentals/logging/index)
* [Yapılandırma](xref:fundamentals/configuration/index)
* [StartupLoader sınıfı: FindStartupType yöntemi (başvuru kaynağı)](https://github.com/aspnet/Hosting/blob/rel/2.0.0/src/Microsoft.AspNetCore.Hosting/Internal/StartupLoader.cs#L66-L116)
