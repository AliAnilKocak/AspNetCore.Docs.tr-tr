---
title: ASP.NET Core bir Windows hizmetinde barındırma
author: guardrex
description: ASP.NET Core uygulaması bir Windows hizmetinde barındırmayı öğrenin.
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/04/2018
uid: host-and-deploy/windows-service
ms.openlocfilehash: 4fd0cc881eff3b1bbdfdf51e223d0fd42051c31d
ms.sourcegitcommit: 516d0645c35ea784a3ae807be087ae70446a46ee
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39320745"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>ASP.NET Core bir Windows hizmetinde barındırma

Tarafından [Luke Latham](https://github.com/guardrex) ve [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core uygulaması olarak IIS kullanmadan Windows üzerinde barındırılabilen bir [Windows hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications). Bir Windows hizmeti olarak barındırılan uygulamayı otomatik olarak yapabilirsiniz başladıktan sonra bilgisayarı yeniden başlatır ve kullanıcı müdahalesine gerek kalmadan kilitleniyor.

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))

## <a name="get-started"></a>Kullanmaya başlayın

Bir hizmet olarak çalıştırmak için mevcut bir ASP.NET Core projesini ayarlarsınız ayarlamak için aşağıdaki en düşük değişiklikleri gerekir:

1. Proje dosyasında:

   1. Çalışma zamanı tanımlayıcısı varlığını onaylamak veya ekleyin  **\<PropertyGroup >** , hedef Framework'ü içerir:

      ::: moniker range=">= aspnetcore-2.1"

      ```xml
      <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
      </PropertyGroup>
      ```

      ::: moniker-end

      ::: moniker range="= aspnetcore-2.0"

      ```xml
      <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
      </PropertyGroup>
      ```

      ::: moniker-end

      ::: moniker range="< aspnetcore-2.0"

      ```xml
      <PropertyGroup>
        <TargetFramework>netcoreapp1.1</TargetFramework>
        <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
      </PropertyGroup>
      ```

      ::: moniker-end

   1. İçin bir paket başvurusu ekleme [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices/).

1. Aşağıdaki değişiklikleri yapın `Program.Main`:

   * Çağrı [ana bilgisayar. RunAsService](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostwindowsserviceextensions.runasservice) yerine `host.Run`.

   * Çağrı [UseContentRoot](xref:fundamentals/host/web-host#content-root) ve uygulama için bir yol yerine konumu yayımlanan `Directory.GetCurrentDirectory()`.

     ::: moniker range=">= aspnetcore-2.0"

     [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=ServiceOnly&highlight=8-9,12)]

     ::: moniker-end

     ::: moniker range="< aspnetcore-2.0"

     [!code-csharp[](windows-service/samples_snapshot/1.x/AspNetCoreService/Program.cs?name=ServiceOnly&highlight=3-4,8,13)]

     ::: moniker-end

1. Uygulamayı yayımlayın. Kullanım [dotnet yayımlama](/dotnet/articles/core/tools/dotnet-publish) veya [Visual Studio yayımlama profilini](xref:host-and-deploy/visual-studio-publish-profiles).

   Komut satırından örnek uygulamayı yayımlamak için proje klasöründen konsol penceresinde aşağıdaki komutu çalıştırın:

   ```console
   dotnet publish --configuration Release
   ```

1. Kullanım [sc.exe](https://technet.microsoft.com/library/bb490995) hizmeti oluşturmak için komut satırı aracı. `binPath` Değerdir yürütülebilir dosya adını içeren uygulamanın yürütülebilir dosyanın yolu. **Eşittir işareti ve tırnak karakteri yolunun başında arasındaki boşluk gereklidir.**

   ```console
   sc create <SERVICE_NAME> binPath= "<PATH_TO_SERVICE_EXECUTABLE>"
   ```

   Proje klasöründe yayımlanan bir hizmet için yolunu kullanın *yayımlama* hizmet oluşturmak için klasör. Aşağıdaki örnekte, bir hizmettir:

   * Adlı **MyService**.
   * Yayımlanan *c:\\my_services\\AspNetCoreService\\bin\\yayın\\&lt;TARGET_FRAMEWORK&gt;\\Yayımlama* klasör.
   * Adlı bir uygulama tarafından yürütülebilir temsil *AspNetCoreService.exe*.

   Yönetici ayrıcalıklarıyla bir komut kabuğunu açın ve aşağıdaki komutu çalıştırın:

   ```console
   sc create MyService binPath= "c:\my_services\AspNetCoreService\bin\Release\<TARGET_FRAMEWORK>\publish\AspNetCoreService.exe"
   ```
   
   > [!IMPORTANT]
   > Alanı arasında mevcut olduğundan emin olun `binPath=` bağımsız değişkeni ve değeri.
   
   Yayımlama ve farklı bir klasör hizmeti başlatmak için:
   
   1. Kullanım [--çıktı &lt;OUTPUT_DIRECTORY&gt; ](/dotnet/core/tools/dotnet-publish#options) seçeneğini `dotnet publish` komutu.
   1. Hizmetle `sc.exe` çıkış klasör yolunu kullanarak komutu. Sağlanan yol hizmetin yürütülebilir dosya adı dahil `binPath`.

1. Hizmetle başlar `sc start <SERVICE_NAME>` komutu.

   Örnek uygulama hizmeti başlatmak için aşağıdaki komutu kullanın:

   ```console
   sc start MyService
   ```

   Komut hizmeti başlatmak için birkaç saniye sürer.

1. `sc query <SERVICE_NAME>` Komutu, durumu belirlemek için hizmet durumunu denetlemek için kullanılabilir:

   * `START_PENDING`
   * `RUNNING`
   * `STOP_PENDING`
   * `STOPPED`

   Örnek uygulama hizmeti durumunu denetlemek için aşağıdaki komutu kullanın:

   ```console
   sc query MyService
   ```

1. Hizmet olduğunda `RUNNING` durum ve hizmeti bir web uygulaması ise, uygulama, bir yola göz atın (varsayılan olarak, `http://localhost:5000`, hangi yönlendiren `https://localhost:5001` kullanırken [HTTPS yeniden yönlendirmesi ara yazılım](xref:security/enforcing-ssl)).

   Örnek app service için uygulamaya Gözat `http://localhost:5000`.

1. Hizmetle Durdur `sc stop <SERVICE_NAME>` komutu.

   Aşağıdaki komut örnek uygulama hizmetini durdurur:

   ```console
   sc stop MyService
   ```

1. Hizmeti ile bir hizmeti durdurmak için bir kısa bir gecikmeyle kaldırmanız `sc delete <SERVICE_NAME>` komutu.

   Örnek uygulama hizmeti durumunu kontrol edin:

   ```console
   sc query MyService
   ```

   Örnek uygulama hizmeti olduğunda `STOPPED` durum, örnek uygulama hizmeti kaldırmak için aşağıdaki komutu kullanın:

   ```console
   sc delete MyService
   ```

## <a name="provide-a-way-to-run-outside-of-a-service"></a>Dışında bir hizmeti çalıştırmanın bir yolunu sağlar.

Çağıran kod eklemek için her zamanki şekilde bir hizmet dışında çalışırken hata ayıklama ve test etmek daha kolay `RunAsService` yalnızca belirli koşullar altında. Örneğin, bir konsol uygulaması olarak uygulama çalıştırabilirsiniz bir `--console` komut satırı bağımsız değişkeni veya hata ayıklayıcı eklenir:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=ServiceOrConsole)]

ASP.NET Core yapılandırma komut satırı bağımsız değişkenleri için ad-değer çiftleri gerektirdiğinden `--console` anahtar için bağımsız değişkenler geçirilmeden önce kaldırılır [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder).

> [!NOTE]
> `isService` gelen geçirilen değil `Main` içine `CreateWebHostBuilder` çünkü imzası `CreateWebHostBuilder` olmalıdır `CreateWebHostBuilder(string[])` sırayla [tümleştirme testi](xref:test/integration-tests) düzgün çalışması için.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](windows-service/samples_snapshot/1.x/AspNetCoreService/Program.cs?name=ServiceOrConsole)]

::: moniker-end

## <a name="handle-stopping-and-starting-events"></a>Durdurma ve başlatma olaylarını işleme

İşlenecek [OnStarting](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice.onstarting), [OnStarted](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice.onstarted), ve [OnStopping](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice.onstopping) olayları, aşağıdaki ek değişiklikleri yapın:

1. Türetilen bir sınıf oluşturmanız [WebHostService](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice):

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=NoLogging)]

2. Bir genişletme yöntemi için oluşturma [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost) özel Geçiren `WebHostService` için [ServiceBase.Run](/dotnet/api/system.serviceprocess.servicebase.run):

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. İçinde `Program.Main`, yeni bir uzantı metodu çağırma `RunAsCustomService`, yerine [RunAsService](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostwindowsserviceextensions.runasservice):

   ::: moniker range=">= aspnetcore-2.0"

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=HandleStopStart&highlight=14)]

   > [!NOTE]
   > `isService` gelen geçirilen değil `Main` içine `CreateWebHostBuilder` çünkü imzası `CreateWebHostBuilder` olmalıdır `CreateWebHostBuilder(string[])` sırayla [tümleştirme testi](xref:test/integration-tests) düzgün çalışması için.

   ::: moniker-end

   ::: moniker range="< aspnetcore-2.0"

   [!code-csharp[](windows-service/samples_snapshot/1.x/AspNetCoreService/Program.cs?name=HandleStopStart&highlight=27)]

   ::: moniker-end

Varsa özel `WebHostService` kod bağımlılık ekleme (örneğin, bir Günlükçü) hizmetinden gerektirir,'ndan elde [IWebHost.Services](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost.services) özelliği:

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=Logging&highlight=7-8)]

## <a name="proxy-server-and-load-balancer-scenarios"></a>Ara sunucu ve yük dengeleyici senaryoları

Internet'ten veya kurumsal ağ istekleri etkileşim ve bir proxy'nin arkasındaysa veya yük dengeleyici Hizmetleri ek yapılandırma gerektirebilir. Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kestrel'i uç nokta Yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS yapılandırma ve SNI desteği içerir)
* <xref:fundamentals/host/web-host>
