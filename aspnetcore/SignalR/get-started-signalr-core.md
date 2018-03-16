---
title: "ASP.NET Core üzerinde SignalR ile çalışmaya başlama"
author: rachelappel
description: "Bu öğreticide, SignalR için ASP.NET Core kullanarak bir uygulama oluşturun."
manager: wpickett
ms.author: rachelap
ms.custom: mvc
ms.date: 03/16/2018
ms.prod: aspnet-core
ms.topic: tutorial
ms.technology: aspnet
uid: signalr/get-started-signalr-core
ms.openlocfilehash: 139da5a2d0dadf51fece94b7c54ccd531e0ae8c2
ms.sourcegitcommit: 6548a3dd0cd1e3e92ac2310dee757ddad9fd6456
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-get-started-with-signalr-for-aspnet-core"></a>Öğretici: ASP.NET Core için SignalR ile çalışmaya başlama

Tarafından [Rachel Appel](https://twitter.com/rachelappel)

Bu öğretici, SignalR için ASP.NET Core kullanarak gerçek zamanlı bir uygulama oluşturma temelleri öğretir.

   ![Çözüm](get-started-signalr-core/_static/signalr-get-started-finished.png)

Bu öğretici aşağıdaki SignalR geliştirme görevleri gösterir:

> [!div class="checklist"]
> * Bir SignalR ASP.NET Core web uygulaması oluşturun.
> * İçeriği istemcilere göndermek için bir SignalR hub'ı oluşturun.
> * Değiştirme `Startup` sınıfı ve uygulamayı yapılandırır.

[Görüntülemek veya karşıdan örnek kod](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/get-started-signalr-core/sample/) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))

# <a name="prerequisites"></a>Önkoşullar

Aşağıdaki yazılımı yükleyin:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET core 2.1.0 Önizleme 1 SDK](https://www.microsoft.com/net/download/dotnet-core/sdk-2.1.300-preview1) veya daha yenisi
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) sürüm 15.6 veya üstü **ASP.NET ve web geliştirme** iş yükü
* [npm](https://www.npmjs.com/get-npm)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET core 2.1.0 Önizleme 1 SDK](https://www.microsoft.com/net/download/dotnet-core/sdk-2.1.300-preview1) veya daha yenisi
* [Visual Studio Code](https://code.visualstudio.com/download) 
* [C# Visual Studio kodu](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [npm](https://www.npmjs.com/get-npm)

-----

## <a name="create-an-aspnet-core-project-that-hosts-signalr-client-and-server"></a>SignalR istemcisi ve sunucusu barındıran bir ASP.NET Core projesi oluşturma

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Kullanım **dosya** > **yeni proje** menü seçeneğini ve seçin **ASP.NET çekirdek Web uygulaması**. Proje adı *SignalRChat*.

  ![Visual Studio'da yeni proje iletişim kutusu](get-started-signalr-core/_static/signalr-new-project-dialog.png)

2. Seçin **Web uygulaması** Razor sayfalarının kullanarak bir proje oluşturmak için. Ardından **Tamam**. Olduğundan emin olun **ASP.NET Core 2.1** SignalR .NET eski sürümlerinde çalışır ancak framework seçicisini seçilir.

  ![Visual Studio'da yeni proje iletişim kutusu](get-started-signalr-core/_static/signalr-new-project-choose-type.png)

3. ' Nde projeye sağ **Çözüm Gezgini** > **Ekle** > **yeni öğe** > **npm yapılandırma dosyası** . Dosya adı *package.json*.

4. Aşağıdaki komutu çalıştırın **Paket Yöneticisi Konsolu** penceresinden proje kök:

    ```console
      npm install @aspnet/signalr
    ```
5. Kopya *signalr.js* dosya *node_modules\\ @aspnet\signalr\dist\browser*  için *wwwroot\lib* projenizdeki klasöre.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Gelen **tümleşik Terminal**, aşağıdaki komutu çalıştırın:
 
    ```console
      dotnet new razor -o SignalRChat
    ```

2. JavaScript istemci kitaplığını kullanarak yükleme *npm*.

    ```
      npm init -y
      npm install @aspnet/signalr
    ```

-----

## <a name="create-the-signalr-hub"></a>SignalR hub'ı Oluştur

Bir hub istemci ve sunucu birbirine yöntemlerini çağırmaya izin veren üst düzey bir işlem hattı görevi gören bir sınıftır.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Bir sınıf projeye seçerek eklemek **dosya** > **yeni** > **dosya** ve seçerek **Visual C# sınıfı**.

1. Devralınan `Microsoft.AspNetCore.SignalR.Hub`. `Hub` Özellikleri ve olayları gönderme ve alma veri yanı sıra bağlantıları ve grupları yönetmek için sınıf içerir.

1. Oluşturma `SendMessage` tüm bağlı sohbet istemcilere bir ileti gönderir yöntemi. Döndürdüğü fark bir [görev](https://msdn.microsoft.com/en-us/library/system.threading.tasks.task(v=vs.110).aspx), SignalR zaman uyumsuz olarak çağrılır. Zaman uyumsuz kodu daha iyi ölçeklenir.

  [!code-csharp[Startup](get-started-signalr-core/sample/Hubs/ChatHub.cs?range=7-14)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Açık *SignalRChat* Visual Studio Code klasöründe.

1. Bir sınıf projeye seçerek eklemek **dosya** > **yeni dosya** menüsünde.

1. Devralınan `Microsoft.AspNetCore.SignalR.Hub`. `Hub` Özellikleri ve olayları istemciye gönderme ve alma veri yanı sıra bağlantıları ve grupları yönetmek için sınıf içerir.

1. Ekleme bir `SendMessage` sınıfına yöntemi. `SendMessage` Yöntem, tüm bağlı sohbet istemcilere bir ileti gönderir. Döndürdüğü fark bir [görev](/dotnet/api/system.threading.tasks.task), SignalR zaman uyumsuz olarak çağrılır. Zaman uyumsuz kodu daha iyi ölçeklenir.

  [!code-csharp[Startup](get-started-signalr-core/sample/Hubs/ChatHub.cs?range=7-14)]

-----

## <a name="configure-the-project-to-use-signalr"></a>SignalR kullanmak üzere proje yapılandırma

SignalR için isteklerini iletmek için bilir böylece SignalR sunucusunun yapılandırılması gerekir.

1. Bir SignalR projesi yapılandırmak için projenin değiştirin `Startup.ConfigureServices` yöntemi.

  `services.AddSignalR` SignalR parçası olarak ekler [ara yazılım](xref:fundamentals/middleware/index) ardışık düzen.

1. Yollar kullanılarak, hub'larınız için yapılandırma `UseSignalR`.

  [!code-csharp[Startup](get-started-signalr-core/sample/Startup.cs?highlight=22,40-43)]

## <a name="create-the-signalr-client-code"></a>SignalR istemci kodu oluşturma

1. İçeriği Değiştir *Pages\Index.cshtml* aşağıdaki kod ile:

  [!code-cshtml[Index](get-started-signalr-core/sample/Pages/Index.cshtml)]

  Önceki HTML adını ve ileti alanları ve bir gönderme düğmesi görüntüler. Komut dosyası başvuruları altındaki dikkat edin: SignalR başvurusu ve *chat.js*.

1. Adlı bir JavaScript dosyası ekleme *chat.js*, *wwwroot\js* klasör. Aşağıdaki kodu ekleyin:

  [!code-javascript[Index](get-started-signalr-core/sample/wwwroot/js/chat.js)]

## <a name="run-the-app"></a>Uygulama Çalıştırma

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Seçin **hata ayıklama** > **Başlat hata ayıklama olmadan** bir tarayıcıyı başlatın ve Web sitesi yerel olarak yüklemek için. Adres çubuğundan URL'yi kopyalayın.

1. Başka bir tarayıcı örneğini (herhangi bir tarayıcıda) açın ve URL'yi adres çubuğuna yapıştırın.

1. Ya da tarayıcı seçin, bir ad ve ileti girin ve tıklatın **Gönder** düğmesi. Ad ve ileti iki sayfalarında anında görüntülenir.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Tuşuna **hata ayıklama** derlemek ve programı çalıştırmak için (F5). Programın çalıştırılması, bir tarayıcı penceresi açılır.

1. Başka bir tarayıcı penceresi açın ve Web sitesi yerel olarak yükleyin.

1. Ya da tarayıcı seçin, bir ad ve ileti girin ve tıklatın **Gönder** düğmesi. Ad ve ileti iki sayfalarında anında görüntülenir.

-----

  ![Çözüm](get-started-signalr-core/_static/signalr-get-started-finished.png)