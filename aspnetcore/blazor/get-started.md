---
title: ASP.NET Core kullanmaya başlayınBlazor
author: guardrex
description: Blazor Blazor Tercih ettiğiniz araç ile uygulama oluşturarak başlayın.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 63fee0b6a3152640a5483c2a682eec7d04742145
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243609"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="06f52-103">ASP.NET Core kullanmaya başlayınBlazor</span><span class="sxs-lookup"><span data-stu-id="06f52-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="06f52-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="06f52-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="06f52-105">Kullanmaya başlamak için Blazor , araç seçiminiz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="06f52-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06f52-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06f52-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="06f52-107">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ' in en son sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="06f52-107">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="06f52-108">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="06f52-108">Create a new project.</span></span>

1. <span data-ttu-id="06f52-109">\*\* Blazor Uygulama\*\*seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-109">Select **Blazor App**.</span></span> <span data-ttu-id="06f52-110">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-110">Select **Next**.</span></span>

1. <span data-ttu-id="06f52-111">**Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="06f52-111">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="06f52-112">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="06f52-112">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="06f52-113">**Oluştur**'u seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-113">Select **Create**.</span></span>

1. <span data-ttu-id="06f52-114">BlazorWebassembly deneyimi için \*\* Blazor Webassembly uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-114">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="06f52-115">BlazorSunucu deneyimi için \*\* Blazor sunucu uygulaması\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-115">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="06f52-116">**Oluştur**'u seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-116">Select **Create**.</span></span>

   <span data-ttu-id="06f52-117">İki Blazor barındırma modeli, \* Blazor webassembly\* ve \* Blazor sunucu\*hakkında bilgi için bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="06f52-117">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="06f52-118"><kbd>Ctrl</kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="06f52-118">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="06f52-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="06f52-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="06f52-120">[.NET Core 3,1 SDK 'sının](https://dotnet.microsoft.com/download/dotnet-core/3.1)en son sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="06f52-120">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="06f52-121">SDK 'Yı daha önce yüklediyseniz, bir komut kabuğu 'nda aşağıdaki komutu yürüterek yüklü sürümünüzü belirleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="06f52-121">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="06f52-122">En son [Visual Studio Code](https://code.visualstudio.com/)sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="06f52-122">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="06f52-123">En son [C# for Visual Studio Code uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını ' a `debug.javascript.usePreview` ayarlayın `true` .</span><span class="sxs-lookup"><span data-stu-id="06f52-123">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

  <span data-ttu-id="06f52-124">`debug.javascript.usePreview` `true` Vs Code Kullanıcı arabirimini kullanmak üzere ayarlamak için, **Dosya**  >  **tercihleri**  >  **ayarları** ' nı açın ve arama yapın `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="06f52-124">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="06f52-125">**Node.js ve Chrome için yeni bir önizleme Içindeki JavaScript hata ayıklayıcısını kullan**onay kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="06f52-125">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="06f52-126">Bir Blazor weelsembly deneyimi için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="06f52-126">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="06f52-127">Bir Blazor sunucu deneyimi için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="06f52-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="06f52-128">İki Blazor barındırma modeli, \* Blazor webassembly\* ve \* Blazor sunucu\*hakkında bilgi için bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="06f52-128">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="06f52-129">`WebApplication1`Visual Studio Code klasörü açın.</span><span class="sxs-lookup"><span data-stu-id="06f52-129">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="06f52-130">IDE, projeyi derlemek ve hatalarını ayıklamak için varlık eklemenizi ister.</span><span class="sxs-lookup"><span data-stu-id="06f52-130">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="06f52-131">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-131">Select **Yes**.</span></span>

1. <span data-ttu-id="06f52-132"><kbd>Ctrl</kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="06f52-132">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="06f52-133">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06f52-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="06f52-134">[Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="06f52-134">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="06f52-135">**Dosya**  >  **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="06f52-135">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="06f52-136">Yan çubukta **Web ve konsol**  >  **uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-136">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="06f52-137">BlazorWebassembly deneyimi için \*\* Blazor Webassembly uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-137">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="06f52-138">BlazorSunucu deneyimi için \*\* Blazor sunucu uygulaması\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-138">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="06f52-139">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-139">Select **Next**.</span></span>

   <span data-ttu-id="06f52-140">İki Blazor barındırma modeli, \* Blazor webassembly\* ve \* Blazor sunucu\*hakkında bilgi için bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="06f52-140">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="06f52-141">Aşağıdaki konfigürasyonları onaylayın:</span><span class="sxs-lookup"><span data-stu-id="06f52-141">Confirm the following configurations:</span></span>

   * <span data-ttu-id="06f52-142">**Hedef Framework** , **.NET Core 3,1**olarak ayarlandı.</span><span class="sxs-lookup"><span data-stu-id="06f52-142">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="06f52-143">**Kimlik doğrulaması** **yok**olarak ayarlandı.</span><span class="sxs-lookup"><span data-stu-id="06f52-143">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="06f52-144">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-144">Select **Next**.</span></span>

1. <span data-ttu-id="06f52-145">**Proje adı** alanında, uygulamayı adlandırın `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="06f52-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="06f52-146">**Oluştur**'u seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-146">Select **Create**.</span></span>

1. <span data-ttu-id="06f52-147">**Run**  >  Uygulamayı hata *ayıklayıcı olmadan*çalıştırmak için**hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="06f52-148">Uygulamayı **Run**  >  *hata ayıklayıcıyla*çalıştırmak için uygulamayı**Başlat hata ayıklaması** veya Çalıştır (&#9654;) düğmesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="06f52-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="06f52-149">Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin.</span><span class="sxs-lookup"><span data-stu-id="06f52-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="06f52-150">Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="06f52-150">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="06f52-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="06f52-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="06f52-152">[.NET Core 3,1 SDK 'sının](https://dotnet.microsoft.com/download/dotnet-core/3.1)en son sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="06f52-152">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="06f52-153">SDK 'Yı daha önce yüklediyseniz, bir komut kabuğu 'nda aşağıdaki komutu yürüterek yüklü sürümünüzü belirleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="06f52-153">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="06f52-154">Bir Blazor weelsembly deneyimi için aşağıdaki komutları bir komut kabuğunda yürütün:</span><span class="sxs-lookup"><span data-stu-id="06f52-154">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="06f52-155">Bir Blazor sunucu deneyimi için aşağıdaki komutları bir komut kabuğunda yürütün:</span><span class="sxs-lookup"><span data-stu-id="06f52-155">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="06f52-156">İki Blazor barındırma modeli, \* Blazor webassembly\* ve \* Blazor sunucu\*hakkında bilgi için bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="06f52-156">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="06f52-157">Bir tarayıcıda öğesine gidin `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="06f52-157">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="06f52-158">Kenar çubuğu 'ndaki sekmelerde birden çok sayfa mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="06f52-158">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="06f52-159">Giriş Sayfası</span><span class="sxs-lookup"><span data-stu-id="06f52-159">Home</span></span>
* <span data-ttu-id="06f52-160">Sayaç</span><span class="sxs-lookup"><span data-stu-id="06f52-160">Counter</span></span>
* <span data-ttu-id="06f52-161">Verileri getir</span><span class="sxs-lookup"><span data-stu-id="06f52-161">Fetch data</span></span>

<span data-ttu-id="06f52-162">Sayaç sayfasında, bir sayfa yenileme olmadan sayacı artırmak için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="06f52-162">On the Counter page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="06f52-163">Bir Web sayfasındaki sayacı normal şekilde artırma, JavaScript yazmayı gerektirir, ancak ile Blazor C# kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="06f52-163">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="06f52-164">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="06f52-164">`Pages/Counter.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="06f52-165">`/counter`En üstteki yönergeyle belirtilen şekilde tarayıcıda için bir istek `@page` , `Counter` bileşenin içeriğini işlemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="06f52-165">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="06f52-166">Bileşenler, daha sonra, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılabilen işleme ağacının bellek içi gösterimine işlenir.</span><span class="sxs-lookup"><span data-stu-id="06f52-166">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="06f52-167">Düğme her seçildiğinde:</span><span class="sxs-lookup"><span data-stu-id="06f52-167">Each time the button is selected:</span></span>

* <span data-ttu-id="06f52-168">`onclick`Olay tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="06f52-168">The `onclick` event is fired.</span></span>
* <span data-ttu-id="06f52-169">`IncrementCount`Yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="06f52-169">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="06f52-170">`currentCount`Artırılır.</span><span class="sxs-lookup"><span data-stu-id="06f52-170">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="06f52-171">Bileşen yeniden işlenir.</span><span class="sxs-lookup"><span data-stu-id="06f52-171">The component is rendered again.</span></span>

<span data-ttu-id="06f52-172">Çalışma zamanı, yeni içeriği önceki içerikle karşılaştırır ve yalnızca değiştirilen içeriği Belge Nesne Modeli (DOM) öğesine uygular.</span><span class="sxs-lookup"><span data-stu-id="06f52-172">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="06f52-173">HTML sözdizimini kullanarak başka bir bileşene bileşen ekleyin.</span><span class="sxs-lookup"><span data-stu-id="06f52-173">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="06f52-174">Örneğin, bileşene `Counter` bir öğe ekleyerek bileşeni uygulamanın giriş sayfasına ekleyin `<Counter />` `Index` .</span><span class="sxs-lookup"><span data-stu-id="06f52-174">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="06f52-175">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="06f52-175">`Pages/Index.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="06f52-176">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="06f52-176">Run the app.</span></span> <span data-ttu-id="06f52-177">Giriş sayfası, bileşen tarafından sağlanmış kendi sayacıdır `Counter` .</span><span class="sxs-lookup"><span data-stu-id="06f52-177">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="06f52-178">Bileşen parametreleri, alt bileşende özellikler ayarlamanıza olanak tanıyan öznitelikler veya [alt içerik](xref:blazor/components/index#child-content)kullanılarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="06f52-178">Component parameters are specified using attributes or [child content](xref:blazor/components/index#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="06f52-179">Bileşene bir parametre eklemek için `Counter` bileşenin `@code` bloğunu güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="06f52-179">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="06f52-180">Özniteliği ile için bir public özelliği ekleyin `IncrementAmount` [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) .</span><span class="sxs-lookup"><span data-stu-id="06f52-180">Add a public property for `IncrementAmount` with a [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
* <span data-ttu-id="06f52-181">`IncrementCount` `IncrementAmount` Değerini değerini artırdığınızda kullanmak için yöntemini değiştirin `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="06f52-181">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="06f52-182">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="06f52-182">`Pages/Counter.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="06f52-183">`IncrementAmount` `Index` `<Counter>` Özniteliği kullanarak bileşenin öğesinde öğesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="06f52-183">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="06f52-184">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="06f52-184">`Pages/Index.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="06f52-185">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="06f52-185">Run the app.</span></span> <span data-ttu-id="06f52-186">`Index`Bileşenin, düğmenin her seçililişinde on ile artan kendi sayacı vardır.</span><span class="sxs-lookup"><span data-stu-id="06f52-186">The `Index` component has its own counter that increments by ten each time the button is selected.</span></span> <span data-ttu-id="06f52-187">İçindeki `Counter` bileşen ( `Pages/Counter.razor` ), `/counter` bir tane tarafından arttırmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="06f52-187">The `Counter` component (`Pages/Counter.razor`) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="06f52-188">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="06f52-188">Next steps</span></span>

<span data-ttu-id="06f52-189">Adım Blazor adım bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="06f52-189">Build a Blazor app step-by-step:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="06f52-190">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="06f52-190">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
