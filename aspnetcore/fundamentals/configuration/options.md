---
title: ASP.NET Core için seçenek kalıbı
author: guardrex
description: ASP.NET Core uygulamalarında ilgili ayarların gruplarını temsil etmek için seçenekler deseninin nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 753afb9def90fd35122182f260f1dd0ce8d2830a
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288949"
---
# <a name="options-pattern-in-aspnet-core"></a>ASP.NET Core için seçenek kalıbı

[Luke Latham](https://github.com/guardrex) tarafından

::: moniker range=">= aspnetcore-3.0"

Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır. [Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:

* Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; senaryoları (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.
* @No__t, uygulamanın farklı bölümleri için 1 ayarların [ayrılması,](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) birbirine bağımlı veya bir diğeriyle bağlantılı değildir.

Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar. Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Önkoşullar

Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Options. configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.

## <a name="options-interfaces"></a>Seçenekler arabirimleri

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, `TOptions` örnekleri için seçenekleri almak ve seçenek bildirimlerini yönetmek için kullanılır. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aşağıdaki senaryoları destekler:

* Değişiklik bildirimleri
* [Adlandırılmış seçenekler](#named-options-support-with-iconfigurenamedoptions)
* [Yeniden yüklenebilir yapılandırma](#reload-configuration-data-with-ioptionssnapshot)
* Seçmeli seçenekler geçersiz kılma (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)

[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırması oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>, yeni seçenek örnekleri oluşturmaktan sorumludur. Tek bir <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> yöntemi vardır. Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ' ı ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> ' ı alır ve önce tüm yapılandırmaları çalıştırır, sonrasında yapılandırma sonrası. @No__t-0 ve <xref:Microsoft.Extensions.Options.IConfigureOptions%601> arasında ayrım yapar ve yalnızca uygun arabirimi çağırır.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> tarafından `TOptions` örnekleri önbelleğe almak için kullanılır. @No__t-0, değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Değerler, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> ile el ile tanıtılamaz. @No__t-0 yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, her istekte seçeneklerin yeniden hesaplanması gereken senaryolarda faydalıdır. Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.

<xref:Microsoft.Extensions.Options.IOptions%601>, seçenekleri desteklemek için kullanılabilir. Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ' in önceki senaryolarını desteklemez. @No__t-1 arabirimini kullanan mevcut çerçeveler ve kitaplıklarda <xref:Microsoft.Extensions.Options.IOptions%601> kullanmaya devam edebilir ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> tarafından sunulan senaryolara gerek kalmaz.

## <a name="general-options-configuration"></a>Genel Seçenekler yapılandırması

Genel Seçenekler yapılandırması örnek uygulamada &num;1 örnek olarak gösterilmiştir.

Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır. Aşağıdaki `MyOptions` sınıfının iki özelliği vardır, `Option1` ve `Option2`. Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf oluşturucusu `Option1` ' ın varsayılan değerini ayarlar. `Option2`, doğrudan Özellik başlatılarak ayarlanmış bir varsayılan değere sahiptir (*modeller/MyOptions. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

@No__t-0 sınıfı, <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ile hizmet kapsayıcısına eklenir ve yapılandırmaya bağlanır:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Aşağıdaki sayfa modeli, ayarlara erişmek için <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ile [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır (*Sayfalar/Index. cshtml. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Örneğin *appSettings. JSON* dosyası `option1` ve `option2` değerlerini belirtir:

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Bir ayar dosyasından seçenek yapılandırmasını yüklemek için özel <xref:System.Configuration.ConfigurationBuilder> kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> Ayarlar dosyasından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aracılığıyla seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir.

## <a name="configure-simple-options-with-a-delegate"></a>Bir temsilciyle basit seçenekleri yapılandırma

Basit seçeneklerin bir temsilciyle yapılandırılması örnek uygulamada &num;2 örnek olarak gösterilmiştir.

Seçenek değerlerini ayarlamak için bir temsilci kullanın. Örnek uygulama `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Aşağıdaki kodda, hizmet kapsayıcısına ikinci bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmeti eklenir. @No__t-0 ile bağlamayı yapılandırmak için bir temsilci kullanır:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz. Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

@No__t-0 ' a yapılan her çağrı, hizmet kapsayıcısına bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmeti ekler. Yukarıdaki örnekte, `Option1` ve `Option2` değerlerinin ikisi de *appSettings. JSON*içinde belirtilmiştir, ancak `Option1` ve `Option2` değerleri yapılandırılan temsilci tarafından geçersiz kılınır.

Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar. Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Alt seçenekler yapılandırması

Alt seçenekler yapılandırması örnek uygulamada &num;3 örnek olarak gösterilmiştir.

Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır. Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.

Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her özellik `property[:sub-property:]` biçiminde bir yapılandırma anahtarına bağlanır. Örneğin, `MyOptions.Option1` özelliği *appSettings. JSON*içindeki `option1` özelliğinden okunan `Option1` anahtarına bağlanır.

Aşağıdaki kodda, hizmet kapsayıcısına üçüncü bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmeti eklenir. @No__t-0 ' i *appSettings. JSON* dosyasının `subsection` bölümüne bağlar:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

@No__t-0 genişletme yöntemi, [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet paketini gerektirir. Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya üzeri) kullanıyorsa, paket otomatik olarak eklenir.

Örneğin *appSettings. JSON* dosyası, `suboption1` ve `suboption2` anahtarlarına sahip `subsection` üyesini tanımlar:

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

@No__t-0 sınıfı, Seçenekler değerlerini tutmak için, `SubOption1` ve `SubOption2` özelliklerini tanımlar (*modeller/Myalt seçenekler. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Sayfa modelinin `OnGet` yöntemi, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Uygulama çalıştırıldığında `OnGet` yöntemi, alt sınıf değerlerini gösteren bir dize döndürür:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Bir görünüm modeli veya doğrudan görünüm ekleme ile belirtilen seçenekler

Bir görünüm modeli veya doğrudan görünüm ekleme ile sunulan seçenekler örnek uygulamada &num;4 örnek olarak gösterilmiştir.

Seçenekler bir görünüm modelinde veya ekleme <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> tarafından doğrudan bir görünüme (*Sayfalar/Index. cshtml. cs*) sağlanabilir:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Örnek uygulama, `@inject` yönergesiyle `IOptionsMonitor<MyOptions>` ' nın nasıl ekleneceğini gösterir:

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yaparak.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme

Yapılandırma verilerini <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ile yeniden yükleme, örnek uygulamada &num;5 örnek olarak gösterilmiştir.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, minimum işleme yüküyle yeniden yükleme seçeneklerini destekler.

Seçenekler erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında her istek için bir kez hesaplanır.

Aşağıdaki örnek, *appSettings. JSON* değişikliklerinden sonra New <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ' ın nasıl oluşturulduğunu gösterir (*Pages/Index. cshtml. cs*). Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar *appSettings. JSON* dosyası tarafından belirtilen sabit değerler döndürüyor.

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Aşağıdaki görüntüde, *appSettings. JSON* dosyasından yüklenen ilk @no__t 0 ve `option2` değerleri gösterilmektedir:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

*AppSettings. JSON* dosyasındaki değerleri `value1_from_json UPDATED` ve `200` ' e değiştirin. *AppSettings. JSON* dosyasını kaydedin. Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>IController Enamedooptıons ile adlandırılmış seçenekler desteği

@No__t-0 ile adlandırılmış seçenekler desteği örnek uygulamada &num;6 örnek olarak gösterilmiştir.

*Adlandırılmış seçenekler* desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir. Örnek uygulamada, adlandırılmış Seçenekler [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir ve bu, [configurenamedooptıons @ No__t-2toptions > ' i çağırır. ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)Uzantı yöntemini Yapılandır:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Örnek uygulama <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index. cshtml. cs*) ile adlandırılmış seçeneklere erişir:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1` değerleri, *appSettings. JSON* dosyasından yüklenen yapılandırmadan sağlanır. `named_options_2` değerleri tarafından sağlanır:

* @No__t-2 için `ConfigureServices` `named_options_2` temsilcisi.
* @No__t-1 sınıfı tarafından sunulan `Option2` için varsayılan değer.

## <a name="configure-all-options-with-the-configureall-method"></a>Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma

Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın. Aşağıdaki kod, ortak bir değere sahip tüm yapılandırma örnekleri için `Option1` yapılandırır. Aşağıdaki kodu `Startup.ConfigureServices` yöntemine el ile ekleyin:

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Tüm seçenekler adlandırılmış örneklerdir. Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekleri, `string.Empty` olan `Options.DefaultName` örneğini hedefleyecek şekilde değerlendirilir. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ' i de uygular. @No__t-0 ' ın varsayılan uygulamasının her birini uygun şekilde kullanma mantığı vardır. @No__t-0 adlandırılmış seçeneği, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın).

## <a name="optionsbuilder-api"></a>Seçenekno Oluşturucu API 'SI

<xref:Microsoft.Extensions.Options.OptionsBuilder%601> `TOptions` örnekleri yapılandırmak için kullanılır. `OptionsBuilder` @no__t, sonraki çağrıların tümünde görünmesini yerine yalnızca tek bir parametre olan adlandırılmış seçenekleri oluşturmayı kolaylaştırır. Seçenekler doğrulaması ve hizmet bağımlılıklarını kabul eden `ConfigureOptions` aşırı yüklemeleri yalnızca `OptionsBuilder` aracılığıyla kullanılabilir.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Ayarları yapılandırmak için dı hizmetlerini kullanma

Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:

* [Options Builder @ no__t-2TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1)üzerinde [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin. Options [Builder @ no__t-1TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza olanak sağlayan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* @No__t-0 veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> uygulayan kendi türünü oluşturun ve türü bir hizmet olarak kaydedin.

Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz. Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir. [Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı, belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici genel <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ' i kaydettirir. 

## <a name="options-validation"></a>Seçenekler doğrulaması

Seçenekler doğrulaması seçenekler yapılandırıldığında seçenekleri doğrulamanızı sağlar. Seçenekler geçerliyse `true` döndüren bir doğrulama yöntemiyle `Validate` ' i çağırın ve geçerli değillerse-2 ' ye @no__t:

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

Yukarıdaki örnek, adlandırılmış seçenekler örneğini `optionalOptionsName` olarak ayarlar. Varsayılan Seçenekler örneği `Options.DefaultName` ' dır.

Seçenekler örneği oluşturulduğunda doğrulama çalıştırılır. Seçenek Örneğinizde, ilk kez erişildiğinde doğrulamanın başarılı olması garanti edilir.

> [!IMPORTANT]
> Seçenekler, Seçenekler başlangıçta yapılandırıldıktan ve doğrulandıktan sonra seçeneklerindeki değişikliklere karşı koruma yapmaz.

@No__t-0 yöntemi `Func<TOptions, bool>` kabul eder. Doğrulamayı tamamen özelleştirmek için, şunları sağlayan `IValidateOptions<TOptions>` ' ı uygulayın:

* Birden çok seçenek türü doğrulaması: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Başka bir seçenek türüne bağlı olan doğrulama: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` şunları doğrular:

* Belirli bir adlandırılmış seçenekler örneği.
* @No__t-0 `null` olduğunda tüm seçenekler.

Arabirimin uygulanınızdan bir `ValidateOptionsResult` döndürün:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Veri ek açıklaması tabanlı doğrulama, `OptionsBuilder<TOptions>` ' de <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> yöntemi çağırarak [Microsoft. Extensions. Options. DataAnnotation](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden kullanılabilir. `Microsoft.Extensions.Options.DataAnnotations`, [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e dahildir (ASP.NET Core 2,2 veya üzeri).

```csharp
using System.ComponentModel.DataAnnotations;
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().Value);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

Eager doğrulaması (başlangıçta hızlı başarısız), gelecek bir sürüm için dikkate alınmaz.

## <a name="options-post-configuration"></a>Yapılandırma sonrası seçenekler

Yapılandırma sonrası <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> olarak ayarlayın. Tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırması oluştuktan sonra yapılandırma sonrası çalıştırılır:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>, adlandırılmış seçenekleri yapılandırmak için kullanılabilir:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Tüm yapılandırma örneklerini yapılandırmak için <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> kullanın:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Başlangıç sırasında seçeneklere erişme

<xref:Microsoft.Extensions.Options.IOptions%601> ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.Configure` ' de kullanılabilir. çünkü hizmetler `Configure` Yöntemi yürütülmeden önce oluşturulmuştur.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

@No__t-2 ' de <xref:Microsoft.Extensions.Options.IOptions%601> veya <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> kullanmayın. Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır. [Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:

* Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; senaryoları (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.
* @No__t, uygulamanın farklı bölümleri için 1 ayarların [ayrılması,](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) birbirine bağımlı veya bir diğeriyle bağlantılı değildir.

Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar. Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Önkoşullar

Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Options. configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.

## <a name="options-interfaces"></a>Seçenekler arabirimleri

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, `TOptions` örnekleri için seçenekleri almak ve seçenek bildirimlerini yönetmek için kullanılır. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aşağıdaki senaryoları destekler:

* Değişiklik bildirimleri
* [Adlandırılmış seçenekler](#named-options-support-with-iconfigurenamedoptions)
* [Yeniden yüklenebilir yapılandırma](#reload-configuration-data-with-ioptionssnapshot)
* Seçmeli seçenekler geçersiz kılma (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)

[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırması oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>, yeni seçenek örnekleri oluşturmaktan sorumludur. Tek bir <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> yöntemi vardır. Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ' ı ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> ' ı alır ve önce tüm yapılandırmaları çalıştırır, sonrasında yapılandırma sonrası. @No__t-0 ve <xref:Microsoft.Extensions.Options.IConfigureOptions%601> arasında ayrım yapar ve yalnızca uygun arabirimi çağırır.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> tarafından `TOptions` örnekleri önbelleğe almak için kullanılır. @No__t-0, değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Değerler, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> ile el ile tanıtılamaz. @No__t-0 yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, her istekte seçeneklerin yeniden hesaplanması gereken senaryolarda faydalıdır. Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.

<xref:Microsoft.Extensions.Options.IOptions%601>, seçenekleri desteklemek için kullanılabilir. Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ' in önceki senaryolarını desteklemez. @No__t-1 arabirimini kullanan mevcut çerçeveler ve kitaplıklarda <xref:Microsoft.Extensions.Options.IOptions%601> kullanmaya devam edebilir ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> tarafından sunulan senaryolara gerek kalmaz.

## <a name="general-options-configuration"></a>Genel Seçenekler yapılandırması

Genel Seçenekler yapılandırması örnek uygulamada &num;1 örnek olarak gösterilmiştir.

Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır. Aşağıdaki `MyOptions` sınıfının iki özelliği vardır, `Option1` ve `Option2`. Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf oluşturucusu `Option1` ' ın varsayılan değerini ayarlar. `Option2`, doğrudan Özellik başlatılarak ayarlanmış bir varsayılan değere sahiptir (*modeller/MyOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

@No__t-0 sınıfı, <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ile hizmet kapsayıcısına eklenir ve yapılandırmaya bağlanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Aşağıdaki sayfa modeli, ayarlara erişmek için <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ile [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır (*Sayfalar/Index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Örneğin *appSettings. JSON* dosyası `option1` ve `option2` değerlerini belirtir:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Bir ayar dosyasından seçenek yapılandırmasını yüklemek için özel <xref:System.Configuration.ConfigurationBuilder> kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> Ayarlar dosyasından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aracılığıyla seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir.

## <a name="configure-simple-options-with-a-delegate"></a>Bir temsilciyle basit seçenekleri yapılandırma

Basit seçeneklerin bir temsilciyle yapılandırılması örnek uygulamada &num;2 örnek olarak gösterilmiştir.

Seçenek değerlerini ayarlamak için bir temsilci kullanın. Örnek uygulama `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Aşağıdaki kodda, hizmet kapsayıcısına ikinci bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmeti eklenir. @No__t-0 ile bağlamayı yapılandırmak için bir temsilci kullanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz. Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

@No__t-0 ' a yapılan her çağrı, hizmet kapsayıcısına bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmeti ekler. Yukarıdaki örnekte, `Option1` ve `Option2` değerlerinin ikisi de *appSettings. JSON*içinde belirtilmiştir, ancak `Option1` ve `Option2` değerleri yapılandırılan temsilci tarafından geçersiz kılınır.

Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar. Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Alt seçenekler yapılandırması

Alt seçenekler yapılandırması örnek uygulamada &num;3 örnek olarak gösterilmiştir.

Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır. Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.

Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her özellik `property[:sub-property:]` biçiminde bir yapılandırma anahtarına bağlanır. Örneğin, `MyOptions.Option1` özelliği *appSettings. JSON*içindeki `option1` özelliğinden okunan `Option1` anahtarına bağlanır.

Aşağıdaki kodda, hizmet kapsayıcısına üçüncü bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmeti eklenir. @No__t-0 ' i *appSettings. JSON* dosyasının `subsection` bölümüne bağlar:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

@No__t-0 genişletme yöntemi, [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet paketini gerektirir. Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya üzeri) kullanıyorsa, paket otomatik olarak eklenir.

Örneğin *appSettings. JSON* dosyası, `suboption1` ve `suboption2` anahtarlarına sahip `subsection` üyesini tanımlar:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

@No__t-0 sınıfı, Seçenekler değerlerini tutmak için, `SubOption1` ve `SubOption2` özelliklerini tanımlar (*modeller/Myalt seçenekler. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Sayfa modelinin `OnGet` yöntemi, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Uygulama çalıştırıldığında `OnGet` yöntemi, alt sınıf değerlerini gösteren bir dize döndürür:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Bir görünüm modeli veya doğrudan görünüm ekleme ile belirtilen seçenekler

Bir görünüm modeli veya doğrudan görünüm ekleme ile sunulan seçenekler örnek uygulamada &num;4 örnek olarak gösterilmiştir.

Seçenekler bir görünüm modelinde veya ekleme <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> tarafından doğrudan bir görünüme (*Sayfalar/Index. cshtml. cs*) sağlanabilir:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Örnek uygulama, `@inject` yönergesiyle `IOptionsMonitor<MyOptions>` ' nın nasıl ekleneceğini gösterir:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yaparak.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme

Yapılandırma verilerini <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ile yeniden yükleme, örnek uygulamada &num;5 örnek olarak gösterilmiştir.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, minimum işleme yüküyle yeniden yükleme seçeneklerini destekler.

Seçenekler erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında her istek için bir kez hesaplanır.

Aşağıdaki örnek, *appSettings. JSON* değişikliklerinden sonra New <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ' ın nasıl oluşturulduğunu gösterir (*Pages/Index. cshtml. cs*). Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar *appSettings. JSON* dosyası tarafından belirtilen sabit değerler döndürüyor.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Aşağıdaki görüntüde, *appSettings. JSON* dosyasından yüklenen ilk @no__t 0 ve `option2` değerleri gösterilmektedir:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

*AppSettings. JSON* dosyasındaki değerleri `value1_from_json UPDATED` ve `200` ' e değiştirin. *AppSettings. JSON* dosyasını kaydedin. Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>IController Enamedooptıons ile adlandırılmış seçenekler desteği

@No__t-0 ile adlandırılmış seçenekler desteği örnek uygulamada &num;6 örnek olarak gösterilmiştir.

*Adlandırılmış seçenekler* desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir. Örnek uygulamada, adlandırılmış Seçenekler [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir ve bu, [configurenamedooptıons @ No__t-2toptions > ' i çağırır. ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)Uzantı yöntemini Yapılandır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Örnek uygulama <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index. cshtml. cs*) ile adlandırılmış seçeneklere erişir:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1` değerleri, *appSettings. JSON* dosyasından yüklenen yapılandırmadan sağlanır. `named_options_2` değerleri tarafından sağlanır:

* @No__t-2 için `ConfigureServices` `named_options_2` temsilcisi.
* @No__t-1 sınıfı tarafından sunulan `Option2` için varsayılan değer.

## <a name="configure-all-options-with-the-configureall-method"></a>Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma

Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın. Aşağıdaki kod, ortak bir değere sahip tüm yapılandırma örnekleri için `Option1` yapılandırır. Aşağıdaki kodu `Startup.ConfigureServices` yöntemine el ile ekleyin:

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Tüm seçenekler adlandırılmış örneklerdir. Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekleri, `string.Empty` olan `Options.DefaultName` örneğini hedefleyecek şekilde değerlendirilir. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ' i de uygular. @No__t-0 ' ın varsayılan uygulamasının her birini uygun şekilde kullanma mantığı vardır. @No__t-0 adlandırılmış seçeneği, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın).

## <a name="optionsbuilder-api"></a>Seçenekno Oluşturucu API 'SI

<xref:Microsoft.Extensions.Options.OptionsBuilder%601> `TOptions` örnekleri yapılandırmak için kullanılır. `OptionsBuilder` @no__t, sonraki çağrıların tümünde görünmesini yerine yalnızca tek bir parametre olan adlandırılmış seçenekleri oluşturmayı kolaylaştırır. Seçenekler doğrulaması ve hizmet bağımlılıklarını kabul eden `ConfigureOptions` aşırı yüklemeleri yalnızca `OptionsBuilder` aracılığıyla kullanılabilir.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Ayarları yapılandırmak için dı hizmetlerini kullanma

Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:

* [Options Builder @ no__t-2TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1)üzerinde [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin. Options [Builder @ no__t-1TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza olanak sağlayan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* @No__t-0 veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> uygulayan kendi türünü oluşturun ve türü bir hizmet olarak kaydedin.

Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz. Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir. [Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı, belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici genel <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ' i kaydettirir. 

## <a name="options-validation"></a>Seçenekler doğrulaması

Seçenekler doğrulaması seçenekler yapılandırıldığında seçenekleri doğrulamanızı sağlar. Seçenekler geçerliyse `true` döndüren bir doğrulama yöntemiyle `Validate` ' i çağırın ve geçerli değillerse-2 ' ye @no__t:

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

Yukarıdaki örnek, adlandırılmış seçenekler örneğini `optionalOptionsName` olarak ayarlar. Varsayılan Seçenekler örneği `Options.DefaultName` ' dır.

Seçenekler örneği oluşturulduğunda doğrulama çalıştırılır. Seçenek Örneğinizde, ilk kez erişildiğinde doğrulamanın başarılı olması garanti edilir.

> [!IMPORTANT]
> Seçenekler, Seçenekler başlangıçta yapılandırıldıktan ve doğrulandıktan sonra seçeneklerindeki değişikliklere karşı koruma yapmaz.

@No__t-0 yöntemi `Func<TOptions, bool>` kabul eder. Doğrulamayı tamamen özelleştirmek için, şunları sağlayan `IValidateOptions<TOptions>` ' ı uygulayın:

* Birden çok seçenek türü doğrulaması: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Başka bir seçenek türüne bağlı olan doğrulama: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` şunları doğrular:

* Belirli bir adlandırılmış seçenekler örneği.
* @No__t-0 `null` olduğunda tüm seçenekler.

Arabirimin uygulanınızdan bir `ValidateOptionsResult` döndürün:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Veri ek açıklaması tabanlı doğrulama, `OptionsBuilder<TOptions>` ' de <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> yöntemi çağırarak [Microsoft. Extensions. Options. DataAnnotation](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden kullanılabilir. `Microsoft.Extensions.Options.DataAnnotations`, [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e dahildir (ASP.NET Core 2,2 veya üzeri).

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().Value);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

Eager doğrulaması (başlangıçta hızlı başarısız), gelecek bir sürüm için dikkate alınmaz.

## <a name="options-post-configuration"></a>Yapılandırma sonrası seçenekler

Yapılandırma sonrası <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> olarak ayarlayın. Tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırması oluştuktan sonra yapılandırma sonrası çalıştırılır:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>, adlandırılmış seçenekleri yapılandırmak için kullanılabilir:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Tüm yapılandırma örneklerini yapılandırmak için <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> kullanın:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Başlangıç sırasında seçeneklere erişme

<xref:Microsoft.Extensions.Options.IOptions%601> ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.Configure` ' de kullanılabilir. çünkü hizmetler `Configure` Yöntemi yürütülmeden önce oluşturulmuştur.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

@No__t-2 ' de <xref:Microsoft.Extensions.Options.IOptions%601> veya <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> kullanmayın. Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır. [Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:

* Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; senaryoları (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.
* @No__t, uygulamanın farklı bölümleri için 1 ayarların [ayrılması,](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) birbirine bağımlı veya bir diğeriyle bağlantılı değildir.

Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar. Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Önkoşullar

Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Options. configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.

## <a name="options-interfaces"></a>Seçenekler arabirimleri

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, `TOptions` örnekleri için seçenekleri almak ve seçenek bildirimlerini yönetmek için kullanılır. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aşağıdaki senaryoları destekler:

* Değişiklik bildirimleri
* [Adlandırılmış seçenekler](#named-options-support-with-iconfigurenamedoptions)
* [Yeniden yüklenebilir yapılandırma](#reload-configuration-data-with-ioptionssnapshot)
* Seçmeli seçenekler geçersiz kılma (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)

[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırması oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>, yeni seçenek örnekleri oluşturmaktan sorumludur. Tek bir <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> yöntemi vardır. Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ' ı ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> ' ı alır ve önce tüm yapılandırmaları çalıştırır, sonrasında yapılandırma sonrası. @No__t-0 ve <xref:Microsoft.Extensions.Options.IConfigureOptions%601> arasında ayrım yapar ve yalnızca uygun arabirimi çağırır.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> tarafından `TOptions` örnekleri önbelleğe almak için kullanılır. @No__t-0, değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Değerler, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> ile el ile tanıtılamaz. @No__t-0 yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, her istekte seçeneklerin yeniden hesaplanması gereken senaryolarda faydalıdır. Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.

<xref:Microsoft.Extensions.Options.IOptions%601>, seçenekleri desteklemek için kullanılabilir. Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ' in önceki senaryolarını desteklemez. @No__t-1 arabirimini kullanan mevcut çerçeveler ve kitaplıklarda <xref:Microsoft.Extensions.Options.IOptions%601> kullanmaya devam edebilir ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> tarafından sunulan senaryolara gerek kalmaz.

## <a name="general-options-configuration"></a>Genel Seçenekler yapılandırması

Genel Seçenekler yapılandırması örnek uygulamada &num;1 örnek olarak gösterilmiştir.

Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır. Aşağıdaki `MyOptions` sınıfının iki özelliği vardır, `Option1` ve `Option2`. Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf oluşturucusu `Option1` ' ın varsayılan değerini ayarlar. `Option2`, doğrudan Özellik başlatılarak ayarlanmış bir varsayılan değere sahiptir (*modeller/MyOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

@No__t-0 sınıfı, <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ile hizmet kapsayıcısına eklenir ve yapılandırmaya bağlanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Aşağıdaki sayfa modeli, ayarlara erişmek için <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ile [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır (*Sayfalar/Index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Örneğin *appSettings. JSON* dosyası `option1` ve `option2` değerlerini belirtir:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Bir ayar dosyasından seçenek yapılandırmasını yüklemek için özel <xref:System.Configuration.ConfigurationBuilder> kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> Ayarlar dosyasından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aracılığıyla seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir.

## <a name="configure-simple-options-with-a-delegate"></a>Bir temsilciyle basit seçenekleri yapılandırma

Basit seçeneklerin bir temsilciyle yapılandırılması örnek uygulamada &num;2 örnek olarak gösterilmiştir.

Seçenek değerlerini ayarlamak için bir temsilci kullanın. Örnek uygulama `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Aşağıdaki kodda, hizmet kapsayıcısına ikinci bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmeti eklenir. @No__t-0 ile bağlamayı yapılandırmak için bir temsilci kullanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz. Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

@No__t-0 ' a yapılan her çağrı, hizmet kapsayıcısına bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmeti ekler. Yukarıdaki örnekte, `Option1` ve `Option2` değerlerinin ikisi de *appSettings. JSON*içinde belirtilmiştir, ancak `Option1` ve `Option2` değerleri yapılandırılan temsilci tarafından geçersiz kılınır.

Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar. Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Alt seçenekler yapılandırması

Alt seçenekler yapılandırması örnek uygulamada &num;3 örnek olarak gösterilmiştir.

Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır. Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.

Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her özellik `property[:sub-property:]` biçiminde bir yapılandırma anahtarına bağlanır. Örneğin, `MyOptions.Option1` özelliği *appSettings. JSON*içindeki `option1` özelliğinden okunan `Option1` anahtarına bağlanır.

Aşağıdaki kodda, hizmet kapsayıcısına üçüncü bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmeti eklenir. @No__t-0 ' i *appSettings. JSON* dosyasının `subsection` bölümüne bağlar:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

@No__t-0 genişletme yöntemi, [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet paketini gerektirir. Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya üzeri) kullanıyorsa, paket otomatik olarak eklenir.

Örneğin *appSettings. JSON* dosyası, `suboption1` ve `suboption2` anahtarlarına sahip `subsection` üyesini tanımlar:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

@No__t-0 sınıfı, Seçenekler değerlerini tutmak için, `SubOption1` ve `SubOption2` özelliklerini tanımlar (*modeller/Myalt seçenekler. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Sayfa modelinin `OnGet` yöntemi, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Uygulama çalıştırıldığında `OnGet` yöntemi, alt sınıf değerlerini gösteren bir dize döndürür:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Bir görünüm modeli veya doğrudan görünüm ekleme ile belirtilen seçenekler

Bir görünüm modeli veya doğrudan görünüm ekleme ile sunulan seçenekler örnek uygulamada &num;4 örnek olarak gösterilmiştir.

Seçenekler bir görünüm modelinde veya ekleme <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> tarafından doğrudan bir görünüme (*Sayfalar/Index. cshtml. cs*) sağlanabilir:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Örnek uygulama, `@inject` yönergesiyle `IOptionsMonitor<MyOptions>` ' nın nasıl ekleneceğini gösterir:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yaparak.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme

Yapılandırma verilerini <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ile yeniden yükleme, örnek uygulamada &num;5 örnek olarak gösterilmiştir.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, minimum işleme yüküyle yeniden yükleme seçeneklerini destekler.

Seçenekler erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında her istek için bir kez hesaplanır.

Aşağıdaki örnek, *appSettings. JSON* değişikliklerinden sonra New <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ' ın nasıl oluşturulduğunu gösterir (*Pages/Index. cshtml. cs*). Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar *appSettings. JSON* dosyası tarafından belirtilen sabit değerler döndürüyor.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Aşağıdaki görüntüde, *appSettings. JSON* dosyasından yüklenen ilk @no__t 0 ve `option2` değerleri gösterilmektedir:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

*AppSettings. JSON* dosyasındaki değerleri `value1_from_json UPDATED` ve `200` ' e değiştirin. *AppSettings. JSON* dosyasını kaydedin. Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>IController Enamedooptıons ile adlandırılmış seçenekler desteği

@No__t-0 ile adlandırılmış seçenekler desteği örnek uygulamada &num;6 örnek olarak gösterilmiştir.

*Adlandırılmış seçenekler* desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir. Örnek uygulamada, adlandırılmış Seçenekler [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir ve bu, [configurenamedooptıons @ No__t-2toptions > ' i çağırır. ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)Uzantı yöntemini Yapılandır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Örnek uygulama <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index. cshtml. cs*) ile adlandırılmış seçeneklere erişir:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1` değerleri, *appSettings. JSON* dosyasından yüklenen yapılandırmadan sağlanır. `named_options_2` değerleri tarafından sağlanır:

* @No__t-2 için `ConfigureServices` `named_options_2` temsilcisi.
* @No__t-1 sınıfı tarafından sunulan `Option2` için varsayılan değer.

## <a name="configure-all-options-with-the-configureall-method"></a>Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma

Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın. Aşağıdaki kod, ortak bir değere sahip tüm yapılandırma örnekleri için `Option1` yapılandırır. Aşağıdaki kodu `Startup.ConfigureServices` yöntemine el ile ekleyin:

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Tüm seçenekler adlandırılmış örneklerdir. Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekleri, `string.Empty` olan `Options.DefaultName` örneğini hedefleyecek şekilde değerlendirilir. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ' i de uygular. @No__t-0 ' ın varsayılan uygulamasının her birini uygun şekilde kullanma mantığı vardır. @No__t-0 adlandırılmış seçeneği, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın).

## <a name="optionsbuilder-api"></a>Seçenekno Oluşturucu API 'SI

<xref:Microsoft.Extensions.Options.OptionsBuilder%601> `TOptions` örnekleri yapılandırmak için kullanılır. `OptionsBuilder` @no__t, sonraki çağrıların tümünde görünmesini yerine yalnızca tek bir parametre olan adlandırılmış seçenekleri oluşturmayı kolaylaştırır. Seçenekler doğrulaması ve hizmet bağımlılıklarını kabul eden `ConfigureOptions` aşırı yüklemeleri yalnızca `OptionsBuilder` aracılığıyla kullanılabilir.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Ayarları yapılandırmak için dı hizmetlerini kullanma

Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:

* [Options Builder @ no__t-2TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1)üzerinde [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin. Options [Builder @ no__t-1TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza olanak sağlayan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* @No__t-0 veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> uygulayan kendi türünü oluşturun ve türü bir hizmet olarak kaydedin.

Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz. Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir. [Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı, belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici genel <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ' i kaydettirir. 

## <a name="options-post-configuration"></a>Yapılandırma sonrası seçenekler

Yapılandırma sonrası <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> olarak ayarlayın. Tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırması oluştuktan sonra yapılandırma sonrası çalıştırılır:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>, adlandırılmış seçenekleri yapılandırmak için kullanılabilir:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Tüm yapılandırma örneklerini yapılandırmak için <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> kullanın:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Başlangıç sırasında seçeneklere erişme

<xref:Microsoft.Extensions.Options.IOptions%601> ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.Configure` ' de kullanılabilir. çünkü hizmetler `Configure` Yöntemi yürütülmeden önce oluşturulmuştur.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

@No__t-2 ' de <xref:Microsoft.Extensions.Options.IOptions%601> veya <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> kullanmayın. Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/configuration/index>
