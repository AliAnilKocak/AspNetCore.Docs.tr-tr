---
title: ASP.NET Core üzerinde SignalR ile çalışmaya başlama
author: rachelappel
description: Bu öğreticide, SignalR için ASP.NET Core kullanarak bir uygulama oluşturun.
manager: wpickett
monikerRange: '>= aspnetcore-2.1'
ms.author: rachelap
ms.custom: mvc
ms.date: 05/22/2018
ms.prod: aspnet-core
ms.topic: tutorial
ms.technology: aspnet
uid: signalr/get-started
ms.openlocfilehash: 880abd87805990baf8dd977c340a60582e54d2df
ms.sourcegitcommit: a0b6319c36f41cdce76ea334372f6e14fc66507e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/02/2018
ms.locfileid: "34729512"
---
# <a name="get-started-with-signalr-on-aspnet-core"></a><span data-ttu-id="95eac-103">ASP.NET Core üzerinde SignalR ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="95eac-103">Get started with SignalR on ASP.NET Core</span></span>

<span data-ttu-id="95eac-104">Tarafından [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="95eac-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="95eac-105">Bu öğretici, SignalR için ASP.NET Core kullanarak gerçek zamanlı bir uygulama oluşturma temelleri öğretir.</span><span class="sxs-lookup"><span data-stu-id="95eac-105">This tutorial teaches the basics of building a real-time app using SignalR for ASP.NET Core.</span></span>

   ![Çözüm](get-started/_static/signalr-get-started-finished.png)

<span data-ttu-id="95eac-107">Bu öğretici aşağıdaki SignalR geliştirme görevleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="95eac-107">This tutorial demonstrates the following SignalR development tasks:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="95eac-108">Bir SignalR ASP.NET Core web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95eac-108">Create a SignalR on ASP.NET Core web app.</span></span>
> * <span data-ttu-id="95eac-109">İçeriği istemcilere göndermek için bir SignalR hub'ı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95eac-109">Create a SignalR hub to push content to clients.</span></span>
> * <span data-ttu-id="95eac-110">Değiştirme `Startup` sınıfı ve uygulamayı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="95eac-110">Modify the `Startup` class and configure the app.</span></span>

<span data-ttu-id="95eac-111">[Görüntülemek veya karşıdan örnek kod](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/get-started/sample/) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="95eac-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/get-started/sample/) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

# <a name="prerequisites"></a><span data-ttu-id="95eac-112">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="95eac-112">Prerequisites</span></span>

<span data-ttu-id="95eac-113">Aşağıdaki yazılımı yükleyin:</span><span class="sxs-lookup"><span data-stu-id="95eac-113">Install the following software:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="95eac-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95eac-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="95eac-115">.NET core SDK 2.1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="95eac-115">.NET Core SDK 2.1 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="95eac-116">[Visual Studio 2017](https://www.visualstudio.com/downloads/) sürüm 15.7 veya üstü **ASP.NET ve web geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="95eac-116">[Visual Studio 2017](https://www.visualstudio.com/downloads/) version 15.7 or later with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="95eac-117">npm</span><span class="sxs-lookup"><span data-stu-id="95eac-117">npm</span></span>](https://www.npmjs.com/get-npm)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="95eac-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95eac-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="95eac-119">.NET core SDK 2.1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="95eac-119">.NET Core SDK 2.1 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="95eac-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95eac-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="95eac-121">C# Visual Studio kodu</span><span class="sxs-lookup"><span data-stu-id="95eac-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="95eac-122">npm</span><span class="sxs-lookup"><span data-stu-id="95eac-122">npm</span></span>](https://www.npmjs.com/get-npm)

-----

## <a name="create-an-aspnet-core-project-that-hosts-signalr-client-and-server"></a><span data-ttu-id="95eac-123">SignalR istemcisi ve sunucusu barındıran bir ASP.NET Core projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="95eac-123">Create an ASP.NET Core project that hosts SignalR client and server</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="95eac-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95eac-124">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="95eac-125">Kullanım **dosya** > **yeni proje** menü seçeneğini ve seçin **ASP.NET çekirdek Web uygulaması**.</span><span class="sxs-lookup"><span data-stu-id="95eac-125">Use the **File** > **New Project** menu option and choose **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="95eac-126">Proje adı *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="95eac-126">Name the project *SignalRChat*.</span></span>

   ![Visual Studio'da yeni proje iletişim kutusu](get-started/_static/signalr-new-project-dialog.png)

2. <span data-ttu-id="95eac-128">Seçin **Web uygulaması** Razor sayfalarının kullanarak bir proje oluşturmak için.</span><span class="sxs-lookup"><span data-stu-id="95eac-128">Select **Web Application** to create a project using Razor Pages.</span></span> <span data-ttu-id="95eac-129">Ardından **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="95eac-129">Then select **OK**.</span></span> <span data-ttu-id="95eac-130">Olduğundan emin olun **ASP.NET Core 2.1** SignalR .NET eski sürümlerinde çalışır ancak framework seçicisini seçilir.</span><span class="sxs-lookup"><span data-stu-id="95eac-130">Be sure that **ASP.NET Core 2.1** is selected from the framework selector, though SignalR runs on older versions of .NET.</span></span>

   ![Visual Studio'da yeni proje iletişim kutusu](get-started/_static/signalr-new-project-choose-type.png)

<span data-ttu-id="95eac-132">Visual Studio içerir `Microsoft.AspNetCore.SignalR` parçası olarak, sunucu kitaplıklarından içeren paket kendi **ASP.NET çekirdek Web uygulaması** şablonu.</span><span class="sxs-lookup"><span data-stu-id="95eac-132">Visual Studio includes the `Microsoft.AspNetCore.SignalR` package containing its server libraries as part of its **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="95eac-133">Ancak, SignalR için JavaScript istemci kitaplığı kullanılarak yüklenmelidir *npm*.</span><span class="sxs-lookup"><span data-stu-id="95eac-133">However, the JavaScript client library for SignalR must be installed using *npm*.</span></span>

3. <span data-ttu-id="95eac-134">Aşağıdaki komutları çalıştırın **Paket Yöneticisi Konsolu** penceresinden proje kök:</span><span class="sxs-lookup"><span data-stu-id="95eac-134">Run the following commands in the **Package Manager Console** window, from the project root:</span></span>

    ```console
    npm init -y
    npm install @aspnet/signalr
    ```     

4. <span data-ttu-id="95eac-135">İçindeki "signalr" adlı yeni bir klasör oluşturun *lib* projenizdeki klasöre.</span><span class="sxs-lookup"><span data-stu-id="95eac-135">Create a new folder named "signalr" inside the  *lib* folder in your project.</span></span> <span data-ttu-id="95eac-136">Ardından kopyalama *signalr.js* dosya *node_modules\\ @aspnet\signalr\dist\browser*  bu klasöre.</span><span class="sxs-lookup"><span data-stu-id="95eac-136">Then copy the *signalr.js* file from *node_modules\\@aspnet\signalr\dist\browser* to this folder.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="95eac-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95eac-137">Visual Studio Code</span></span>](#tab/visual-studio-code/)

1. <span data-ttu-id="95eac-138">Gelen **tümleşik Terminal**, aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="95eac-138">From the **Integrated Terminal**, run the following command:</span></span>

    ```console
    dotnet new webapp -o SignalRChat
    ```

2. <span data-ttu-id="95eac-139">JavaScript istemci kitaplığını kullanarak yükleme *npm*.</span><span class="sxs-lookup"><span data-stu-id="95eac-139">Install the JavaScript client library using *npm*.</span></span>

    ```console
    npm init -y
    npm install @aspnet/signalr
    ```

3. <span data-ttu-id="95eac-140">Kopya *signalr.js* dosya *node_modules\\ @aspnet\signalr\dist\browser*  için *lib* projenizdeki klasöre.</span><span class="sxs-lookup"><span data-stu-id="95eac-140">Copy the *signalr.js* file from *node_modules\\@aspnet\signalr\dist\browser* to the *lib* folder in your project.</span></span>

-----

## <a name="create-the-signalr-hub"></a><span data-ttu-id="95eac-141">SignalR hub'ı Oluştur</span><span class="sxs-lookup"><span data-stu-id="95eac-141">Create the SignalR Hub</span></span>

<span data-ttu-id="95eac-142">Bir hub istemci ve sunucu birbirine yöntemlerini çağırmaya izin veren üst düzey bir işlem hattı görevi gören bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="95eac-142">A hub is a class that serves as a high-level pipeline that allows the client and server to call methods on each other.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="95eac-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95eac-143">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="95eac-144">Bir sınıf projeye seçerek eklemek **dosya** > **yeni** > **dosya** ve seçerek **Visual C# sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="95eac-144">Add a class to the project by choosing **File** > **New** > **File** and selecting **Visual C# Class**.</span></span> <span data-ttu-id="95eac-145">Dosya adı *ChatHub*.</span><span class="sxs-lookup"><span data-stu-id="95eac-145">Name the file *ChatHub*.</span></span> 

2. <span data-ttu-id="95eac-146">Devralınan `Microsoft.AspNetCore.SignalR.Hub`.</span><span class="sxs-lookup"><span data-stu-id="95eac-146">Inherit from `Microsoft.AspNetCore.SignalR.Hub`.</span></span> <span data-ttu-id="95eac-147">`Hub` Özellikleri ve olayları gönderme ve alma veri yanı sıra bağlantıları ve grupları yönetmek için sınıf içerir.</span><span class="sxs-lookup"><span data-stu-id="95eac-147">The `Hub` class contains properties and events for managing connections and groups, as well as sending and receiving data.</span></span>

3. <span data-ttu-id="95eac-148">Oluşturma `SendMessage` tüm bağlı sohbet istemcilere bir ileti gönderir yöntemi.</span><span class="sxs-lookup"><span data-stu-id="95eac-148">Create the `SendMessage` method that sends a message to all connected chat clients.</span></span> <span data-ttu-id="95eac-149">Döndürdüğü fark bir [görev](https://msdn.microsoft.com/library/system.threading.tasks.task(v=vs.110).aspx), SignalR zaman uyumsuz olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="95eac-149">Notice it returns a [Task](https://msdn.microsoft.com/library/system.threading.tasks.task(v=vs.110).aspx), because SignalR is asynchronous.</span></span> <span data-ttu-id="95eac-150">Zaman uyumsuz kodu daha iyi ölçeklenir.</span><span class="sxs-lookup"><span data-stu-id="95eac-150">Asynchronous code scales better.</span></span>

   [!code-csharp[Startup](get-started/sample/Hubs/ChatHub.cs)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="95eac-151">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95eac-151">Visual Studio Code</span></span>](#tab/visual-studio-code/)

1. <span data-ttu-id="95eac-152">Açık *SignalRChat* Visual Studio Code klasöründe.</span><span class="sxs-lookup"><span data-stu-id="95eac-152">Open the *SignalRChat* folder in Visual Studio Code.</span></span>

2. <span data-ttu-id="95eac-153">Bir sınıf projeye seçerek eklemek **dosya** > **yeni dosya** menüsünde.</span><span class="sxs-lookup"><span data-stu-id="95eac-153">Add a class to the project by selecting **File** > **New File** from the menu.</span></span>

3. <span data-ttu-id="95eac-154">Devralınan `Microsoft.AspNetCore.SignalR.Hub`.</span><span class="sxs-lookup"><span data-stu-id="95eac-154">Inherit from `Microsoft.AspNetCore.SignalR.Hub`.</span></span> <span data-ttu-id="95eac-155">`Hub` Özellikleri ve olayları istemciye gönderme ve alma veri yanı sıra bağlantıları ve grupları yönetmek için sınıf içerir.</span><span class="sxs-lookup"><span data-stu-id="95eac-155">The `Hub` class contains properties and events for managing connections and groups, as well as sending and receiving data to clients.</span></span>

4. <span data-ttu-id="95eac-156">Ekleme bir `SendMessage` sınıfına yöntemi.</span><span class="sxs-lookup"><span data-stu-id="95eac-156">Add a `SendMessage` method to the class.</span></span> <span data-ttu-id="95eac-157">`SendMessage` Yöntem, tüm bağlı sohbet istemcilere bir ileti gönderir.</span><span class="sxs-lookup"><span data-stu-id="95eac-157">The `SendMessage` method sends a message to all connected chat clients.</span></span> <span data-ttu-id="95eac-158">Döndürdüğü fark bir [görev](/dotnet/api/system.threading.tasks.task), SignalR zaman uyumsuz olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="95eac-158">Notice it returns a [Task](/dotnet/api/system.threading.tasks.task), because SignalR is asynchronous.</span></span> <span data-ttu-id="95eac-159">Zaman uyumsuz kodu daha iyi ölçeklenir.</span><span class="sxs-lookup"><span data-stu-id="95eac-159">Asynchronous code scales better.</span></span>

   [!code-csharp[Startup](get-started/sample/Hubs/ChatHub.cs?range=6-12)]

-----

## <a name="configure-the-project-to-use-signalr"></a><span data-ttu-id="95eac-160">SignalR kullanmak üzere proje yapılandırma</span><span class="sxs-lookup"><span data-stu-id="95eac-160">Configure the project to use SignalR</span></span>

<span data-ttu-id="95eac-161">SignalR için isteklerini iletmek için bilir böylece SignalR sunucusunun yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="95eac-161">The SignalR server must be configured so that it knows to pass requests to SignalR.</span></span>

1. <span data-ttu-id="95eac-162">Bir SignalR projesi yapılandırmak için projenin değiştirin `Startup.ConfigureServices` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="95eac-162">To configure a SignalR project, modify the project's `Startup.ConfigureServices` method.</span></span>

   <span data-ttu-id="95eac-163">`services.AddSignalR` SignalR parçası olarak ekler [ara yazılım](xref:fundamentals/middleware/index) ardışık düzen.</span><span class="sxs-lookup"><span data-stu-id="95eac-163">`services.AddSignalR` adds SignalR as part of the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>

2. <span data-ttu-id="95eac-164">Yollar kullanılarak, hub'larınız için yapılandırma `UseSignalR`.</span><span class="sxs-lookup"><span data-stu-id="95eac-164">Configure routes to your hubs using `UseSignalR`.</span></span>


   [!code-csharp[Startup](get-started/sample/Startup.cs?highlight=37,57-60)]


## <a name="create-the-signalr-client-code"></a><span data-ttu-id="95eac-165">SignalR istemci kodu oluşturma</span><span class="sxs-lookup"><span data-stu-id="95eac-165">Create the SignalR client code</span></span>

1. <span data-ttu-id="95eac-166">İçeriği Değiştir *Pages\Index.cshtml* aşağıdaki kod ile:</span><span class="sxs-lookup"><span data-stu-id="95eac-166">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

   [!code-cshtml[Index](get-started/sample/Pages/Index.cshtml)]

   <span data-ttu-id="95eac-167">Önceki HTML adını ve ileti alanları ve bir gönderme düğmesi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="95eac-167">The preceding HTML displays name and message fields, and a submit button.</span></span> <span data-ttu-id="95eac-168">Komut dosyası başvuruları altındaki dikkat edin: SignalR başvurusu ve *chat.js*.</span><span class="sxs-lookup"><span data-stu-id="95eac-168">Notice the script references at the bottom: a reference to SignalR and *chat.js*.</span></span>

2. <span data-ttu-id="95eac-169">Adlı bir JavaScript dosyası ekleme *chat.js*, *wwwroot\js* klasör.</span><span class="sxs-lookup"><span data-stu-id="95eac-169">Add a JavaScript file, named *chat.js*, to the *wwwroot\js* folder.</span></span> <span data-ttu-id="95eac-170">Aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="95eac-170">Add the following code to it:</span></span>

   [!code-javascript[Index](get-started/sample/wwwroot/js/chat.js)]

## <a name="run-the-app"></a><span data-ttu-id="95eac-171">Uygulama Çalıştırma</span><span class="sxs-lookup"><span data-stu-id="95eac-171">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="95eac-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95eac-172">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="95eac-173">Seçin **hata ayıklama** > **Başlat hata ayıklama olmadan** bir tarayıcıyı başlatın ve Web sitesi yerel olarak yüklemek için.</span><span class="sxs-lookup"><span data-stu-id="95eac-173">Select **Debug** > **Start without debugging** to launch a browser and load the website locally.</span></span> <span data-ttu-id="95eac-174">Adres çubuğundan URL'yi kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="95eac-174">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="95eac-175">Başka bir tarayıcı örneğini (herhangi bir tarayıcıda) açın ve URL'yi adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="95eac-175">Open another browser instance (any browser) and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="95eac-176">Ya da tarayıcı seçin, bir ad ve ileti girin ve tıklatın **Gönder** düğmesi.</span><span class="sxs-lookup"><span data-stu-id="95eac-176">Choose either browser, enter a name and message, and click the **Send** button.</span></span> <span data-ttu-id="95eac-177">Ad ve ileti iki sayfalarında anında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="95eac-177">The name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="95eac-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95eac-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="95eac-179">Tuşuna **hata ayıklama** derlemek ve programı çalıştırmak için (F5).</span><span class="sxs-lookup"><span data-stu-id="95eac-179">Press **Debug** (F5) to build and run the program.</span></span> <span data-ttu-id="95eac-180">Programın çalıştırılması, bir tarayıcı penceresi açılır.</span><span class="sxs-lookup"><span data-stu-id="95eac-180">Running the program opens a browser window.</span></span>

1. <span data-ttu-id="95eac-181">Başka bir tarayıcı penceresi açın ve Web sitesi yerel olarak yükleyin.</span><span class="sxs-lookup"><span data-stu-id="95eac-181">Open another browser window and load the website locally in it.</span></span>

1. <span data-ttu-id="95eac-182">Ya da tarayıcı seçin, bir ad ve ileti girin ve tıklatın **Gönder** düğmesi.</span><span class="sxs-lookup"><span data-stu-id="95eac-182">Choose either browser, enter a name and message, and click the **Send** button.</span></span> <span data-ttu-id="95eac-183">Ad ve ileti iki sayfalarında anında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="95eac-183">The name and message are displayed on both pages instantly.</span></span>

---

  ![Çözüm](get-started/_static/signalr-get-started-finished.png)

## <a name="related-resources"></a><span data-ttu-id="95eac-185">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="95eac-185">Related resources</span></span>

[<span data-ttu-id="95eac-186">ASP.NET Core SignalR giriş</span><span class="sxs-lookup"><span data-stu-id="95eac-186">Introduction to ASP.NET Core SignalR</span></span>](introduction.md)
