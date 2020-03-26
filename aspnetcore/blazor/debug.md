---
title: Hata ayıklama ASP.NET Core Blazor WebAssembly
author: guardrex
description: Blazor uygulamalarda hata ayıklamayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 5dbc900ab68682068a7f9e3ffdaabef89a0c7798
ms.sourcegitcommit: 6ffb583991d6689326605a24565130083a28ef85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80306482"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="54333-103">Hata ayıklama ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="54333-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="54333-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="54333-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="54333-105"> WebAssembly uygulamalarına, Kmıum tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak hata ayıklanabilir.</span><span class="sxs-lookup"><span data-stu-id="54333-105"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="54333-106">Alternatif olarak, Visual Studio veya Visual Studio Code kullanarak uygulamanızda hata ayıklayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="54333-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="54333-107">Kullanılabilir senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="54333-107">Available scenarios include:</span></span>

* <span data-ttu-id="54333-108">Kesme noktaları ayarlayın ve kaldırın.</span><span class="sxs-lookup"><span data-stu-id="54333-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="54333-109">Visual Studio 'da hata ayıklama desteğiyle uygulamayı çalıştırın ve Visual Studio Code (<kbd>F5</kbd> support).</span><span class="sxs-lookup"><span data-stu-id="54333-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="54333-110">Kod üzerinden tek adımlı (<kbd>F10</kbd>).</span><span class="sxs-lookup"><span data-stu-id="54333-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="54333-111">Visual Studio veya Visual Studio Code 'de <kbd>F8</kbd> ile kod yürütmeyi bir tarayıcıda veya <kbd>F5</kbd> ile sürdürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="54333-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="54333-112">*Yereller* görünümü ' nde yerel değişkenlerin değerlerini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="54333-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="54333-113">JavaScript 'ten .NET 'e ve .NET 'ten JavaScript 'e gidecek çağrı zincirleri dahil olmak üzere çağrı yığınına bakın.</span><span class="sxs-lookup"><span data-stu-id="54333-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="54333-114">Şimdilik şunları *yapamazsınız*:</span><span class="sxs-lookup"><span data-stu-id="54333-114">For now, you *can't*:</span></span>

* <span data-ttu-id="54333-115">Dizileri inceleyin.</span><span class="sxs-lookup"><span data-stu-id="54333-115">Inspect arrays.</span></span>
* <span data-ttu-id="54333-116">Üyeleri incelemek için üzerine gelin.</span><span class="sxs-lookup"><span data-stu-id="54333-116">Hover to inspect members.</span></span>
* <span data-ttu-id="54333-117">Yönetilen kodun içine veya dışına hata ayıklama adımı.</span><span class="sxs-lookup"><span data-stu-id="54333-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="54333-118">Değer türlerini incelemek için tam destek vardır.</span><span class="sxs-lookup"><span data-stu-id="54333-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="54333-119">İşlenmemiş özel durumların üzerine bölün.</span><span class="sxs-lookup"><span data-stu-id="54333-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="54333-120">Uygulamanın başlatılması sırasında isabet kesme noktaları.</span><span class="sxs-lookup"><span data-stu-id="54333-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="54333-121">Hizmet çalışanıyla bir uygulamada hata ayıklama.</span><span class="sxs-lookup"><span data-stu-id="54333-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="54333-122">Yaklaşan sürümlerde hata ayıklama deneyimini iyileştirmeye devam edeceğiz.</span><span class="sxs-lookup"><span data-stu-id="54333-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="54333-123">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="54333-123">Prerequisites</span></span>

<span data-ttu-id="54333-124">Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="54333-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="54333-125">Microsoft Edge (sürüm 80 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="54333-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="54333-126">Google Chrome (sürüm 70 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="54333-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="54333-127">Visual Studio ve Visual Studio Code için hata ayıklamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="54333-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="54333-128">Hata ayıklama, ASP.NET Core 3,2 Preview 3 veya üzeri Blazor WebAssembly proje şablonu kullanılarak oluşturulan yeni projeler için otomatik olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="54333-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template.</span></span>

<span data-ttu-id="54333-129">Var olan bir Blazor Weelsembly uygulamasında hata ayıklamayı etkinleştirmek için, başlangıç projesindeki *Launchsettings. JSON* dosyasını her bir başlatma profiline aşağıdaki `inspectUri` özelliğini içerecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="54333-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="54333-130">' Yi güncelleştirdikten sonra, *Launchsettings. JSON* dosyası aşağıdaki örneğe benzer şekilde görünmelidir:</span><span class="sxs-lookup"><span data-stu-id="54333-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="54333-131">`inspectUri` özelliği:</span><span class="sxs-lookup"><span data-stu-id="54333-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="54333-132">IDE 'nin, uygulamanın Blazor WebAssembly uygulaması olduğunu algılamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="54333-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="54333-133">Betik hata ayıklama altyapısına Blazorhata ayıklama proxy 'si aracılığıyla tarayıcıya bağlanmasını söyler.</span><span class="sxs-lookup"><span data-stu-id="54333-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="54333-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54333-134">Visual Studio</span></span>

<span data-ttu-id="54333-135">Visual Studio 'da Blazor WebAssembly uygulamasında hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="54333-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="54333-136">Visual Studio 2019 16,6 ' nin (Preview 2 veya üzeri) [en son önizleme sürümünü yüklediğinizden](https://visualstudio.com/preview) emin olun.</span><span class="sxs-lookup"><span data-stu-id="54333-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="54333-137">WebAssembly uygulaması Blazor barındırılan yeni bir ASP.NET Core oluşturun.</span><span class="sxs-lookup"><span data-stu-id="54333-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="54333-138">Uygulamayı hata ayıklayıcıda çalıştırmak için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="54333-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="54333-139">`IncrementCount` yönteminde *Counter. Razor* içinde bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="54333-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="54333-140">**Sayaç** sekmesine gidin ve kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="54333-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="54333-142">Yereller penceresindeki `currentCount` alanının değerini gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="54333-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Yerelleri görüntüle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="54333-144">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="54333-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="54333-145">Blazor WebAssembly uygulamanızda hata ayıklarken, sunucu kodunuzda hata ayıklama de yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="54333-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="54333-146">`OnInitializedAsync`içindeki *Fetchdata. Razor* sayfasında bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="54333-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="54333-147">`Get` eylemi yöntemindeki `WeatherForecastController` bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="54333-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="54333-148">Sunucuya HTTP isteği vermeden hemen önce `FetchData` bileşenindeki ilk kesme noktasına gitmek için **verileri getir** sekmesine gidin:</span><span class="sxs-lookup"><span data-stu-id="54333-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Veri getirme verilerini ayıklama](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="54333-150">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve ardından `WeatherForecastController`sunucudaki kesme noktasına gidin:</span><span class="sxs-lookup"><span data-stu-id="54333-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Hata ayıklama sunucusu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="54333-152">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve hava durumu tahmin tablosunun işlenmiş olduğunu görün.</span><span class="sxs-lookup"><span data-stu-id="54333-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

## <a name="visual-studio-code"></a><span data-ttu-id="54333-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="54333-153">Visual Studio Code</span></span>

<span data-ttu-id="54333-154">Visual Studio Code bir Blazor WebAssembly uygulamasında hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="54333-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="54333-155">Uzantıyı ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını `true``debug.javascript.usePreview` olarak ayarlayın. [ C# ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)</span><span class="sxs-lookup"><span data-stu-id="54333-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Uzantıları](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS önizleme hata ayıklayıcısı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="54333-158">Mevcut bir Blazor WebAssembly uygulamasını hata ayıklama etkinken açın.</span><span class="sxs-lookup"><span data-stu-id="54333-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="54333-159">Hata ayıklamayı etkinleştirmek için ek kurulum gerekli olduğunda aşağıdaki bildirime sahipseniz, doğru uzantıların yüklü olduğunu ve JavaScript önizlemesi hata ayıklamanın etkin olduğunu doğrulayın ve ardından pencereyi yeniden yükleyin:</span><span class="sxs-lookup"><span data-stu-id="54333-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Ek kurulum gerekli](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="54333-161">Derleme ve hata ayıklama için gerekli varlıkları uygulamaya eklemek için bir bildirim sunulur.</span><span class="sxs-lookup"><span data-stu-id="54333-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="54333-162">**Evet**' i seçin:</span><span class="sxs-lookup"><span data-stu-id="54333-162">Select **Yes**:</span></span>

     ![Gerekli varlıkları Ekle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="54333-164">Uygulamanın hata ayıklayıcıda başlatılması iki adımlı bir işlemdir:</span><span class="sxs-lookup"><span data-stu-id="54333-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="54333-165">1 \.</span><span class="sxs-lookup"><span data-stu-id="54333-165">1\.</span></span> <span data-ttu-id="54333-166">**İlk**olarak, **.NET Core başlatma (Blazor tek başına)** başlatma yapılandırmasını kullanarak uygulamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="54333-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="54333-167">2 \.</span><span class="sxs-lookup"><span data-stu-id="54333-167">2\.</span></span> <span data-ttu-id="54333-168">**Uygulama başladıktan sonra**, Chrome başlatma yapılandırması **'Nda .NET Core hata ayıklama Blazor Web derlemesini** kullanarak tarayıcıyı başlatın (Chrome gerekir).</span><span class="sxs-lookup"><span data-stu-id="54333-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="54333-169">Chrome yerine Edge kullanmak için, *. vscode/Launch. JSON* içindeki başlatma yapılandırmasının `type` `pwa-chrome` `pwa-edge`olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="54333-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-edge`.</span></span>

1. <span data-ttu-id="54333-170">`Counter` bileşenindeki `IncrementCount` yöntemde bir kesme noktası ayarlayın ve ardından kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="54333-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="54333-172">Tarayıcıda hata ayıkla</span><span class="sxs-lookup"><span data-stu-id="54333-172">Debug in the browser</span></span>

1. <span data-ttu-id="54333-173">Geliştirme ortamında uygulamanın hata ayıklama derlemesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="54333-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="54333-174"><kbd>Shıft</kbd>+<kbd>alt</kbd>+<kbd>D</kbd>tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="54333-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="54333-175">Tarayıcı, uzaktan hata ayıklama etkinken çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="54333-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="54333-176">Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="54333-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="54333-177">Hata sayfası, Blazor hata ayıklama proxy 'sinin uygulamaya bağlanabilmesi için hata ayıklama bağlantı noktası açıkken tarayıcıyı çalıştırmaya yönelik yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="54333-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="54333-178">*Tüm tarayıcı örneklerini kapatın* ve belirtildiği şekilde tarayıcıyı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="54333-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="54333-179">Tarayıcı uzaktan hata ayıklama etkinken çalışırken hata ayıklama klavye kısayolu yeni bir hata ayıklayıcı sekmesi açar. Bir süre sonra, **kaynaklar** sekmesi uygulamadaki .net derlemelerinin listesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="54333-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="54333-180">Her bir derlemeyi genişletin ve hata ayıklama için kullanılabilen *. cs*/ *. Razor* kaynak dosyalarını bulun.</span><span class="sxs-lookup"><span data-stu-id="54333-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="54333-181">Kesme noktaları ayarlayın, uygulamanın sekmesine geri dönün ve kod yürütüldüğünde kesme noktaları isabet edilir.</span><span class="sxs-lookup"><span data-stu-id="54333-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="54333-182">Kesme noktası isabet ettikten sonra, kod üzerinden tek adımlı (<kbd>F10</kbd><kbd>) ve</kbd>kod yürütme işlemini normal şekilde yapın.</span><span class="sxs-lookup"><span data-stu-id="54333-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="54333-183">, [Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişletgetiren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler.</span><span class="sxs-lookup"><span data-stu-id="54333-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="54333-184">Klavye kısayolunun hata ayıklaması basıldığında Blazor, ara sunucu üzerindeki Chrome DevTools 'u gösterir.</span><span class="sxs-lookup"><span data-stu-id="54333-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="54333-185">Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).</span><span class="sxs-lookup"><span data-stu-id="54333-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="54333-186">Tarayıcı kaynağı eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="54333-186">Browser source maps</span></span>

<span data-ttu-id="54333-187">Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="54333-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="54333-188">Ancak, Blazor Şu anda doğrudan C# JavaScript/, ile eşlenmiyor.</span><span class="sxs-lookup"><span data-stu-id="54333-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="54333-189">Bunun yerine, Blazor, kaynak haritaları ilgili değil, tarayıcı içinde Il yorumu yapar.</span><span class="sxs-lookup"><span data-stu-id="54333-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="54333-190">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="54333-190">Troubleshoot</span></span>

<span data-ttu-id="54333-191">Hatalar halinde çalıştırıyorsanız, aşağıdaki ipucu yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="54333-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="54333-192">**Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın.</span><span class="sxs-lookup"><span data-stu-id="54333-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="54333-193">Konsolunda, tüm kesme noktalarını kaldırmak için `localStorage.clear()` yürütün.</span><span class="sxs-lookup"><span data-stu-id="54333-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
