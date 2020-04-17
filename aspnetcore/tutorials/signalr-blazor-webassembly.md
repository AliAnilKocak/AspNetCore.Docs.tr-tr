---
title: WebAssembly SignalR ile Blazor ASP.NET Core kullanın
author: guardrex
description: WebAssembly ile Blazor ASP.NET Core SignalR kullanan bir sohbet uygulaması oluşturun.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 798068c83e16070d3279c88c44af0cd96d182fe2
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488890"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Blazor WebAssembly ile ASP.NET Core SignalR kullanın

Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Bu öğretici Blazor WebAssembly ile SignalR kullanarak gerçek zamanlı bir uygulama oluşturma temellerini öğretir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Blazor WebAssembly Barındırılan uygulama projesi oluşturma
> * SignalR istemci kitaplığını ekleme
> * SignalR hub'ı ekleme
> * SignalR hizmetleri ve SignalR hub'ı için bir uç nokta ekleme
> * Sohbet için Razor bileşen kodu ekleme

Bu eğitimin sonunda, çalışan bir sohbet uygulamanız olacak.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Barındırılan blazor WebAssembly uygulama projesi oluşturma

Visual Studio sürüm 16.6 Preview 2 veya sonraki sürümlerini kullanmadığınızda [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) şablonu'nu yükleyin. [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) paketi, Blazor WebAssembly önizlemedeyken bir önizleme sürümüne sahiptir. Komut kabuğunda aşağıdaki komutu uygulayın:

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
```

Araç seçiminiz için kılavuzu izleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Yeni bir proje oluşturma.

1. **Blazor Uygulamasını** seçin ve **İleri'yi**seçin.

1. **Proje adı** alanına "BlazorSignalRApp" yazın. **Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum sağlayın. **Oluştur**’u seçin.

1. **Blazor WebAssembly App** şablonu seçin.

1. **Advanced**altında, **ASP.NET Core barındırılan** onay kutusunu seçin.

1. **Oluştur**’u seçin.

> [!NOTE]
> Visual Studio'nun yeni bir sürümünü yükselttiyseniz veya yüklediyseniz ve Blazor WebAssembly şablonu VS UI'de görünmüyorsa, şablonu daha önce gösterilen komutu `dotnet new` kullanarak yeniden yükleyin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Komut kabuğunda aşağıdaki komutu uygulayın:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Visual Studio Code'da uygulamanın proje klasörünü açın.

1. İletişim kutusu, uygulamayı oluşturmak ve hata ayıklamak için varlık eklemek için göründüğünde **Evet'i**seçin. Visual Studio Code otomatik olarak oluşturulan *launch.json ve tasks.json* dosyaları ile *.vscode* klasörünü ekler. *tasks.json*

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. Komut kabuğunda aşağıdaki komutu uygulayın:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Mac için Visual Studio'da, proje klasörüne gidip projenin çözüm dosyasını açarak projeyi açın (*.sln*).

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Komut kabuğunda aşağıdaki komutu uygulayın:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>SignalR istemci kitaplığını ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. **Solution Explorer'da** **BlazorSignalRApp.Client** projesine sağ tıklayın ve **NuGet Paketlerini Yönet'i**seçin.

1. **NuGet Paketlerini Yönet** iletişim kutusunda, **Paket kaynağının** *nuget.org*ayarlı olduğunu onaylayın.

1. **Gözat** seçili ile arama kutusuna "Microsoft.AspNetCore.SignalR.Client" yazın.

1. Arama sonuçlarında `Microsoft.AspNetCore.SignalR.Client` paketi seçin ve **Yükle'yi**seçin.

1. Önizleme **Değişiklikleri** iletişim kutusu görünüyorsa, **Tamam'ı**seçin.

1. Lisans **Kabul** iletişim kutusu görünürse, lisans koşullarını kabul ediyorsanız **Kabul Et'i** seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

**Tümleşik Terminalde** (Araç çubuğundan**Terminali** **Görüntüle)** > aşağıdaki komutları uygulayın:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Çözüm** kenar çubuğunda **BlazorSignalRApp.Client** projesine sağ tıklayın ve **NuGet Paketlerini Yönet'i**seçin.

1. **NuGet Paketlerini Yönet** iletişim kutusunda, kaynak açılır bırakmanın *nuget.org*olarak ayarlı olduğunu onaylayın.

1. **Gözat** seçili ile arama kutusuna "Microsoft.AspNetCore.SignalR.Client" yazın.

1. Arama sonuçlarında, `Microsoft.AspNetCore.SignalR.Client` paketin yanındaki onay kutusunu seçin ve Paket **Ekle'yi**seçin.

1. Lisans **Kabul** iletişim kutusu görünürse, lisans koşullarını kabul ediyorsanız **Kabul Et'i** seçin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Komut kabuğunda aşağıdaki komutları uygulayın:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>SignalR hub'ı ekleme

**BlazorSignalRApp.Server** projesinde, hub *(çoğul)* klasörü oluşturun `ChatHub` ve aşağıdaki sınıfı ekleyin *(Hubs/ChatHub.cs):*

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a>SignalR hizmetleri ve SignalR hub'ı için bir uç nokta ekleme

1. **BlazorSignalRApp.Server** projesinde *Startup.cs* dosyasını açın.

1. `ChatHub` Sınıfın ad alanını dosyanın en üstüne ekleyin:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. SignalR hizmetlerini aşağıdakilere `Startup.ConfigureServices`ekleyin:

   ```csharp
   services.AddSignalR();
   ```

1. Varsayılan `Startup.Configure` denetleyici rotasının uç noktaları ile istemci tarafı geri dönüşleri arasında, hub için bir uç nokta ekleyin:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Sohbet için Razor bileşen kodu ekleme

1. **BlazorSignalRApp.Client** projesinde *Pages/Index.razor* dosyasını açın.

1. Biçimlendirmeyi aşağıdaki kodla değiştirin:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Uygulamayı çalıştırma

1. Araçlama nız için kılavuzu izleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Solution Explorer'da** **BlazorSignalRApp.Server** projesini seçin. Hata ayıklama olmadan uygulamayı çalıştırmak için **Ctrl+F5** tuşuna basın.

1. URL'yi adres çubuğundan kopyalayın, başka bir tarayıcı örneği veya sekmesini açın ve URL'yi adres çubuğuna yapıştırın.

1. Tarayıcıdan birini seçin, bir ad ve mesaj girin ve **Gönder** düğmesini seçin. Ad ve ileti her iki sayfada anında görüntülenir:

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Tırnak: *Star Trek VI: Keşfedilmemiş Ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Araç çubuğundan > **Hata Ayıklama Yapmadan Hata Ayıklama Çalıştır'ı** seçin. **Debug**

1. URL'yi adres çubuğundan kopyalayın, başka bir tarayıcı örneği veya sekmesini açın ve URL'yi adres çubuğuna yapıştırın.

1. Tarayıcıdan birini seçin, bir ad ve mesaj girin ve **Gönder** düğmesini seçin. Ad ve ileti her iki sayfada anında görüntülenir:

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Tırnak: *Star Trek VI: Keşfedilmemiş Ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Çözüm** kenar çubuğunda **BlazorSignalRApp.Server** projesini seçin. Menüden > **Hata Ayıklama olmadan** **Başlat'ı**seçin.

1. URL'yi adres çubuğundan kopyalayın, başka bir tarayıcı örneği veya sekmesini açın ve URL'yi adres çubuğuna yapıştırın.

1. Tarayıcıdan birini seçin, bir ad ve mesaj girin ve **Gönder** düğmesini seçin. Ad ve ileti her iki sayfada anında görüntülenir:

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Tırnak: *Star Trek VI: Keşfedilmemiş Ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. Komut kabuğunda aşağıdaki komutları uygulayın:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. URL'yi adres çubuğundan kopyalayın, başka bir tarayıcı örneği veya sekmesini açın ve URL'yi adres çubuğuna yapıştırın.

1. Tarayıcıdan birini seçin, bir ad ve mesaj girin ve **Gönder** düğmesini seçin. Ad ve ileti her iki sayfada anında görüntülenir:

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Tırnak: *Star Trek VI: Keşfedilmemiş Ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Blazor WebAssembly Barındırılan uygulama projesi oluşturma
> * İstemci SignalR kitaplığını ekleme
> * SignalR Hub ekleme
> * Hub için hizmet ve bitiş noktası ekleme SignalR SignalR
> * Sohbet için Razor bileşen kodu ekleme

Uygulama oluşturma Blazor hakkında daha fazla Blazor bilgi edinmek için belgelere bakın:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:signalr/introduction>
