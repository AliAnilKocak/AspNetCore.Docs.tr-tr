---
title: ASP.NET Core Blazor kullanmaya başlama
author: guardrex
description: Seçtiğiniz araç ile Blazor bir uygulama oluşturarak Blazor kullanmaya başlayın.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 642881b5400a70a99f6e7e262d2a2f1038389ce7
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726849"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="071a4-103">ASP.NET Core Blazor kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="071a4-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="071a4-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="071a4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="071a4-105">Blazorkullanmaya başlayın:</span><span class="sxs-lookup"><span data-stu-id="071a4-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="071a4-106">[.NET Core 3,1 SDK 'sını](https://dotnet.microsoft.com/download/dotnet-core/3.1)yükler.</span><span class="sxs-lookup"><span data-stu-id="071a4-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="071a4-107">İsteğe bağlı olarak [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) şablonunu yükler:</span><span class="sxs-lookup"><span data-stu-id="071a4-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="071a4-108">[.NET Core 3,1 veya üzeri (Önizleme) SDK 'sını](https://dotnet.microsoft.com/download/dotnet-core/3.1)yükler.</span><span class="sxs-lookup"><span data-stu-id="071a4-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="071a4-109">Komut kabuğu 'nda aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="071a4-109">Run the following command in a command shell.</span></span> <span data-ttu-id="071a4-110">[Microsoft. AspNetCore.Blazor. Şablon](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) paketinin önizleme sürümü varsa Blazor WebAssembly önizleme aşamasındadır.</span><span class="sxs-lookup"><span data-stu-id="071a4-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="071a4-111">Araç seçiminiz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="071a4-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="071a4-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="071a4-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="071a4-113">1 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-113">1\.</span></span> <span data-ttu-id="071a4-114">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019 sürüm 16,4 veya üstünü yükledikten sonra](https://visualstudio.microsoft.com/vs/preview/) .</span><span class="sxs-lookup"><span data-stu-id="071a4-114">Install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="071a4-115">2 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-115">2\.</span></span> <span data-ttu-id="071a4-116">Yeni bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="071a4-116">Create a new project.</span></span>

   <span data-ttu-id="071a4-117">3 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-117">3\.</span></span> <span data-ttu-id="071a4-118">**Blazor uygulama**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="071a4-118">Select **Blazor App**.</span></span> <span data-ttu-id="071a4-119">**İleri**'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="071a4-119">Select **Next**.</span></span>

   <span data-ttu-id="071a4-120">4 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-120">4\.</span></span> <span data-ttu-id="071a4-121">**Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="071a4-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="071a4-122">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="071a4-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="071a4-123">Seçin **oluşturma**.</span><span class="sxs-lookup"><span data-stu-id="071a4-123">Select **Create**.</span></span>

   <span data-ttu-id="071a4-124">5 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-124">5\.</span></span> <span data-ttu-id="071a4-125">Blazor Weelsembly deneyimi için, **Webassembly uygulama şablonunuBlazor** seçin.</span><span class="sxs-lookup"><span data-stu-id="071a4-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="071a4-126">Blazor sunucusu deneyimi için **Blazor sunucusu uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="071a4-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="071a4-127">Seçin **oluşturma**.</span><span class="sxs-lookup"><span data-stu-id="071a4-127">Select **Create**.</span></span> <span data-ttu-id="071a4-128">Blazor, *Blazor sunucusu* ve *Blazor webassembly*'yi barındıran iki hakkında bilgi için bkz. <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="071a4-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="071a4-129">6 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-129">6\.</span></span> <span data-ttu-id="071a4-130">Uygulamayı çalıştırmak için **Ctrl**+**F5** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="071a4-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="071a4-131">Blazor Visual Studio uzantısını ASP.NET Core Blazor önceki bir önizleme sürümü için yüklediyseniz (Preview 6 veya daha önceki bir sürümü), uzantıyı kaldırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="071a4-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="071a4-132">Blazor şablonlarının bir komut kabuğu 'na yüklenmesi artık Visual Studio 'daki şablonları yüzeye eklemek yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="071a4-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="071a4-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="071a4-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="071a4-134">1 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-134">1\.</span></span> <span data-ttu-id="071a4-135">[Visual Studio Code](https://code.visualstudio.com/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="071a4-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="071a4-136">2 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-136">2\.</span></span> <span data-ttu-id="071a4-137">En son [ C# Visual Studio Code uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)yükler.</span><span class="sxs-lookup"><span data-stu-id="071a4-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="071a4-138">3 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-138">3\.</span></span> <span data-ttu-id="071a4-139">Blazor Weelsembly deneyimi için, komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="071a4-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="071a4-140">Blazor sunucusu deneyimi için, komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="071a4-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="071a4-141">Blazor, *Blazor sunucusu* ve *Blazor webassembly*'yi barındıran iki hakkında bilgi için bkz. <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="071a4-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="071a4-142">4 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-142">4\.</span></span> <span data-ttu-id="071a4-143">Visual Studio Code 'de *WebApplication1* klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="071a4-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="071a4-144">5 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-144">5\.</span></span> <span data-ttu-id="071a4-145">Bir Blazor Server projesi için IDE, projeyi derlemek ve hatalarını ayıklamak için varlık eklemenizi ister.</span><span class="sxs-lookup"><span data-stu-id="071a4-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="071a4-146">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="071a4-146">Select **Yes**.</span></span>

   <span data-ttu-id="071a4-147">6 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-147">6\.</span></span> <span data-ttu-id="071a4-148">Blazor sunucusu uygulaması kullanıyorsanız, Visual Studio Code hata ayıklayıcıyı kullanarak uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="071a4-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="071a4-149">Blazor WebAssembly uygulaması kullanıyorsanız, uygulamanın proje klasöründen `dotnet run` yürütün.</span><span class="sxs-lookup"><span data-stu-id="071a4-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="071a4-150">7 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-150">7\.</span></span> <span data-ttu-id="071a4-151">Bir tarayıcıda `https://localhost:5001`' a gidin.</span><span class="sxs-lookup"><span data-stu-id="071a4-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="071a4-152">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="071a4-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="071a4-153">1 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-153">1\.</span></span> <span data-ttu-id="071a4-154">[Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="071a4-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="071a4-155">2 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-155">2\.</span></span> <span data-ttu-id="071a4-156">**Yeni çözüm** > **Dosya** seçin veya yeni bir **Proje**oluşturun.</span><span class="sxs-lookup"><span data-stu-id="071a4-156">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="071a4-157">3 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-157">3\.</span></span> <span data-ttu-id="071a4-158">Kenar çubuğunda **.NET Core** > **uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="071a4-158">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="071a4-159">4 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-159">4\.</span></span> <span data-ttu-id="071a4-160">**Blazor sunucusu uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="071a4-160">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="071a4-161">Şu anda yalnızca Blazor sunucusu şablonu Mac için Visual Studio kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="071a4-161">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="071a4-162">Blazor Weelsembly deneyimi için **.NET Core CLI** sekmesindeki yönergeleri izleyin. Blazor sunucusu şablonunu seçtikten sonra **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="071a4-162">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="071a4-163">Blazor, *Blazor sunucusu* ve *Blazor webassembly*'yi barındıran iki hakkında bilgi için bkz. <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="071a4-163">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="071a4-164">5 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-164">5\.</span></span> <span data-ttu-id="071a4-165">**Hedef Framework 'ü** **.NET Core 3,1** olarak ayarlayın ve **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="071a4-165">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="071a4-166">6 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-166">6\.</span></span> <span data-ttu-id="071a4-167">**Proje adı** alanında, uygulamayı `WebApplication1`olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="071a4-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="071a4-168">Seçin **oluşturma**.</span><span class="sxs-lookup"><span data-stu-id="071a4-168">Select **Create**.</span></span>

   <span data-ttu-id="071a4-169">7 \.</span><span class="sxs-lookup"><span data-stu-id="071a4-169">7\.</span></span> <span data-ttu-id="071a4-170">Uygulamayı *hata ayıklayıcısı olmadan*çalıştırmak Için **hata ayıklama olmadan** **Çalıştır > Çalıştır** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="071a4-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="071a4-171">Uygulamayı hata *ayıklayıcıyla*çalıştırmak Için, **hata ayıklamayı Başlat** ile uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="071a4-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="071a4-172">Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin.</span><span class="sxs-lookup"><span data-stu-id="071a4-172">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="071a4-173">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="071a4-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="071a4-174">Blazor Weelsembly deneyimi için, komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="071a4-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="071a4-175">Blazor sunucusu deneyimi için, komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="071a4-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="071a4-176">Blazor, *Blazor sunucusu* ve *Blazor webassembly*'yi barındıran iki hakkında bilgi için bkz. <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="071a4-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="071a4-177">Bir tarayıcıda `https://localhost:5001`' a gidin.</span><span class="sxs-lookup"><span data-stu-id="071a4-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="071a4-178">Kenar çubuğu 'ndaki sekmelerde birden çok sayfa mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="071a4-178">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="071a4-179">Ana Sayfası</span><span class="sxs-lookup"><span data-stu-id="071a4-179">Home</span></span>
* <span data-ttu-id="071a4-180">Sayaç</span><span class="sxs-lookup"><span data-stu-id="071a4-180">Counter</span></span>
* <span data-ttu-id="071a4-181">Verileri getir</span><span class="sxs-lookup"><span data-stu-id="071a4-181">Fetch data</span></span>

<span data-ttu-id="071a4-182">Sayaç sayfasında, bir sayfa yenilemesi olmadan sayacı artırmak için **bana tıklama** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="071a4-182">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="071a4-183">Bir Web sayfasında normal olarak bir sayacı artırma, JavaScript yazmayı gerektirir, ancak kullanabilirsiniz C#Blazor.</span><span class="sxs-lookup"><span data-stu-id="071a4-183">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="071a4-184">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="071a4-184">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="071a4-185">Tarayıcıda `/counter` için bir istek, en üstteki `@page` yönergesi tarafından belirtilen şekilde `Counter` bileşeninin içeriğini işlemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="071a4-185">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="071a4-186">Bileşenler, daha sonra, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılabilen işleme ağacının bellek içi gösterimine işlenir.</span><span class="sxs-lookup"><span data-stu-id="071a4-186">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="071a4-187">**Bana tıklama** düğmesi her seçildiğinde:</span><span class="sxs-lookup"><span data-stu-id="071a4-187">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="071a4-188">`onclick` olayı tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="071a4-188">The `onclick` event is fired.</span></span>
* <span data-ttu-id="071a4-189">`IncrementCount` yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="071a4-189">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="071a4-190">`currentCount` artırılır.</span><span class="sxs-lookup"><span data-stu-id="071a4-190">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="071a4-191">Bileşen yeniden işlenir.</span><span class="sxs-lookup"><span data-stu-id="071a4-191">The component is rendered again.</span></span>

<span data-ttu-id="071a4-192">Çalışma zamanı, yeni içeriği önceki içerikle karşılaştırır ve yalnızca değiştirilen içeriği Belge Nesne Modeli (DOM) öğesine uygular.</span><span class="sxs-lookup"><span data-stu-id="071a4-192">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="071a4-193">HTML sözdizimini kullanarak başka bir bileşene bileşen ekleyin.</span><span class="sxs-lookup"><span data-stu-id="071a4-193">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="071a4-194">Örneğin, `Index` bileşenine bir `<Counter />` öğesi ekleyerek `Counter` bileşenini uygulamanın giriş sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="071a4-194">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="071a4-195">*Pages/Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="071a4-195">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="071a4-196">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="071a4-196">Run the app.</span></span> <span data-ttu-id="071a4-197">Giriş sayfasının `Counter` bileşeni tarafından kendi sayacı vardır.</span><span class="sxs-lookup"><span data-stu-id="071a4-197">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="071a4-198">Bileşen parametreleri, alt bileşende özellikler ayarlamanıza olanak tanıyan öznitelikler veya [alt içerik](xref:blazor/components#child-content)kullanılarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="071a4-198">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="071a4-199">`Counter` bileşenine bir parametre eklemek için, bileşenin `@code` bloğunu güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="071a4-199">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="071a4-200">`[Parameter]` özniteliğiyle `IncrementAmount` için ortak özellik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="071a4-200">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="071a4-201">`currentCount`değerini artırdığınızda `IncrementAmount` kullanmak için `IncrementCount` yöntemini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="071a4-201">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="071a4-202">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="071a4-202">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="071a4-203">`Index` bileşenin `<Counter>` öğesindeki bir özniteliği kullanarak `IncrementAmount` belirtin.</span><span class="sxs-lookup"><span data-stu-id="071a4-203">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="071a4-204">*Pages/Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="071a4-204">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="071a4-205">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="071a4-205">Run the app.</span></span> <span data-ttu-id="071a4-206">`Index` bileşeni, **bana tıklama** düğmesi seçildiğinde her seferinde on ile artan kendi sayacıdır.</span><span class="sxs-lookup"><span data-stu-id="071a4-206">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="071a4-207">`/counter` `Counter` bileşeni (*Counter. Razor*), bir tarafından arttırmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="071a4-207">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="071a4-208">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="071a4-208">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="071a4-209">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="071a4-209">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
