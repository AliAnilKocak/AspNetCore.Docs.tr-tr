---
title: ASP.NET Core Blazor kullanmaya başlama
author: guardrex
description: Tercih ettiğiniz araç ile bir Blazor uygulaması oluşturarak Blazor kullanmaya başlayın.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/get-started
ms.openlocfilehash: cce91b6332295f77c639f881fe342b625fee7fca
ms.sourcegitcommit: 92c901c7f32ee9efb335d99ec4c3add2cc9f3142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71025515"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="ab7f7-103">ASP.NET Core Blazor kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="ab7f7-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="ab7f7-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="ab7f7-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ab7f7-105">Blazor kullanmaya başlama:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="ab7f7-106">En son [.NET Core 3,0 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-106">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="ab7f7-107">Komut kabuğu 'nda aşağıdaki komutu çalıştırarak Blazor şablonlarını yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview9.19457.4
   ```

1. <span data-ttu-id="ab7f7-108">Araç seçiminiz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ab7f7-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab7f7-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="ab7f7-110">1 \.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-110">1\.</span></span> <span data-ttu-id="ab7f7-111">**ASP.net ve Web geliştirme** iş yüküyle en son [Visual Studio önizlemesini](https://visualstudio.com/vs/preview) yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-111">Install the latest [Visual Studio preview](https://visualstudio.com/vs/preview) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="ab7f7-112">2 \.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-112">2\.</span></span> <span data-ttu-id="ab7f7-113">Yeni bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-113">Create a new project.</span></span>

   <span data-ttu-id="ab7f7-114">3 \.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-114">3\.</span></span> <span data-ttu-id="ab7f7-115">**Blazor uygulamasını**seçin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-115">Select **Blazor App**.</span></span> <span data-ttu-id="ab7f7-116">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-116">Select **Next**.</span></span>

   <span data-ttu-id="ab7f7-117">4 \.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-117">4\.</span></span> <span data-ttu-id="ab7f7-118">**Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="ab7f7-119">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="ab7f7-120">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-120">Select **Create**.</span></span>

   <span data-ttu-id="ab7f7-121">5 \.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-121">5\.</span></span> <span data-ttu-id="ab7f7-122">Blazor WebAssembly deneyimi için **Blazor Webassembly uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-122">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="ab7f7-123">Blazor sunucu deneyimi için **Blazor Server uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-123">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="ab7f7-124">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-124">Select **Create**.</span></span> <span data-ttu-id="ab7f7-125">İki Blazor barındırma modeli, *Blazor Server* ve *Blazor webassembly*hakkında bilgi için bkz <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-125">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="ab7f7-126">6 \.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-126">6\.</span></span> <span data-ttu-id="ab7f7-127">Uygulamayı çalıştırmak için **F5** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-127">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="ab7f7-128">ASP.NET Core Blazor 'nin önceki bir önizleme sürümü için Blazor Visual Studio uzantısı 'nı yüklediyseniz (Preview 6 veya daha önceki bir sürümü), uzantıyı kaldırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-128">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="ab7f7-129">Blazor şablonlarının bir komut kabuğu 'na yüklenmesi artık Visual Studio 'daki şablonları yüzey için yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-129">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ab7f7-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ab7f7-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="ab7f7-131">1 \.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-131">1\.</span></span> <span data-ttu-id="ab7f7-132">[Visual Studio Code](https://code.visualstudio.com/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-132">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="ab7f7-133">2 \.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-133">2\.</span></span> <span data-ttu-id="ab7f7-134">En son [ C# Visual Studio Code uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)yükler.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-134">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="ab7f7-135">3 \.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-135">3\.</span></span> <span data-ttu-id="ab7f7-136">Bir Blazor WebAssembly deneyimi için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-136">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="ab7f7-137">Bir Blazor sunucu deneyimi için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-137">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="ab7f7-138">İki Blazor barındırma modeli, *Blazor Server* ve *Blazor webassembly*hakkında bilgi için bkz <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-138">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="ab7f7-139">4 \.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-139">4\.</span></span> <span data-ttu-id="ab7f7-140">Visual Studio Code 'de *WebApplication1* klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-140">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="ab7f7-141">5 \.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-141">5\.</span></span> <span data-ttu-id="ab7f7-142">Bir Blazor Server projesi için IDE, projeyi derlemek ve hatalarını ayıklamak için varlık eklemenizi ister.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-142">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="ab7f7-143">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-143">Select **Yes**.</span></span>

   <span data-ttu-id="ab7f7-144">6 \.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-144">6\.</span></span> <span data-ttu-id="ab7f7-145">Bir Blazor Server uygulaması kullanıyorsanız, Visual Studio Code hata ayıklayıcıyı kullanarak uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-145">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="ab7f7-146">Blazor webassembly uygulaması kullanılıyorsa, uygulamanın proje klasöründen `dotnet run` yürütün.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-146">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="ab7f7-147">7 \.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-147">7\.</span></span> <span data-ttu-id="ab7f7-148">Bir tarayıcıda öğesine `https://localhost:5001`gidin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-148">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ab7f7-149">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ab7f7-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="ab7f7-150">Bir Blazor Weelsembly deneyimi için komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-150">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="ab7f7-151">Bir Blazor sunucu deneyimi için aşağıdaki komutları bir komut kabuğunda yürütün:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-151">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="ab7f7-152">İki Blazor barındırma modeli, *Blazor Server* ve *Blazor webassembly*hakkında bilgi için bkz <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-152">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="ab7f7-153">Bir tarayıcıda öğesine `https://localhost:5001`gidin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-153">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="ab7f7-154">Kenar çubuğu 'ndaki sekmelerde birden çok sayfa mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-154">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="ab7f7-155">Ana Sayfası</span><span class="sxs-lookup"><span data-stu-id="ab7f7-155">Home</span></span>
* <span data-ttu-id="ab7f7-156">Sayaç</span><span class="sxs-lookup"><span data-stu-id="ab7f7-156">Counter</span></span>
* <span data-ttu-id="ab7f7-157">Verileri getir</span><span class="sxs-lookup"><span data-stu-id="ab7f7-157">Fetch data</span></span>

<span data-ttu-id="ab7f7-158">Sayaç sayfasında, bir sayfa yenilemesi olmadan sayacı artırmak için **bana tıklama** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-158">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="ab7f7-159">Bir Web sayfasında normal olarak bir sayacı artırma, JavaScript yazmayı gerektirir, ancak Razor bileşenleri kullanarak C#daha iyi bir yaklaşım sağlar.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-159">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="ab7f7-160">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-160">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="ab7f7-161">En üstteki `/counter` `@page` yönergeyle belirtilen şekilde tarayıcıda için bir istek, `Counter` bileşenin içeriğini işlemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-161">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="ab7f7-162">Bileşenler, daha sonra, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılabilen işleme ağacının bellek içi gösterimine işlenir.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-162">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="ab7f7-163">**Bana tıklama** düğmesi her seçildiğinde:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-163">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="ab7f7-164">`onclick` Olay tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-164">The `onclick` event is fired.</span></span>
* <span data-ttu-id="ab7f7-165">`IncrementCount` Yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-165">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="ab7f7-166">`currentCount` Artırılır.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-166">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="ab7f7-167">Bileşen yeniden işlenir.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-167">The component is rendered again.</span></span>

<span data-ttu-id="ab7f7-168">Çalışma zamanı, yeni içeriği önceki içerikle karşılaştırır ve yalnızca değiştirilen içeriği Belge Nesne Modeli (DOM) öğesine uygular.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-168">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="ab7f7-169">HTML sözdizimini kullanarak başka bir bileşene bileşen ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-169">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="ab7f7-170">Örneğin, bileşene bir `Counter` `<Counter />` öğe `Index` ekleyerek bileşeni uygulamanın giriş sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-170">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="ab7f7-171">*Pages/Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-171">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="ab7f7-172">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-172">Run the app.</span></span> <span data-ttu-id="ab7f7-173">Giriş sayfası, `Counter` bileşen tarafından sağlanmış kendi sayacıdır.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-173">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="ab7f7-174">Bileşen parametreleri, alt bileşende özellikler ayarlamanıza olanak tanıyan öznitelikler veya [alt içerik](xref:blazor/components#child-content)kullanılarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-174">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="ab7f7-175">`Counter` Bileşene bir parametre eklemek için `@code` bileşenin bloğunu güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-175">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="ab7f7-176">Özniteliği ile için `IncrementAmount` bir public özelliği ekleyin. `[Parameter]`</span><span class="sxs-lookup"><span data-stu-id="ab7f7-176">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="ab7f7-177">Değerini değerini `IncrementAmount`artırdığınızda kullanmak için `IncrementCount` yöntemini değiştirin `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-177">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="ab7f7-178">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-178">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="ab7f7-179">Özniteliği kullanarak bileşenin`<Counter>`öğesindeöğesinibelirtin. `Index` `IncrementAmount`</span><span class="sxs-lookup"><span data-stu-id="ab7f7-179">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="ab7f7-180">*Pages/Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ab7f7-180">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="ab7f7-181">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-181">Run the app.</span></span> <span data-ttu-id="ab7f7-182">Bileşenin, bana tıklama düğmesi seçildiğinde her seferinde on ile artan kendi sayacı vardır. `Index`</span><span class="sxs-lookup"><span data-stu-id="ab7f7-182">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="ab7f7-183">' `Counter` De`/counter` bileşen (*Counter. Razor*), bir tane tarafından arttırmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="ab7f7-183">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ab7f7-184">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="ab7f7-184">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="ab7f7-185">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ab7f7-185">Additional resources</span></span>

* <xref:signalr/introduction>
