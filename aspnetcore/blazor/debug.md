---
title: Hata ayıklama ASP.NET Core Blazor
author: guardrex
description: Blazor uygulamalarında hata ayıklamayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2019
uid: blazor/debug
ms.openlocfilehash: ebdd015a8d1138d98d8fcd1b1f18b8053248e2bd
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168111"
---
# <a name="debug-aspnet-core-blazor"></a><span data-ttu-id="baac3-103">Hata ayıklama ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="baac3-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="baac3-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="baac3-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="baac3-105">Chrome 'da WebAssembly üzerinde çalışan Blazor WebAssembly uygulamalarında hata ayıklama için *erken* destek mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="baac3-105">*Early* support exists for debugging Blazor WebAssembly apps running on WebAssembly in Chrome.</span></span>

<span data-ttu-id="baac3-106">Hata ayıklayıcı özellikleri sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="baac3-106">Debugger capabilities are limited.</span></span> <span data-ttu-id="baac3-107">Kullanılabilir senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="baac3-107">Available scenarios include:</span></span>

* <span data-ttu-id="baac3-108">Kesme noktaları ayarlayın ve kaldırın.</span><span class="sxs-lookup"><span data-stu-id="baac3-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="baac3-109">Code veya özgeçmişi (`F10``F8`) kod yürütme aracılığıyla tek adımlı ().</span><span class="sxs-lookup"><span data-stu-id="baac3-109">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="baac3-110">*Yereller* görünümü ' nde, ve `int` `bool`türündeki `string`tüm yerel değişkenlerin değerlerini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="baac3-110">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="baac3-111">JavaScript 'ten .NET 'e ve .NET 'ten JavaScript 'e gidecek çağrı zincirleri dahil olmak üzere çağrı yığınına bakın.</span><span class="sxs-lookup"><span data-stu-id="baac3-111">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="baac3-112">Şunları *yapamazsınız*:</span><span class="sxs-lookup"><span data-stu-id="baac3-112">You *can't*:</span></span>

* <span data-ttu-id="baac3-113">`int` ,`string`Veya olmayan`bool`tüm Yereller için değerleri gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="baac3-113">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="baac3-114">Herhangi bir sınıf özelliklerinin veya alanının değerlerini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="baac3-114">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="baac3-115">Değerlerini görmek için değişkenlerin üzerine gelin.</span><span class="sxs-lookup"><span data-stu-id="baac3-115">Hover over variables to see their values.</span></span>
* <span data-ttu-id="baac3-116">Konsolunda ifadeleri değerlendirin.</span><span class="sxs-lookup"><span data-stu-id="baac3-116">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="baac3-117">Zaman uyumsuz çağrılar arasında adımla.</span><span class="sxs-lookup"><span data-stu-id="baac3-117">Step across async calls.</span></span>
* <span data-ttu-id="baac3-118">Diğer birçok sıradan hata ayıklama senaryosunu gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="baac3-118">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="baac3-119">Daha fazla hata ayıklama senaryosu geliştirmesi, mühendislik ekibinin bir odadır.</span><span class="sxs-lookup"><span data-stu-id="baac3-119">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="baac3-120">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="baac3-120">Prerequisites</span></span>

<span data-ttu-id="baac3-121">Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="baac3-121">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="baac3-122">Google Chrome (sürüm 70 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="baac3-122">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="baac3-123">Microsoft Edge Önizlemesi ([Edge geliştirme kanalı](https://www.microsoftedgeinsider.com))</span><span class="sxs-lookup"><span data-stu-id="baac3-123">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="baac3-124">Yordam</span><span class="sxs-lookup"><span data-stu-id="baac3-124">Procedure</span></span>

1. <span data-ttu-id="baac3-125">`Debug` Yapılandırmada bir Blazor webassembly uygulaması çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="baac3-125">Run a Blazor WebAssembly app in `Debug` configuration.</span></span> <span data-ttu-id="baac3-126">Seçeneği DotNet [Run](/dotnet/core/tools/dotnet-run) komutuna geçirin: `dotnet run --configuration Debug`. `--configuration Debug`</span><span class="sxs-lookup"><span data-stu-id="baac3-126">Pass the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="baac3-127">Uygulamaya tarayıcıda erişin.</span><span class="sxs-lookup"><span data-stu-id="baac3-127">Access the app in the browser.</span></span>
1. <span data-ttu-id="baac3-128">Klavye odağını geliştirici araçları paneline değil uygulamaya yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="baac3-128">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="baac3-129">Hata ayıklama başlatıldığında geliştirici araçları paneli kapatılabilir.</span><span class="sxs-lookup"><span data-stu-id="baac3-129">The developer tools panel can be closed when debugging is initiated.</span></span>
1. <span data-ttu-id="baac3-130">Aşağıdaki Blazor özgü klavye kısayolunu seçin:</span><span class="sxs-lookup"><span data-stu-id="baac3-130">Select the following Blazor-specific keyboard shortcut:</span></span>
   * <span data-ttu-id="baac3-131">`Shift+Alt+D`Windows/Linux 'ta</span><span class="sxs-lookup"><span data-stu-id="baac3-131">`Shift+Alt+D` on Windows/Linux</span></span>
   * <span data-ttu-id="baac3-132">`Shift+Cmd+D`macOS 'ta</span><span class="sxs-lookup"><span data-stu-id="baac3-132">`Shift+Cmd+D` on macOS</span></span>
1. <span data-ttu-id="baac3-133">Uzaktan hata ayıklama etkinken tarayıcıyı yeniden başlatmak için ekranda listelenen adımları izleyin.</span><span class="sxs-lookup"><span data-stu-id="baac3-133">Follow the steps listed on the screen to restart the browser with remote debugging enabled.</span></span>
1. <span data-ttu-id="baac3-134">Hata ayıklama oturumu başlatmak için aşağıdaki Blazor özgü klavye kısayolunu bir kez daha seçin:</span><span class="sxs-lookup"><span data-stu-id="baac3-134">Select the following Blazor-specific keyboard shortcut once again to start the debug session:</span></span>
   * <span data-ttu-id="baac3-135">`Shift+Alt+D`Windows/Linux 'ta</span><span class="sxs-lookup"><span data-stu-id="baac3-135">`Shift+Alt+D` on Windows/Linux</span></span>
   * <span data-ttu-id="baac3-136">`Shift+Cmd+D`macOS 'ta</span><span class="sxs-lookup"><span data-stu-id="baac3-136">`Shift+Cmd+D` on macOS</span></span>

## <a name="enable-remote-debugging"></a><span data-ttu-id="baac3-137">Uzaktan hata ayıklamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="baac3-137">Enable remote debugging</span></span>

<span data-ttu-id="baac3-138">Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası Chrome tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="baac3-138">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="baac3-139">Hata sayfası, Blazor hata ayıklama proxy 'sinin uygulamaya bağlanabilmesi için hata ayıklama bağlantı noktası açıkken Chrome çalıştırmaya yönelik yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="baac3-139">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="baac3-140">*Tüm Chrome örneklerini kapatın* ve belirtildiği şekilde Chrome 'u yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="baac3-140">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="baac3-141">Uygulamada hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="baac3-141">Debug the app</span></span>

<span data-ttu-id="baac3-142">Uzaktan hata ayıklama etkinken Chrome çalışmaya başladıktan sonra hata ayıklama klavye kısayolu yeni bir hata ayıklayıcı sekmesi açar. Bir süre sonra, **kaynaklar** sekmesi uygulamadaki .net derlemelerinin listesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="baac3-142">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="baac3-143">Her bir derlemeyi genişletin ve hata ayıklama için kullanılabilen *. cs*/ *. Razor* kaynak dosyalarını bulun.</span><span class="sxs-lookup"><span data-stu-id="baac3-143">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="baac3-144">Kesme noktaları ayarlayın, uygulamanın sekmesine geri dönün ve kod yürütüldüğünde kesme noktaları isabet edilir.</span><span class="sxs-lookup"><span data-stu-id="baac3-144">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="baac3-145">Kesme noktası isabet ettikten sonra, kod ya da özgeçmişi`F10`(`F8`) kodu üzerinden tek adımlı () kod yürütmeyi normal şekilde yapın.</span><span class="sxs-lookup"><span data-stu-id="baac3-145">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

<span data-ttu-id="baac3-146">Blazor, [Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişlettiğini içeren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler.</span><span class="sxs-lookup"><span data-stu-id="baac3-146">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="baac3-147">Klavye kısayolunun hata ayıklaması basıldığında Blazor, ara sunucu üzerindeki Chrome DevTools 'u işaret eder.</span><span class="sxs-lookup"><span data-stu-id="baac3-147">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="baac3-148">Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).</span><span class="sxs-lookup"><span data-stu-id="baac3-148">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="baac3-149">Tarayıcı kaynağı eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="baac3-149">Browser source maps</span></span>

<span data-ttu-id="baac3-150">Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="baac3-150">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="baac3-151">Ancak, Blazor Şu anda doğrudan C# JavaScript/, ile eşlenmiyor.</span><span class="sxs-lookup"><span data-stu-id="baac3-151">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="baac3-152">Bunun yerine Blazor, tarayıcı içinde Il yorumu yapar, bu nedenle kaynak haritaları ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="baac3-152">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshooting-tip"></a><span data-ttu-id="baac3-153">Sorun giderme ipucu</span><span class="sxs-lookup"><span data-stu-id="baac3-153">Troubleshooting tip</span></span>

<span data-ttu-id="baac3-154">Hatalar halinde çalıştırıyorsanız, aşağıdaki ipucu yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="baac3-154">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="baac3-155">**Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın.</span><span class="sxs-lookup"><span data-stu-id="baac3-155">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="baac3-156">Konsolunda, tüm kesme noktalarını `localStorage.clear()` kaldırmak için yürütün.</span><span class="sxs-lookup"><span data-stu-id="baac3-156">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
