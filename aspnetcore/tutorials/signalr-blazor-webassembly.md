---
title: İle ASP.NET Core kullanma SignalRBlazor WebAssembly
author: guardrex
description: İle ASP.NET Core kullanan bir sohbet uygulaması oluşturun SignalR Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 3d7ae49142849c589a1a20c33d30e87747ad1935
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021633"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="0c67a-103">İle ASP.NET Core kullanma SignalRBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="0c67a-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="0c67a-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="0c67a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0c67a-105">Bu öğretici ile kullanarak gerçek zamanlı bir uygulama oluşturma hakkında temel bilgileri öğretir SignalR Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="0c67a-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="0c67a-106">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="0c67a-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0c67a-107">Blazor WebAssemblyBarındırılan uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0c67a-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="0c67a-108">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="0c67a-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="0c67a-109">Hub ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="0c67a-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="0c67a-110">SignalRHub için hizmetler ve uç nokta ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="0c67a-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="0c67a-111">RazorSohbet için bileşen kodu ekle</span><span class="sxs-lookup"><span data-stu-id="0c67a-111">Add Razor component code for chat</span></span>

<span data-ttu-id="0c67a-112">Bu öğreticinin sonunda, çalışan bir sohbet uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="0c67a-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="0c67a-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0c67a-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0c67a-114">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="0c67a-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0c67a-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c67a-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0c67a-116">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019 16,6 veya üzeri](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="0c67a-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0c67a-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c67a-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0c67a-118">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c67a-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="0c67a-119">Mac için Visual Studio Sürüm 8,6 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="0c67a-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="0c67a-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0c67a-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="0c67a-121">Barındırılan Blazor WebAssembly uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0c67a-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="0c67a-122">Araç seçiminiz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="0c67a-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0c67a-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c67a-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="0c67a-124">Visual Studio 16,6 veya üzeri ve .NET Core SDK 3.1.300 veya üzeri gereklidir.</span><span class="sxs-lookup"><span data-stu-id="0c67a-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="0c67a-125">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="0c67a-125">Create a new project.</span></span>

1. <span data-ttu-id="0c67a-126">\*\* Blazor Uygulama\*\* ' yı seçin ve **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="0c67a-127">`BlazorSignalRApp` **Proje adı** alanına yazın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="0c67a-128">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="0c67a-129">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-129">Select **Create**.</span></span>

1. <span data-ttu-id="0c67a-130">\*\* Blazor WebAssembly Uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="0c67a-131">**Gelişmiş**' in altında, **ASP.NET Core barındırılan** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="0c67a-132">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0c67a-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c67a-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0c67a-134">Bir komut kabuğunda, aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="0c67a-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="0c67a-135">Visual Studio Code, uygulamanın proje klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="0c67a-136">Uygulamayı derlemek ve hata ayıklamak için varlık Ekle iletişim kutusu göründüğünde **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="0c67a-137">Visual Studio Code, klasörü otomatik olarak `.vscode` oluşturulan `launch.json` ve dosyaları ekler `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="0c67a-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0c67a-138">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c67a-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0c67a-139">En son [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/) sürümünü yükleyip aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="0c67a-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="0c67a-140">**Dosya**  >  **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0c67a-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="0c67a-141">Yan çubukta **Web ve konsol**  >  **uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="0c67a-142">\*\* Blazor WebAssembly Uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="0c67a-143">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-143">Select **Next**.</span></span>

1. <span data-ttu-id="0c67a-144">**Kimlik** doğrulamasının **kimlik doğrulaması yok**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-144">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="0c67a-145">**Barındırılan ASP.NET Core** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-145">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="0c67a-146">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-146">Select **Next**.</span></span>

1. <span data-ttu-id="0c67a-147">**Proje adı** alanında, uygulamayı adlandırın `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="0c67a-147">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="0c67a-148">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-148">Select **Create**.</span></span>

   <span data-ttu-id="0c67a-149">Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="0c67a-150">Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0c67a-150">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="0c67a-151">Proje klasörüne gidip projenin çözüm dosyasını () açarak projeyi açın `.sln` .</span><span class="sxs-lookup"><span data-stu-id="0c67a-151">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0c67a-152">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0c67a-152">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="0c67a-153">Bir komut kabuğunda, aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="0c67a-153">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="0c67a-154">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="0c67a-154">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0c67a-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c67a-155">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="0c67a-156">**Çözüm Gezgini**, projeye sağ tıklayın `BlazorSignalRApp.Client` ve **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-156">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="0c67a-157">**NuGet Paketlerini Yönet** iletişim kutusunda, **paket kaynağının** olarak ayarlandığını doğrulayın `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="0c67a-157">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="0c67a-158">**Araştır** seçiliyken, `Microsoft.AspNetCore.SignalR.Client` Arama kutusuna yazın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-158">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="0c67a-159">Arama sonuçlarında, [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) paketi seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-159">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="0c67a-160">**Değişiklikleri Önizle** iletişim kutusu görüntülenirse **Tamam**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-160">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="0c67a-161">**Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul ediyorum** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-161">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0c67a-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c67a-162">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="0c67a-163">**Tümleşik terminalde** (araç çubuğundan**Görünüm**  >  **terminali** ) aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="0c67a-163">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0c67a-164">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c67a-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0c67a-165">**Çözüm** kenar çubuğunda projeye sağ tıklayın `BlazorSignalRApp.Client` ve **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-165">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="0c67a-166">**NuGet Paketlerini Yönet** iletişim kutusunda, kaynak açılan kutusunun olarak ayarlandığını doğrulayın `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="0c67a-166">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="0c67a-167">**Araştır** seçiliyken, `Microsoft.AspNetCore.SignalR.Client` Arama kutusuna yazın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-167">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="0c67a-168">Arama sonuçlarında, paketin yanındaki onay kutusunu işaretleyin [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-168">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="0c67a-169">**Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-169">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0c67a-170">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0c67a-170">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="0c67a-171">Komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="0c67a-171">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="0c67a-172">Hub ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="0c67a-172">Add a SignalR hub</span></span>

<span data-ttu-id="0c67a-173">`BlazorSignalRApp.Server`Projesinde, bir `Hubs` (plural) klasörü oluşturun ve aşağıdaki `ChatHub` sınıfı ( `Hubs/ChatHub.cs` ) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0c67a-173">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="0c67a-174">Hub için hizmetler ve uç nokta ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="0c67a-174">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="0c67a-175">`BlazorSignalRApp.Server`Projede `Startup.cs` dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-175">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="0c67a-176">Sınıfın ad alanını `ChatHub` dosyanın en üstüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0c67a-176">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="0c67a-177">SignalRSıkıştırma ara yazılım hizmetlerini ekleme ve yanıtlama `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0c67a-177">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="0c67a-178">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="0c67a-178">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="0c67a-179">İşlem ardışık düzeninin yapılandırmasının en üstünde yanıt sıkıştırma ara yazılımı ' nı kullanın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-179">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="0c67a-180">Denetleyiciler ve istemci tarafı geri dönüş uç noktaları arasında merkez için bir uç nokta ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-180">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="0c67a-181">RazorSohbet için bileşen kodu ekle</span><span class="sxs-lookup"><span data-stu-id="0c67a-181">Add Razor component code for chat</span></span>

1. <span data-ttu-id="0c67a-182">`BlazorSignalRApp.Client`Projede `Pages/Index.razor` dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-182">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="0c67a-183">İşaretlemeyi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0c67a-183">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="0c67a-184">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="0c67a-184">Run the app</span></span>

1. <span data-ttu-id="0c67a-185">Araç kılavuzunuz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="0c67a-185">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0c67a-186">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c67a-186">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0c67a-187">**Çözüm Gezgini**, `BlazorSignalRApp.Server` projeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-187">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="0c67a-188">Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd> + <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-188">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="0c67a-189">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-189">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0c67a-190">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-190">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="0c67a-191">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="0c67a-191">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0c67a-193">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0c67a-193">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0c67a-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c67a-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0c67a-195">VS Code, sunucu uygulaması () için bir başlatma profili oluşturmak üzere teklif edildiğinde `.vscode/launch.json` , `program` uygulamanın derlemesini () göstermek için giriş aşağıdakine benzer şekilde görünür `{APPLICATION NAME}.Server.dll` :</span><span class="sxs-lookup"><span data-stu-id="0c67a-195">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="0c67a-196">Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd> + <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-196">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="0c67a-197">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-197">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0c67a-198">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-198">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="0c67a-199">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="0c67a-199">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0c67a-201">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0c67a-201">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0c67a-202">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c67a-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0c67a-203">**Çözüm** kenar çubuğunda `BlazorSignalRApp.Server` projeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-203">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="0c67a-204">Uygulamayı <kbd>⌘</kbd>hata + <kbd>↩</kbd> <kbd>⌥</kbd> + <kbd>⌘</kbd> + ayıklamadan çalıştırmak için hata ayıklama veya ⌥ ⌘<kbd>↩</kbd> ile uygulamayı çalıştırmak için ⌘ ↩ tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-204">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="0c67a-205">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-205">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0c67a-206">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-206">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="0c67a-207">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="0c67a-207">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0c67a-209">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0c67a-209">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0c67a-210">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0c67a-210">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="0c67a-211">Komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="0c67a-211">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="0c67a-212">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="0c67a-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0c67a-213">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="0c67a-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="0c67a-214">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="0c67a-214">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0c67a-216">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0c67a-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="0c67a-217">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="0c67a-217">Next steps</span></span>

<span data-ttu-id="0c67a-218">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="0c67a-218">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0c67a-219">Blazor WebAssemblyBarındırılan uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0c67a-219">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="0c67a-220">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="0c67a-220">Add the SignalR client library</span></span>
> * <span data-ttu-id="0c67a-221">Hub ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="0c67a-221">Add a SignalR hub</span></span>
> * <span data-ttu-id="0c67a-222">SignalRHub için hizmetler ve uç nokta ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="0c67a-222">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="0c67a-223">RazorSohbet için bileşen kodu ekle</span><span class="sxs-lookup"><span data-stu-id="0c67a-223">Add Razor component code for chat</span></span>

<span data-ttu-id="0c67a-224">Uygulama oluşturma hakkında daha fazla bilgi edinmek için Blazor Blazor belgelere bakın:</span><span class="sxs-lookup"><span data-stu-id="0c67a-224">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="0c67a-225">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0c67a-225">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="0c67a-226">SignalRkimlik doğrulaması için çıkış noktaları arası anlaşma</span><span class="sxs-lookup"><span data-stu-id="0c67a-226">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
