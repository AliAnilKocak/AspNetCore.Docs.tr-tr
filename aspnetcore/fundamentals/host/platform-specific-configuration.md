---
title: ASP.NET Core barındırma başlangıç derlemeleri kullanma
author: rick-anderson
description: Uygulama Ihostingstartup kullanarak dış bütünleştirilmiş koddan bir ASP.NET Core uygulaması geliştirmek nasıl keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: 71fd5cf1934b5374e0a393e055db23b98c03b62f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660400"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>ASP.NET Core barındırma başlangıç derlemeleri kullanma

[Palete kroni](https://github.com/pakrym)

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (barındırma başlatma) uygulaması, bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler ekler. Örneğin, bir harici kitaplık ek yapılandırma sağlayıcıları ya da bir uygulama hizmetlerini barındıran bir başlangıç uygulaması kullanabilirsiniz.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>HostingStartup özniteliği

[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, çalışma zamanında etkinleştirilecek bir barındırma başlangıç derlemesinin varlığını gösterir.

Giriş derlemesi veya `Startup` sınıfını içeren derleme `HostingStartup` özniteliği için otomatik olarak taranır. `HostingStartup` özniteliklerin aranacağı derlemelerin listesi [Webhostdefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)içindeki yapılandırmadan çalışma zamanında yüklenir. Bulmadan dışlanacak derlemelerin listesi [Webhostdefaults. Hostingstartupexcludederlemelieskey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)öğesinden yüklendi.

Aşağıdaki örnekte, barındırma başlangıç derlemesinin ad alanı `StartupEnhancement`. Barındırma başlangıç kodunu içeren sınıf `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

`HostingStartup` özniteliği genellikle barındırma başlangıç derlemesinin `IHostingStartup` uygulama sınıfı dosyasında bulunur.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Yüklenen barındırma başlangıç derlemeler keşfedin

Yüklenen barındırma başlangıç derlemeleri bulmak için günlük kaydını etkinleştirmek ve uygulama günlüklerini kontrol edin. Derlemeler yüklenirken oluşan hataları günlüğe kaydedilir. Yüklenen barındırma başlangıç derlemeler hata ayıklama düzeyinde kaydedilir ve tüm hatalar kaydedilir.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Başlangıç derlemeleri barındırma otomatik yüklemeyi devre dışı

Başlangıç derlemeleri barındırma otomatik yüklemeyi devre dışı bırakmak için aşağıdaki yaklaşımlardan birini kullanın:

* Tüm barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini `true` veya `1`olarak ayarlayın:

  * Barındırma başlangıç ana bilgisayar yapılandırma ayarını önle:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * ortam değişkeni `ASPNETCORE_PREVENTHOSTINGSTARTUP`.

* Belirli barındırma başlangıç derlemeleri yüklenmesini önlemek için aşağıdakilerden birini başlangıçta hariç tutmak için başlangıç derlemeleri barındırma noktalı virgülle ayrılmış bir dizeye ayarlayın:

  * Barındırma başlatma derlemeleri dışlama ana bilgisayar yapılandırma ayarı:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * ortam değişkeni `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.

Hem ana bilgisayar yapılandırma ayarı ve ortam değişkenini ayarlarsanız, ana bilgisayar ayarını davranışını denetler.

Ana bilgisayar ayarı veya ortam değişkenini kullanarak barındırma başlangıç derlemeleri devre dışı bırakma, derlemenin genel olarak devre dışı bırakır ve uygulama çeşitli özelliklerini devre dışı bırakabilir.

## <a name="project"></a>Project

Bir barındırma başlatma aşağıdaki proje türlerini birini oluşturun:

* [Sınıf kitaplığı](#class-library)
* [Giriş noktası olmayan konsol uygulaması](#console-app-without-an-entry-point)

### <a name="class-library"></a>Sınıf kitaplığı

Bir barındırma başlangıç geliştirme'de sınıf kitaplığının sağlanabilir. Kitaplık bir `HostingStartup` özniteliği içerir.

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) bir Razor Pages uygulaması, *Hostingstartupapp*ve bir sınıf kitaplığı, *hostingstartuplibrary*içerir. Sınıf kitaplığı:

* `IHostingStartup`uygulayan `ServiceKeyInjection`bir barındırma başlangıç sınıfı içerir. `ServiceKeyInjection`, bellek içi yapılandırma sağlayıcısını ([Addınmemorycollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) kullanarak uygulama yapılandırmasına bir hizmet dizesi çifti ekler.
* , Barındırma başlatmasının ad alanını ve sınıfını tanımlayan bir `HostingStartup` özniteliği içerir.

`ServiceKeyInjection` sınıfının <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir uygulamaya geliştirmeler eklemek için bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> kullanır.

*Hostingstartuplibrary/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Uygulama dizin sayfasına okur ve Sınıf Kitaplığı'nızın barındırma başlangıç derlemesi tarafından ayarlanmış iki anahtarı için yapılandırma değerlerini işler:

*Hostingstartupapp/Pages/Index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ayrıca ayrı bir barındırma başlatma, *Hostingstartuppackage*sağlayan bir NuGet paket projesi içerir. Paket, daha önce açıklanan Sınıf Kitaplığı'nın aynı özelliklere sahiptir. Paketi:

* `IHostingStartup`uygulayan `ServiceKeyInjection`bir barındırma başlangıç sınıfı içerir. `ServiceKeyInjection`, uygulamanın yapılandırmasına bir çift hizmet dizesi ekler.
* Bir `HostingStartup` özniteliği içerir.

*Hostingstartuppackage/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Uygulama dizin sayfasına okur ve paketin barındırma başlangıç derlemesi tarafından ayarlanmış iki anahtarı için yapılandırma değerlerini işler:

*Hostingstartupapp/Pages/Index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Konsol uygulaması giriş noktası olmayan

*Bu yaklaşım, .NET Framework değil yalnızca .NET Core uygulamaları için kullanılabilir.*

Etkinleştirme için derleme zamanı başvurusu gerektirmeyen dinamik barındırma başlatma geliştirmesi, bir `HostingStartup` özniteliği içeren giriş noktası olmadan bir konsol uygulamasında sağlanabilmesi. Konsol uygulaması yayımlama, çalışma zamanı Mağazası'ndan kullanılabilen bir barındırma başlangıç bütünleştirilmiş kod üretir.

Bir konsol uygulaması giriş noktası olmayan, çünkü bu işlemde kullanılır:

* Bağımlılıkları dosya barındırma başlangıç derlemedeki barındırma için başlangıç kullanmak için gereklidir. Kitaplık değil bir uygulama yayımlama tarafından üretilen bir çalıştırılabilir uygulama varlık bağımlılıkları dosyasıdır.
* Bir kitaplık, paylaşılan çalışma zamanını hedefleyen bir çalıştırılabilir proje gerektiren [çalışma zamanı paket deposuna](/dotnet/core/deploying/runtime-store)eklenemez.

Dinamik barındırma başlangıç oluşturulmasını içinde:

* Bir giriş noktası olmadan bir barındırma başlangıç derleme konsol uygulamasından oluşturulur:
  * `IHostingStartup` uygulamasını içeren bir sınıf içerir.
  * `IHostingStartup` uygulama sınıfını tanımlamak için bir [Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği içerir.
* Konsol uygulaması barındırma startup şirketinizin bağımlılıkları almak için yayımlanır. Kullanılmayan bağımlılıkları bağımlılıkları dosyasından atılır konsol uygulaması yayımlama bir sonuç olur.
* Bağımlılıkları dosyası, çalışma zamanı barındırma başlangıç derleme konumunu ayarlamak için değiştirilir.
* Barındırma başlangıç derleme ve bağımlılıkları dosyası çalışma zamanı paketi deposuna yerleştirilir. Barındırma başlangıç derleme ve bağımlılıkları dosyasını bulmak için bir ortam değişkenlerini çift içinde listelendikleri.

Konsol uygulaması [Microsoft. AspNetCore. Hosting. soyutlamalar](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) paketine başvurur:

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, bir sınıfı <xref:Microsoft.AspNetCore.Hosting.IWebHost>oluştururken yükleme ve yürütme için `IHostingStartup` bir uygulama olarak tanımlar. Aşağıdaki örnekte, ad alanı `StartupEnhancement`ve sınıf `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Bir sınıf `IHostingStartup`uygular. Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir uygulamaya iyileştirmeler eklemek için bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> kullanır. barındırma başlangıç derlemesinde `IHostingStartup.Configure`, Kullanıcı kodundaki `Startup.Configure` önce çalışma zamanı tarafından çağrılır, bu da kullanıcı kodunun barındırma başlangıç derlemesi tarafından verilen yapılandırmanın üzerine yazılmasına izin verir.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Bir `IHostingStartup` projesi oluştururken, bağımlılıklar dosyası ( *. Deps. JSON*) derlemenin `runtime` konumunu *bin* klasörüne ayarlar:

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Dosyanın yalnızca bir parçası olarak gösterilir. Örnekteki derleme adı `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Barındırma başlatma tarafından belirtilen yapılandırma

Yapılandırma işlemi, barındırma başlatmasının yapılandırmasının öncelikli olmasını mı yoksa uygulamanın yapılandırmasının öncelikli olmasını mı istediğinize bağlı olarak iki yaklaşımdan yararlanabilir:

1. Uygulamanın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> temsilcileri çalıştırıldıktan sonra yapılandırmayı yüklemek için <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> kullanarak uygulamaya yapılandırma sağlayın. Barındırma başlangıç yapılandırması, uygulamanın yapılandırmasına bu yaklaşımı kullanarak öncelik kazanır.
1. Uygulamanın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> temsilcileri yürütmeden önce yapılandırmayı yüklemek için <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> kullanarak uygulamaya yapılandırma sağlayın. Uygulamanın yapılandırma değerleri, bu yaklaşımı kullanarak barındırma başlatma tarafından sağlananlara göre önceliklidir.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Barındırma başlangıç derlemeyi belirtin

Bir sınıf kitaplığı ya da konsol uygulaması tarafından sağlanan bir barındırma başlatması için, `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkeninde barındırma başlangıç derlemesinin adını belirtin. Ortam değişkenini derlemelerin noktalı virgülle ayrılmış bir listedir.

`HostingStartup` özniteliği için yalnızca barındırma başlangıç derlemeleri taranır. Daha önce açıklanan barındırma başlangıç pencerelerini öğrenmek için *Hostingstartupapp*örnek uygulaması için, ortam değişkeni aşağıdaki değere ayarlanır:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Barındırma başlangıç bütünleştirilmiş kodları, barındırma başlangıç derlemeleri ana bilgisayar yapılandırma ayarı kullanılarak da ayarlanabilir:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

Birden çok barındırma başlatması varsa, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemleri derlemelerin listelendiği sırada yürütülür.

## <a name="activation"></a>Etkinleştirme

Başlangıç etkinleştirme barındırmak için Seçenekler şunlardır:

* [Çalışma zamanı deposu](#runtime-store) &ndash; etkinleştirme, etkinleştirme için derleme zamanı başvurusu gerektirmez. Örnek uygulama, çok makineli bir ortamda barındırma başlatmasının dağıtımını kolaylaştırmak için barındırma başlangıç derlemesini ve bağımlılıklar dosyalarını bir klasöre, *dağıtımına*koyar. *Dağıtım* klasörü Ayrıca, barındırma başlangıcını etkinleştirmek için dağıtım sistemindeki ortam değişkenlerini oluşturan veya değiştiren bir PowerShell betiği içerir.
* Etkinleştirme için gerekli derleme zamanı başvurusu
  * [NuGet paketi](#nuget-package)
  * [Proje bin klasörü](#project-bin-folder)

### <a name="runtime-store"></a>Çalışma zamanı deposu

Barındırma başlangıç uygulamasının [çalışma zamanı deposuna](/dotnet/core/deploying/runtime-store)yerleştirilmesi. Gelişmiş uygulama tarafından bir derleme zamanı başvurusu derleme için gerekli değildir.

Barındırma başlatma oluşturulduktan sonra, bildirim proje dosyası ve [DotNet Store](/dotnet/core/tools/dotnet-store) komutu kullanılarak bir çalışma zamanı deposu oluşturulur.

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

Örnek uygulamada (*Runtimesstore* Projesi) aşağıdaki komut kullanılır:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Çalışma zamanının çalışma zamanı deposunu bulması için, çalışma zamanı deposunun konumu `DOTNET_SHARED_STORE` ortam değişkenine eklenir.

**Barındırma başlatmasının bağımlılıklar dosyasını değiştirme ve yerleştirme**

Geliştirmede bir paket başvurusu olmadan geliştirmeyi etkinleştirmek için, `additionalDeps`çalışma zamanına ek bağımlılıklar belirtin. `additionalDeps` şunları yapmanıza olanak sağlar:

* Başlangıçta uygulamanın kendi *. Deps* . JSON dosyasıyla birleştirilecek bir dizi ek *. Deps. JSON* dosyası sağlayarak uygulamanın kitaplık grafiğini genişletin.
* Barındırma başlangıç derlemesine bulunabilir ve yüklenebilir olun.

Ek Bağımlılıklar dosyası oluşturmak için önerilen yaklaşımdır:

 1. Önceki bölümde başvurulan çalışma zamanı deposu bildirim dosyasında `dotnet publish` yürütün.
 1. Bildirimlerin bildirim başvurusunu ve sonuçta elde edilen *. Deps. JSON* dosyasının `runtime` bölümünü kaldırın.

Örnek projede `store.manifest/1.0.0` özelliği `targets` ve `libraries` bölümünden kaldırılır:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

*. Deps. JSON* dosyasını şu konuma yerleştirin:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `DOTNET_ADDITIONAL_DEPS` ortam değişkenine eklenen `{ADDITIONAL DEPENDENCIES PATH}` &ndash; konumu.
* Bu ek bağımlılıklar dosyası için gerekli `{SHARED FRAMEWORK NAME}` &ndash; paylaşılan çerçeve.
* `{SHARED FRAMEWORK VERSION}` &ndash; en düşük paylaşılan Framework sürümü.
* geliştirmesinin derleme adı &ndash; `{ENHANCEMENT ASSEMBLY NAME}`.

Örnek uygulamada (*Runtimesbir* proje), ek bağımlılıklar dosyası aşağıdaki konuma yerleştirilir:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

Çalışma zamanı depo konumunu bulması için, ek bağımlılıklar dosya konumu `DOTNET_ADDITIONAL_DEPS` ortam değişkenine eklenir.

Örnek uygulamada (*Runtimesbir* proje), çalışma zamanı deposunun oluşturulması ve ek bağımlılıklar dosyası oluşturulması bir [PowerShell](/powershell/scripting/powershell-scripting) betiği kullanılarak gerçekleştirilir.

Çeşitli işletim sistemleri için ortam değişkenlerinin nasıl ayarlanbileceğine ilişkin örnekler için, bkz. [birden çok ortam kullanma](xref:fundamentals/environments).

**Dağıtım**

Çoklu makine ortamında bir barındırma başlatmasının dağıtımını kolaylaştırmak için, örnek uygulama, yayımlanan çıktıda şunları içeren bir *dağıtım* klasörü oluşturur:

* Barındırma başlangıç çalışma zamanı deposu.
* Barındırma başlangıç bağımlılıkları dosyası.
* Barındırma başlangıcını etkinleştirmeyi desteklemek için `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`ve `DOTNET_ADDITIONAL_DEPS` oluşturan veya değiştiren bir PowerShell betiği. Komut dosyası dağıtım sistemde bir yönetici PowerShell komut isteminden çalıştırın.

### <a name="nuget-package"></a>NuGet paketi

Bir NuGet paketi barındırma bir başlangıç geliştirmesi sağlanabilir. Pakette bir `HostingStartup` özniteliği vardır. Paket tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan birini kullanarak uygulama için kullanıma sunulur:

* Gelişmiş uygulamanın proje dosyası, barındırma başlangıç için bir paket başvurusu uygulamanın proje dosyası (bir derleme zamanı Başvurusu) sağlar. Derleme zamanı başvurusu yerinde olduğunda, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına ( *. Deps. JSON*) dahil edilir. Bu yaklaşım, [NuGet.org](https://www.nuget.org/)'e yayınlanan bir barındırma başlangıç derleme paketi için geçerlidir.
* Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).

NuGet paketlerini ve çalışma zamanı mağazası hakkında daha fazla bilgi için aşağıdaki konulara bakın:

* [Platformlar arası araçlarla bir NuGet paketi oluşturma](/dotnet/core/deploying/creating-nuget-packages)
* [Paketler yayımlanıyor](/nuget/create-packages/publish-a-package)
* [Çalışma zamanı paket deposu](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Proje bin klasörü

Bir barındırma başlatma geliştirmesi, gelişmiş uygulamada, *bin*ile dağıtılan bir derleme tarafından sağlanıyor. Derleme tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:

* Gelişmiş uygulamanın proje dosyası, barındırma başlangıç (bir derleme zamanı başvuru) bir bütünleştirilmiş kod başvurusu yapar. Derleme zamanı başvurusu yerinde olduğunda, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına ( *. Deps. JSON*) dahil edilir. Bu yaklaşım, dağıtım senaryosu barındırma başlatmasının derlemesine ( *. dll* dosyası) bir derleme zamanı başvurusu yapmak ve derlemeyi şu şekilde taşımak için çağırdığında geçerlidir:
  * Tüketen proje.
  * Tüketim Projesi tarafından erişilebilen bir konum.
* Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).
* .NET Framework hedeflenirken, derleme varsayılan yükleme bağlamında yüklenebilir olur; bu, .NET Framework, derlemenin aşağıdaki konumlardan birinde bulunduğu anlamına gelir:
  * Uygulama temel yolu, uygulamanın yürütülebilir dosyasının ( *. exe*) bulunduğu *bin* klasörünü &ndash;.
  * Genel bütünleştirilmiş kod önbelleği (GAC) &ndash; GAC, birkaç .NET Framework uygulamanın paylaştığı derlemeleri depolar. Daha fazla bilgi için, bkz. [nasıl yapılır: bir derlemeyi genel derleme önbelleğine yüklemek](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) .NET Framework belgeleri.

## <a name="sample-code"></a>Örnek kod

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample)), başlangıç uygulama senaryolarını barındırma gösterir:

* İki barındırma başlangıç derlemeleri (sınıf kitaplıkları), bellek içi yapılandırma anahtar-değer çiftleri her bir çifti ayarlayın:
  * NuGet paketi (*Hostingstartuppackage*)
  * Sınıf kitaplığı (*Hostingstartuplibrary*)
* Bir barındırma başlatması, çalışma zamanı deposu tarafından dağıtılan bir derlemeden (*Startupdiagnostics*) etkinleştirilir. Derleme, uygulamaya tanılama bilgileri sağlayan başlangıçta iki middlewares ekler:
  * Kaydedilen Hizmetleri
  * Adres (Düzen, konak, temel yolu, yol, sorgu dizesi)
  * Bağlantı (uzak IP, uzak bağlantı noktasını, yerel IP yerel bağlantı noktası, istemci sertifikası)
  * İstek üst bilgileri
  * Ortam değişkenleri

Örneği çalıştırmak için:

**NuGet paketinden etkinleştirme**

1. ,, [DotNet paketi](/dotnet/core/tools/dotnet-pack) komutuyla *hostingstartuppackage* paketini derleyin.
1. *Hostingstartuppackage* 'in derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.
1. Derleme ve uygulamayı çalıştırın. Geliştirilmiş bir uygulamada (bir derleme zamanı Başvurusu) bir paket başvurusu yok. Uygulamanın proje dosyasındaki bir `<PropertyGroup>` paket projesinin çıkışını belirtir ( *.. /HostingStartupPackage/bin/Debug*) bir paket kaynağı olarak. Bu, uygulamanın paketi [NuGet.org](https://www.nuget.org/)'e yüklemeden paketi kullanmasına izin verir. Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, paketin `ServiceKeyInjection.Configure` yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin.

*Hostingstartuppackage* projesinde değişiklik yaparsanız ve yeniden derleyseniz, *Hostingstartupapp* ' ın yerel önbellekten eski bir paket değil, güncelleştirilmiş paketi aldığından emin olmak için yerel NuGet paket önbelleklerini temizleyin. Yerel NuGet önbelleklerini temizlemek için aşağıdaki [DotNet NuGet Yereller](/dotnet/core/tools/dotnet-nuget-locals) komutunu yürütün:

```dotnetcli
dotnet nuget locals all --clear
```

**Bir sınıf kitaplığından etkinleştirme**

1. , [DotNet Build](/dotnet/core/tools/dotnet-build) komutuyla *hostingstartuplibrary* sınıf kitaplığını derleyin.
1. *Hostingstartuplibrary* 'in sınıf kitaplığının derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.
1. *bin*- *hostingstartuplibrary. dll* dosyasını sınıf kitaplığının derlenmiş çıktısından uygulamanın *bin/Debug* klasörüne kopyalayarak uygulamaya sınıf kitaplığının derlemesini dağıtın.
1. Derleme ve uygulamayı çalıştırın. Uygulamanın proje dosyasındaki bir `<ItemGroup>`, sınıf kitaplığının derlemesine ( *.\Bin\debug\netcoreapp3,\hostingstartuplibrary.dll*) başvurur (bir derleme zamanı başvurusu). Notları HostingStartupApp'ın proje dosyasında daha fazla bilgi için bkz.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, sınıf kitaplığının `ServiceKeyInjection.Configure` yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin.

**Çalışma zamanı deposu tarafından dağıtılan bir derlemeden etkinleştirme**

1. *Startupdiagnostics* projesi, *startupdiagnostics. Deps. JSON* dosyasını değiştirmek için [PowerShell](/powershell/scripting/powershell-scripting) kullanır. PowerShell, Windows 7 SP1 ve Windows Server 2008 R2 SP1 ile başlayarak Windows üzerinde varsayılan olarak yüklenir. Diğer platformlarda PowerShell 'i almak için bkz. [Windows PowerShell 'ı yükleme](/powershell/scripting/setup/installing-powershell#powershell-core).
1. *Runtimesyürüme* klasöründe *Build. ps1* betiğini yürütün. Betik:
   * `StartupDiagnostics` paketini *obj\packages* klasöründe oluşturur.
   * *Mağaza* klasöründeki `StartupDiagnostics` çalışma zamanı deposunu oluşturur. Betikteki `dotnet store` komutu, Windows 'a dağıtılan bir barındırma başlatması için `win7-x64` [çalışma zamanı tanımlayıcısı 'nı (RID)](/dotnet/core/rid-catalog) kullanır. Farklı bir çalışma zamanı için barındırma başlangıcını sağlarken, betiğin 37. satırındaki doğru RID 'yi yerine koyun. `StartupDiagnostics` çalışma zamanı deposu daha sonra derlemenin tüketilebileceği makinede kullanıcının veya sistem çalışma zamanı deposuna taşınır. `StartupDiagnostics` derlemesinin Kullanıcı çalışma zamanı deposu yüklemesi konumu *. DotNet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/LIB/netcoreapp 3.0/startupdiagnostics. dll*' dir.
   * *Additionaldeps* klasöründeki `StartupDiagnostics` için `additionalDeps` üretir. Ek bağımlılıklar daha sonra kullanıcının veya sistem ek bağımlılıklarına taşınır. Kullanıcı `StartupDiagnostics` ek bağımlılıklar yüklemesi konumu *. DotNet/x64/additionalDeps/startupdiagnostics/Shared/Microsoft. NETCore. app/3.0.0/StartupDiagnostics. Deps. JSON*olur.
   * *Dağıtım* klasörüne *Deploy. ps1* dosyasını koyar.
1. *Dağıtım* klasöründe *Deploy. ps1* betiğini çalıştırın. Betik şunu ekler:
   * `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine `StartupDiagnostics`.
   * Barındırma başlangıç bağımlılıkları yolu (Runtimessımında projenin *dağıtım* klasöründe) `DOTNET_ADDITIONAL_DEPS` ortam değişkenine.
   * Çalışma zamanı depolama yolu (Runtimes, projenin *dağıtım* klasöründe) `DOTNET_SHARED_STORE` ortam değişkenine.
1. Örnek uygulamayı çalıştırın.
1. Uygulamanın kayıtlı hizmetlerini görmek için `/services` uç noktası isteyin. Tanılama bilgilerini görmek için `/diag` uç noktası isteyin.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (barındırma başlatma) uygulaması, bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler ekler. Örneğin, bir harici kitaplık ek yapılandırma sağlayıcıları ya da bir uygulama hizmetlerini barındıran bir başlangıç uygulaması kullanabilirsiniz.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>HostingStartup özniteliği

[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, çalışma zamanında etkinleştirilecek bir barındırma başlangıç derlemesinin varlığını gösterir.

Giriş derlemesi veya `Startup` sınıfını içeren derleme `HostingStartup` özniteliği için otomatik olarak taranır. `HostingStartup` özniteliklerin aranacağı derlemelerin listesi [Webhostdefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)içindeki yapılandırmadan çalışma zamanında yüklenir. Bulmadan dışlanacak derlemelerin listesi [Webhostdefaults. Hostingstartupexcludederlemelieskey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)öğesinden yüklendi. Daha fazla bilgi için bkz. [Web Konağı: barındırma başlangıç derlemeleri](xref:fundamentals/host/web-host#hosting-startup-assemblies) ve [Web Konağı: barındırma başlatma derlemeleri çıkarma](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).

Aşağıdaki örnekte, barındırma başlangıç derlemesinin ad alanı `StartupEnhancement`. Barındırma başlangıç kodunu içeren sınıf `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

`HostingStartup` özniteliği genellikle barındırma başlangıç derlemesinin `IHostingStartup` uygulama sınıfı dosyasında bulunur.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Yüklenen barındırma başlangıç derlemeler keşfedin

Yüklenen barındırma başlangıç derlemeleri bulmak için günlük kaydını etkinleştirmek ve uygulama günlüklerini kontrol edin. Derlemeler yüklenirken oluşan hataları günlüğe kaydedilir. Yüklenen barındırma başlangıç derlemeler hata ayıklama düzeyinde kaydedilir ve tüm hatalar kaydedilir.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Başlangıç derlemeleri barındırma otomatik yüklemeyi devre dışı

Başlangıç derlemeleri barındırma otomatik yüklemeyi devre dışı bırakmak için aşağıdaki yaklaşımlardan birini kullanın:

* Tüm barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini `true` veya `1`olarak ayarlayın:
  * [Barındırma başlangıç](xref:fundamentals/host/web-host#prevent-hosting-startup) ana bilgisayar yapılandırma ayarını önleyin.
  * ortam değişkeni `ASPNETCORE_PREVENTHOSTINGSTARTUP`.
* Belirli barındırma başlangıç derlemeleri yüklenmesini önlemek için aşağıdakilerden birini başlangıçta hariç tutmak için başlangıç derlemeleri barındırma noktalı virgülle ayrılmış bir dizeye ayarlayın:
  * [Barındırma başlatma derlemeleri](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) ana bilgisayar yapılandırma ayarı.
  * ortam değişkeni `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.

Hem ana bilgisayar yapılandırma ayarı ve ortam değişkenini ayarlarsanız, ana bilgisayar ayarını davranışını denetler.

Ana bilgisayar ayarı veya ortam değişkenini kullanarak barındırma başlangıç derlemeleri devre dışı bırakma, derlemenin genel olarak devre dışı bırakır ve uygulama çeşitli özelliklerini devre dışı bırakabilir.

## <a name="project"></a>Project

Bir barındırma başlatma aşağıdaki proje türlerini birini oluşturun:

* [Sınıf kitaplığı](#class-library)
* [Giriş noktası olmayan konsol uygulaması](#console-app-without-an-entry-point)

### <a name="class-library"></a>Sınıf kitaplığı

Bir barındırma başlangıç geliştirme'de sınıf kitaplığının sağlanabilir. Kitaplık bir `HostingStartup` özniteliği içerir.

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) bir Razor Pages uygulaması, *Hostingstartupapp*ve bir sınıf kitaplığı, *hostingstartuplibrary*içerir. Sınıf kitaplığı:

* `IHostingStartup`uygulayan `ServiceKeyInjection`bir barındırma başlangıç sınıfı içerir. `ServiceKeyInjection`, bellek içi yapılandırma sağlayıcısını ([Addınmemorycollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) kullanarak uygulama yapılandırmasına bir hizmet dizesi çifti ekler.
* , Barındırma başlatmasının ad alanını ve sınıfını tanımlayan bir `HostingStartup` özniteliği içerir.

`ServiceKeyInjection` sınıfının <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir uygulamaya geliştirmeler eklemek için bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> kullanır.

*Hostingstartuplibrary/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Uygulama dizin sayfasına okur ve Sınıf Kitaplığı'nızın barındırma başlangıç derlemesi tarafından ayarlanmış iki anahtarı için yapılandırma değerlerini işler:

*Hostingstartupapp/Pages/Index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ayrıca ayrı bir barındırma başlatma, *Hostingstartuppackage*sağlayan bir NuGet paket projesi içerir. Paket, daha önce açıklanan Sınıf Kitaplığı'nın aynı özelliklere sahiptir. Paketi:

* `IHostingStartup`uygulayan `ServiceKeyInjection`bir barındırma başlangıç sınıfı içerir. `ServiceKeyInjection`, uygulamanın yapılandırmasına bir çift hizmet dizesi ekler.
* Bir `HostingStartup` özniteliği içerir.

*Hostingstartuppackage/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Uygulama dizin sayfasına okur ve paketin barındırma başlangıç derlemesi tarafından ayarlanmış iki anahtarı için yapılandırma değerlerini işler:

*Hostingstartupapp/Pages/Index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Konsol uygulaması giriş noktası olmayan

*Bu yaklaşım, .NET Framework değil yalnızca .NET Core uygulamaları için kullanılabilir.*

Etkinleştirme için derleme zamanı başvurusu gerektirmeyen dinamik barındırma başlatma geliştirmesi, bir `HostingStartup` özniteliği içeren giriş noktası olmadan bir konsol uygulamasında sağlanabilmesi. Konsol uygulaması yayımlama, çalışma zamanı Mağazası'ndan kullanılabilen bir barındırma başlangıç bütünleştirilmiş kod üretir.

Bir konsol uygulaması giriş noktası olmayan, çünkü bu işlemde kullanılır:

* Bağımlılıkları dosya barındırma başlangıç derlemedeki barındırma için başlangıç kullanmak için gereklidir. Kitaplık değil bir uygulama yayımlama tarafından üretilen bir çalıştırılabilir uygulama varlık bağımlılıkları dosyasıdır.
* Bir kitaplık, paylaşılan çalışma zamanını hedefleyen bir çalıştırılabilir proje gerektiren [çalışma zamanı paket deposuna](/dotnet/core/deploying/runtime-store)eklenemez.

Dinamik barındırma başlangıç oluşturulmasını içinde:

* Bir giriş noktası olmadan bir barındırma başlangıç derleme konsol uygulamasından oluşturulur:
  * `IHostingStartup` uygulamasını içeren bir sınıf içerir.
  * `IHostingStartup` uygulama sınıfını tanımlamak için bir [Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği içerir.
* Konsol uygulaması barındırma startup şirketinizin bağımlılıkları almak için yayımlanır. Kullanılmayan bağımlılıkları bağımlılıkları dosyasından atılır konsol uygulaması yayımlama bir sonuç olur.
* Bağımlılıkları dosyası, çalışma zamanı barındırma başlangıç derleme konumunu ayarlamak için değiştirilir.
* Barındırma başlangıç derleme ve bağımlılıkları dosyası çalışma zamanı paketi deposuna yerleştirilir. Barındırma başlangıç derleme ve bağımlılıkları dosyasını bulmak için bir ortam değişkenlerini çift içinde listelendikleri.

Konsol uygulaması [Microsoft. AspNetCore. Hosting. soyutlamalar](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) paketine başvurur:

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, bir sınıfı <xref:Microsoft.AspNetCore.Hosting.IWebHost>oluştururken yükleme ve yürütme için `IHostingStartup` bir uygulama olarak tanımlar. Aşağıdaki örnekte, ad alanı `StartupEnhancement`ve sınıf `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Bir sınıf `IHostingStartup`uygular. Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir uygulamaya iyileştirmeler eklemek için bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> kullanır. barındırma başlangıç derlemesinde `IHostingStartup.Configure`, Kullanıcı kodundaki `Startup.Configure` önce çalışma zamanı tarafından çağrılır, bu da kullanıcı kodunun barındırma başlangıç derlemesi tarafından verilen yapılandırmanın üzerine yazılmasına izin verir.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Bir `IHostingStartup` projesi oluştururken, bağımlılıklar dosyası ( *. Deps. JSON*) derlemenin `runtime` konumunu *bin* klasörüne ayarlar:

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Dosyanın yalnızca bir parçası olarak gösterilir. Örnekteki derleme adı `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Barındırma başlatma tarafından belirtilen yapılandırma

Yapılandırma işlemi, barındırma başlatmasının yapılandırmasının öncelikli olmasını mı yoksa uygulamanın yapılandırmasının öncelikli olmasını mı istediğinize bağlı olarak iki yaklaşımdan yararlanabilir:

1. Uygulamanın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> temsilcileri çalıştırıldıktan sonra yapılandırmayı yüklemek için <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> kullanarak uygulamaya yapılandırma sağlayın. Barındırma başlangıç yapılandırması, uygulamanın yapılandırmasına bu yaklaşımı kullanarak öncelik kazanır.
1. Uygulamanın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> temsilcileri yürütmeden önce yapılandırmayı yüklemek için <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> kullanarak uygulamaya yapılandırma sağlayın. Uygulamanın yapılandırma değerleri, bu yaklaşımı kullanarak barındırma başlatma tarafından sağlananlara göre önceliklidir.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Barındırma başlangıç derlemeyi belirtin

Bir sınıf kitaplığı ya da konsol uygulaması tarafından sağlanan bir barındırma başlatması için, `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkeninde barındırma başlangıç derlemesinin adını belirtin. Ortam değişkenini derlemelerin noktalı virgülle ayrılmış bir listedir.

`HostingStartup` özniteliği için yalnızca barındırma başlangıç derlemeleri taranır. Daha önce açıklanan barındırma başlangıç pencerelerini öğrenmek için *Hostingstartupapp*örnek uygulaması için, ortam değişkeni aşağıdaki değere ayarlanır:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Barındırma başlangıç bütünleştirilmiş [kodları barındırma başlangıç derlemeleri](xref:fundamentals/host/web-host#hosting-startup-assemblies) ana bilgisayar yapılandırma ayarı kullanılarak da ayarlanabilir.

Birden çok barındırma başlatması varsa, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemleri derlemelerin listelendiği sırada yürütülür.

## <a name="activation"></a>Etkinleştirme

Başlangıç etkinleştirme barındırmak için Seçenekler şunlardır:

* [Çalışma zamanı deposu](#runtime-store) &ndash; etkinleştirme, etkinleştirme için derleme zamanı başvurusu gerektirmez. Örnek uygulama, çok makineli bir ortamda barındırma başlatmasının dağıtımını kolaylaştırmak için barındırma başlangıç derlemesini ve bağımlılıklar dosyalarını bir klasöre, *dağıtımına*koyar. *Dağıtım* klasörü Ayrıca, barındırma başlangıcını etkinleştirmek için dağıtım sistemindeki ortam değişkenlerini oluşturan veya değiştiren bir PowerShell betiği içerir.
* Etkinleştirme için gerekli derleme zamanı başvurusu
  * [NuGet paketi](#nuget-package)
  * [Proje bin klasörü](#project-bin-folder)

### <a name="runtime-store"></a>Çalışma zamanı deposu

Barındırma başlangıç uygulamasının [çalışma zamanı deposuna](/dotnet/core/deploying/runtime-store)yerleştirilmesi. Gelişmiş uygulama tarafından bir derleme zamanı başvurusu derleme için gerekli değildir.

Barındırma başlatma oluşturulduktan sonra, bildirim proje dosyası ve [DotNet Store](/dotnet/core/tools/dotnet-store) komutu kullanılarak bir çalışma zamanı deposu oluşturulur.

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

Örnek uygulamada (*Runtimesstore* Projesi) aşağıdaki komut kullanılır:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Çalışma zamanının çalışma zamanı deposunu bulması için, çalışma zamanı deposunun konumu `DOTNET_SHARED_STORE` ortam değişkenine eklenir.

**Barındırma başlatmasının bağımlılıklar dosyasını değiştirme ve yerleştirme**

Geliştirmede bir paket başvurusu olmadan geliştirmeyi etkinleştirmek için, `additionalDeps`çalışma zamanına ek bağımlılıklar belirtin. `additionalDeps` şunları yapmanıza olanak sağlar:

* Başlangıçta uygulamanın kendi *. Deps* . JSON dosyasıyla birleştirilecek bir dizi ek *. Deps. JSON* dosyası sağlayarak uygulamanın kitaplık grafiğini genişletin.
* Barındırma başlangıç derlemesine bulunabilir ve yüklenebilir olun.

Ek Bağımlılıklar dosyası oluşturmak için önerilen yaklaşımdır:

 1. Önceki bölümde başvurulan çalışma zamanı deposu bildirim dosyasında `dotnet publish` yürütün.
 1. Bildirimlerin bildirim başvurusunu ve sonuçta elde edilen *. Deps. JSON* dosyasının `runtime` bölümünü kaldırın.

Örnek projede `store.manifest/1.0.0` özelliği `targets` ve `libraries` bölümünden kaldırılır:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

*. Deps. JSON* dosyasını şu konuma yerleştirin:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `DOTNET_ADDITIONAL_DEPS` ortam değişkenine eklenen `{ADDITIONAL DEPENDENCIES PATH}` &ndash; konumu.
* Bu ek bağımlılıklar dosyası için gerekli `{SHARED FRAMEWORK NAME}` &ndash; paylaşılan çerçeve.
* `{SHARED FRAMEWORK VERSION}` &ndash; en düşük paylaşılan Framework sürümü.
* geliştirmesinin derleme adı &ndash; `{ENHANCEMENT ASSEMBLY NAME}`.

Örnek uygulamada (*Runtimesbir* proje), ek bağımlılıklar dosyası aşağıdaki konuma yerleştirilir:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

Çalışma zamanı depo konumunu bulması için, ek bağımlılıklar dosya konumu `DOTNET_ADDITIONAL_DEPS` ortam değişkenine eklenir.

Örnek uygulamada (*Runtimesbir* proje), çalışma zamanı deposunun oluşturulması ve ek bağımlılıklar dosyası oluşturulması bir [PowerShell](/powershell/scripting/powershell-scripting) betiği kullanılarak gerçekleştirilir.

Çeşitli işletim sistemleri için ortam değişkenlerinin nasıl ayarlanbileceğine ilişkin örnekler için, bkz. [birden çok ortam kullanma](xref:fundamentals/environments).

**Dağıtım**

Çoklu makine ortamında bir barındırma başlatmasının dağıtımını kolaylaştırmak için, örnek uygulama, yayımlanan çıktıda şunları içeren bir *dağıtım* klasörü oluşturur:

* Barındırma başlangıç çalışma zamanı deposu.
* Barındırma başlangıç bağımlılıkları dosyası.
* Barındırma başlangıcını etkinleştirmeyi desteklemek için `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`ve `DOTNET_ADDITIONAL_DEPS` oluşturan veya değiştiren bir PowerShell betiği. Komut dosyası dağıtım sistemde bir yönetici PowerShell komut isteminden çalıştırın.

### <a name="nuget-package"></a>NuGet paketi

Bir NuGet paketi barındırma bir başlangıç geliştirmesi sağlanabilir. Pakette bir `HostingStartup` özniteliği vardır. Paket tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan birini kullanarak uygulama için kullanıma sunulur:

* Gelişmiş uygulamanın proje dosyası, barındırma başlangıç için bir paket başvurusu uygulamanın proje dosyası (bir derleme zamanı Başvurusu) sağlar. Derleme zamanı başvurusu yerinde olduğunda, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına ( *. Deps. JSON*) dahil edilir. Bu yaklaşım, [NuGet.org](https://www.nuget.org/)'e yayınlanan bir barındırma başlangıç derleme paketi için geçerlidir.
* Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).

NuGet paketlerini ve çalışma zamanı mağazası hakkında daha fazla bilgi için aşağıdaki konulara bakın:

* [Platformlar arası araçlarla bir NuGet paketi oluşturma](/dotnet/core/deploying/creating-nuget-packages)
* [Paketler yayımlanıyor](/nuget/create-packages/publish-a-package)
* [Çalışma zamanı paket deposu](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Proje bin klasörü

Bir barındırma başlatma geliştirmesi, gelişmiş uygulamada, *bin*ile dağıtılan bir derleme tarafından sağlanıyor. Derleme tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:

* Gelişmiş uygulamanın proje dosyası, barındırma başlangıç (bir derleme zamanı başvuru) bir bütünleştirilmiş kod başvurusu yapar. Derleme zamanı başvurusu yerinde olduğunda, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına ( *. Deps. JSON*) dahil edilir. Bu yaklaşım, dağıtım senaryosu barındırma başlatmasının derlemesine ( *. dll* dosyası) bir derleme zamanı başvurusu yapmak ve derlemeyi şu şekilde taşımak için çağırdığında geçerlidir:
  * Tüketen proje.
  * Tüketim Projesi tarafından erişilebilen bir konum.
* Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).
* .NET Framework hedeflenirken, derleme varsayılan yükleme bağlamında yüklenebilir olur; bu, .NET Framework, derlemenin aşağıdaki konumlardan birinde bulunduğu anlamına gelir:
  * Uygulama temel yolu, uygulamanın yürütülebilir dosyasının ( *. exe*) bulunduğu *bin* klasörünü &ndash;.
  * Genel bütünleştirilmiş kod önbelleği (GAC) &ndash; GAC, birkaç .NET Framework uygulamanın paylaştığı derlemeleri depolar. Daha fazla bilgi için, bkz. [nasıl yapılır: bir derlemeyi genel derleme önbelleğine yüklemek](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) .NET Framework belgeleri.

## <a name="sample-code"></a>Örnek kod

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample)), başlangıç uygulama senaryolarını barındırma gösterir:

* İki barındırma başlangıç derlemeleri (sınıf kitaplıkları), bellek içi yapılandırma anahtar-değer çiftleri her bir çifti ayarlayın:
  * NuGet paketi (*Hostingstartuppackage*)
  * Sınıf kitaplığı (*Hostingstartuplibrary*)
* Bir barındırma başlatması, çalışma zamanı deposu tarafından dağıtılan bir derlemeden (*Startupdiagnostics*) etkinleştirilir. Derleme, uygulamaya tanılama bilgileri sağlayan başlangıçta iki middlewares ekler:
  * Kaydedilen Hizmetleri
  * Adres (Düzen, konak, temel yolu, yol, sorgu dizesi)
  * Bağlantı (uzak IP, uzak bağlantı noktasını, yerel IP yerel bağlantı noktası, istemci sertifikası)
  * İstek üst bilgileri
  * Ortam değişkenleri

Örneği çalıştırmak için:

**NuGet paketinden etkinleştirme**

1. ,, [DotNet paketi](/dotnet/core/tools/dotnet-pack) komutuyla *hostingstartuppackage* paketini derleyin.
1. *Hostingstartuppackage* 'in derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.
1. Derleme ve uygulamayı çalıştırın. Geliştirilmiş bir uygulamada (bir derleme zamanı Başvurusu) bir paket başvurusu yok. Uygulamanın proje dosyasındaki bir `<PropertyGroup>` paket projesinin çıkışını belirtir ( *.. /HostingStartupPackage/bin/Debug*) bir paket kaynağı olarak. Bu, uygulamanın paketi [NuGet.org](https://www.nuget.org/)'e yüklemeden paketi kullanmasına izin verir. Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, paketin `ServiceKeyInjection.Configure` yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin.

*Hostingstartuppackage* projesinde değişiklik yaparsanız ve yeniden derleyseniz, *Hostingstartupapp* ' ın yerel önbellekten eski bir paket değil, güncelleştirilmiş paketi aldığından emin olmak için yerel NuGet paket önbelleklerini temizleyin. Yerel NuGet önbelleklerini temizlemek için aşağıdaki [DotNet NuGet Yereller](/dotnet/core/tools/dotnet-nuget-locals) komutunu yürütün:

```dotnetcli
dotnet nuget locals all --clear
```

**Bir sınıf kitaplığından etkinleştirme**

1. , [DotNet Build](/dotnet/core/tools/dotnet-build) komutuyla *hostingstartuplibrary* sınıf kitaplığını derleyin.
1. *Hostingstartuplibrary* 'in sınıf kitaplığının derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.
1. *bin*- *hostingstartuplibrary. dll* dosyasını sınıf kitaplığının derlenmiş çıktısından uygulamanın *bin/Debug* klasörüne kopyalayarak uygulamaya sınıf kitaplığının derlemesini dağıtın.
1. Derleme ve uygulamayı çalıştırın. Uygulamanın proje dosyasındaki bir `<ItemGroup>`, sınıf kitaplığının derlemesine ( *.\Bin\debug\netcoreapp2,\hostingstartuplibrary.dll*) başvurur (bir derleme zamanı başvurusu). Notları HostingStartupApp'ın proje dosyasında daha fazla bilgi için bkz.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, sınıf kitaplığının `ServiceKeyInjection.Configure` yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin.

**Çalışma zamanı deposu tarafından dağıtılan bir derlemeden etkinleştirme**

1. *Startupdiagnostics* projesi, *startupdiagnostics. Deps. JSON* dosyasını değiştirmek için [PowerShell](/powershell/scripting/powershell-scripting) kullanır. PowerShell, Windows 7 SP1 ve Windows Server 2008 R2 SP1 ile başlayarak Windows üzerinde varsayılan olarak yüklenir. Diğer platformlarda PowerShell 'i almak için bkz. [Windows PowerShell 'ı yükleme](/powershell/scripting/setup/installing-powershell#powershell-core).
1. *Runtimesyürüme* klasöründe *Build. ps1* betiğini yürütün. Betik:
   * `StartupDiagnostics` paketini *obj\packages* klasöründe oluşturur.
   * *Mağaza* klasöründeki `StartupDiagnostics` çalışma zamanı deposunu oluşturur. Betikteki `dotnet store` komutu, Windows 'a dağıtılan bir barındırma başlatması için `win7-x64` [çalışma zamanı tanımlayıcısı 'nı (RID)](/dotnet/core/rid-catalog) kullanır. Farklı bir çalışma zamanı için barındırma başlangıcını sağlarken, betiğin 37. satırındaki doğru RID 'yi yerine koyun. `StartupDiagnostics` çalışma zamanı deposu daha sonra derlemenin tüketilebileceği makinede kullanıcının veya sistem çalışma zamanı deposuna taşınır. `StartupDiagnostics` derlemesinin Kullanıcı çalışma zamanı deposu yüklemesi konumu *. DotNet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/LIB/netcoreapp 2.2/startupdiagnostics. dll*' dir.
   * *Additionaldeps* klasöründeki `StartupDiagnostics` için `additionalDeps` üretir. Ek bağımlılıklar daha sonra kullanıcının veya sistem ek bağımlılıklarına taşınır. Kullanıcı `StartupDiagnostics` ek bağımlılıklar yüklemesi konumu *. DotNet/x64/additionalDeps/startupdiagnostics/Shared/Microsoft. NETCore. App/2.2.0/StartupDiagnostics. Deps. JSON*olur.
   * *Dağıtım* klasörüne *Deploy. ps1* dosyasını koyar.
1. *Dağıtım* klasöründe *Deploy. ps1* betiğini çalıştırın. Betik şunu ekler:
   * `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine `StartupDiagnostics`.
   * Barındırma başlangıç bağımlılıkları yolu (Runtimessımında projenin *dağıtım* klasöründe) `DOTNET_ADDITIONAL_DEPS` ortam değişkenine.
   * Çalışma zamanı depolama yolu (Runtimes, projenin *dağıtım* klasöründe) `DOTNET_SHARED_STORE` ortam değişkenine.
1. Örnek uygulamayı çalıştırın.
1. Uygulamanın kayıtlı hizmetlerini görmek için `/services` uç noktası isteyin. Tanılama bilgilerini görmek için `/diag` uç noktası isteyin.

::: moniker-end
