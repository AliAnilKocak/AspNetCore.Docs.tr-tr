---
title: ASP.NET Core MVC ve Visual Studio ile çalışmaya başlama
author: rick-anderson
description: ASP.NET Core MVC ve Visual Studio ile çalışmaya başlama hakkında bilgi edinin.
ms.author: riande
ms.date: 10/07/2017
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 9d50607899058c887597a3d73198552d3ef5b020
ms.sourcegitcommit: c4572be5ebb301013a5698caf9b5572b76cb2e34
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52710094"
---
# <a name="get-started-with-aspnet-core-mvc-and-visual-studio"></a><span data-ttu-id="5a0bf-103">ASP.NET Core MVC ve Visual Studio ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="5a0bf-103">Get started with ASP.NET Core MVC and Visual Studio</span></span>

<span data-ttu-id="5a0bf-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5a0bf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="5a0bf-105">Bu öğreticinin 3 sürümü vardır:</span><span class="sxs-lookup"><span data-stu-id="5a0bf-105">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="5a0bf-106">macOS: [Mac için Visual Studio ile ASP.NET Core MVC uygulaması oluşturma](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="5a0bf-106">macOS: [Create an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="5a0bf-107">Windows: [Visual Studio ile bir ASP.NET Core MVC uygulaması oluşturma](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="5a0bf-107">Windows: [Create an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="5a0bf-108">macOS, Linux ve Windows: [Visual Studio Code ile ASP.NET Core MVC uygulaması oluşturma](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="5a0bf-108">macOS, Linux, and Windows: [Create an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span>

> [!NOTE]
> <span data-ttu-id="5a0bf-109">ASP.NET Core içindekiler tablosuna yönelik önerilmiş olan yeni bir yapının kullanılabilirliğini test ediyoruz.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-109">We’re testing the usability of a proposed new structure for the ASP.NET Core table of contents.</span></span>  <span data-ttu-id="5a0bf-110">Geçerli veya önerilen içindekiler tablosunda 7 farklı konuyu bulmaya ilişkin alıştırmayı denemek için vaktiniz varsa lütfen [çalışmaya katılmak için buraya tıklayın](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).</span><span class="sxs-lookup"><span data-stu-id="5a0bf-110">If you have a few minutes to try an exercise of finding 7 different topics in the current or proposed table of contents, please [click here to participate in the study](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).</span></span>

## <a name="install-visual-studio-and-net-core"></a><span data-ttu-id="5a0bf-111">Visual Studio ve .NET Core yükleyin</span><span class="sxs-lookup"><span data-stu-id="5a0bf-111">Install Visual Studio and .NET Core</span></span>

::: moniker range=">= aspnetcore-2.1"

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="5a0bf-112">Bir web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="5a0bf-112">Create a web app</span></span>

<span data-ttu-id="5a0bf-113">Visual Studio'dan seçin **Dosya > Yeni > Proje**.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-113">From Visual Studio, select  **File > New > Project**.</span></span>

![Dosya > Yeni > Proje](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="5a0bf-115">Tamamlamak **yeni proje** iletişim:</span><span class="sxs-lookup"><span data-stu-id="5a0bf-115">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="5a0bf-116">Sol bölmede, dokunun **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="5a0bf-116">In the left pane, tap **.NET Core**</span></span>
* <span data-ttu-id="5a0bf-117">Orta bölmede dokunun **ASP.NET Core Web uygulaması (.NET Core)**</span><span class="sxs-lookup"><span data-stu-id="5a0bf-117">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="5a0bf-118">(Kod kopyaladığınızda, ad alanı eşleşecek şekilde "MvcMovie" proje adı önemlidir.) "MvcMovie" proje adı</span><span class="sxs-lookup"><span data-stu-id="5a0bf-118">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="5a0bf-119">Dokunun **Tamam**</span><span class="sxs-lookup"><span data-stu-id="5a0bf-119">Tap **OK**</span></span>

![<span data-ttu-id="5a0bf-120">Yeni Proje iletişim kutusunda, sol bölmede, ASP.NET Core web'de .net core</span><span class="sxs-lookup"><span data-stu-id="5a0bf-120">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2-21.png)

<span data-ttu-id="5a0bf-121">Tamamlamak **yeni ASP.NET Core Web uygulaması (.NET Core) - MvcMovie** iletişim:</span><span class="sxs-lookup"><span data-stu-id="5a0bf-121">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="5a0bf-122">Sürüm Seçici açılan kutusunda seçin **ASP.NET Core 2.1**</span><span class="sxs-lookup"><span data-stu-id="5a0bf-122">In the version selector drop-down box select **ASP.NET Core 2.1**</span></span>
* <span data-ttu-id="5a0bf-123">Seçin **Web uygulaması (Model-View-Controller)**</span><span class="sxs-lookup"><span data-stu-id="5a0bf-123">Select **Web Application (Model-View-Controller)**</span></span>
* <span data-ttu-id="5a0bf-124">Dokunun **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-124">Tap **OK**.</span></span>

![<span data-ttu-id="5a0bf-125">Yeni Proje iletişim kutusunda, sol bölmede, ASP.NET Core web'de .net core</span><span class="sxs-lookup"><span data-stu-id="5a0bf-125">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="5a0bf-126">Visual Studio, yeni oluşturduğunuz MVC projesi için varsayılan bir şablon kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-126">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="5a0bf-127">Çalışan bir uygulamayı şu anda bir proje adı girerek ve bazı Seçenekler'i seçerek sizde.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-127">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="5a0bf-128">Bu temel başlangıç projesini ve başlatmak için iyi bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-128">This is a basic starter project, and it's a good place to start.</span></span>

<span data-ttu-id="5a0bf-129">Dokunun **F5** uygulamayı hata ayıklama modunda çalıştırmak için veya **Ctrl-F5** olmayan hata ayıklama modunda.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-129">Tap **F5** to run the app in debug mode or **Ctrl-F5** in non-debug mode.</span></span>
<span data-ttu-id="5a0bf-130"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![Uygulamayı çalıştırma](start-mvc/_static/1.png)</span><span class="sxs-lookup"><span data-stu-id="5a0bf-130"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![running app](start-mvc/_static/1.png)</span></span>

* <span data-ttu-id="5a0bf-131">Visual Studio başlatır [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ve uygulamanızı çalışır.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-131">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="5a0bf-132">Adres çubuğuna gösteren uyarı `localhost:port#` gibi bir şey `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-132">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a0bf-133">Çünkü `localhost` standart, yerel bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-133">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5a0bf-134">Visual Studio, bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-134">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="5a0bf-135">Yukarıdaki görüntüde, bağlantı noktası numarasını 5000'dir.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-135">In the image above, the port number is 5000.</span></span> <span data-ttu-id="5a0bf-136">Tarayıcı gösterir URL'de `localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-136">The URL in the browser shows `localhost:5000`.</span></span> <span data-ttu-id="5a0bf-137">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-137">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="5a0bf-138">Uygulamayı başlatma **Ctrl + F5** (hata ayıklama olmayan mod), kod değişiklikleri yapabilir, dosyayı kaydetmek, tarayıcıyı yenileyin ve kod değişikliklerini görebilirsiniz olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-138">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5a0bf-139">Geliştiricilerin çoğu, hızlı bir şekilde uygulamayı başlatın ve değişiklikleri görmek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-139">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="5a0bf-140">Uygulamada hata ayıklama veya hata ayıklama olmayan moddan başlatabilirsiniz **hata ayıklama** menü öğesi:</span><span class="sxs-lookup"><span data-stu-id="5a0bf-140">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menü hata ayıklama](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="5a0bf-142">Dokunarak uygulamayı hata ayıklaması yapabilirsiniz **IIS Express** düğmesi</span><span class="sxs-lookup"><span data-stu-id="5a0bf-142">You can debug the app by tapping the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="5a0bf-144">Varsayılan şablonu size çalışma **hakkında giriş** ve **kişi** bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-144">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="5a0bf-145">Yukarıdaki tarayıcı resimde, bu bağlantıları göstermez.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-145">The browser image above doesn't show these links.</span></span> <span data-ttu-id="5a0bf-146">Tarayıcınız boyutuna bağlı olarak, gösterileceğinin Gezinti simgesine tıklamanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-146">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![sağ üstteki gezinti simgesi](start-mvc/_static/2.png)

<span data-ttu-id="5a0bf-148">Hata ayıklama modunda çalışıyormuş dokunun **Shift + F5** hata ayıklamayı durdurmak için.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-148">If you were running in debug mode, tap **Shift-F5** to stop debugging.</span></span>

<span data-ttu-id="5a0bf-149">Bu öğreticinin sonraki bölümünde, biz MVC konusunda bilgi ve biraz kod yazmaya.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-149">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!INCLUDE [](~/includes/net-core-prereqs.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="5a0bf-150">Bir web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="5a0bf-150">Create a web app</span></span>

<span data-ttu-id="5a0bf-151">Visual Studio'dan seçin **Dosya > Yeni > Proje**.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-151">From Visual Studio, select  **File > New > Project**.</span></span>

![Dosya > Yeni > Proje](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="5a0bf-153">Tamamlamak **yeni proje** iletişim:</span><span class="sxs-lookup"><span data-stu-id="5a0bf-153">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="5a0bf-154">Sol bölmede, dokunun **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="5a0bf-154">In the left pane, tap **.NET Core**</span></span>
* <span data-ttu-id="5a0bf-155">Orta bölmede dokunun **ASP.NET Core Web uygulaması (.NET Core)**</span><span class="sxs-lookup"><span data-stu-id="5a0bf-155">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="5a0bf-156">(Kod kopyaladığınızda, ad alanı eşleşecek şekilde "MvcMovie" proje adı önemlidir.) "MvcMovie" proje adı</span><span class="sxs-lookup"><span data-stu-id="5a0bf-156">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="5a0bf-157">Dokunun **Tamam**</span><span class="sxs-lookup"><span data-stu-id="5a0bf-157">Tap **OK**</span></span>

![<span data-ttu-id="5a0bf-158">Yeni Proje iletişim kutusunda, sol bölmede, ASP.NET Core web'de .net core</span><span class="sxs-lookup"><span data-stu-id="5a0bf-158">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2.png)

<span data-ttu-id="5a0bf-159">Tamamlamak **yeni ASP.NET Core Web uygulaması (.NET Core) - MvcMovie** iletişim:</span><span class="sxs-lookup"><span data-stu-id="5a0bf-159">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="5a0bf-160">Sürüm Seçici açılan kutusunda seçin **ASP.NET Core 2.-**</span><span class="sxs-lookup"><span data-stu-id="5a0bf-160">In the version selector drop-down box select **ASP.NET Core 2.-**</span></span>
* <span data-ttu-id="5a0bf-161">Seçin **Application(Model-View-Controller) Web**</span><span class="sxs-lookup"><span data-stu-id="5a0bf-161">Select **Web Application(Model-View-Controller)**</span></span>
* <span data-ttu-id="5a0bf-162">Dokunun **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-162">Tap **OK**.</span></span>

![<span data-ttu-id="5a0bf-163">Yeni Proje iletişim kutusunda, sol bölmede, ASP.NET Core web'de .net core</span><span class="sxs-lookup"><span data-stu-id="5a0bf-163">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22.png)

<span data-ttu-id="5a0bf-164">Visual Studio, yeni oluşturduğunuz MVC projesi için varsayılan bir şablon kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-164">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="5a0bf-165">Çalışan bir uygulamayı şu anda bir proje adı girerek ve bazı Seçenekler'i seçerek sizde.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-165">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="5a0bf-166">Bu temel başlangıç projesini ve başlatmak için iyi bir yerdir,</span><span class="sxs-lookup"><span data-stu-id="5a0bf-166">This is a basic starter project, and it's a good place to start,</span></span>

<span data-ttu-id="5a0bf-167">Dokunun **F5** uygulamayı hata ayıklama modunda çalıştırmak için veya **Ctrl-F5** olmayan hata ayıklama modunda.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-167">Tap **F5** to run the app in debug mode or **Ctrl-F5** in non-debug mode.</span></span>
<span data-ttu-id="5a0bf-168"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![Uygulamayı çalıştırma](start-mvc/_static/1.png)</span><span class="sxs-lookup"><span data-stu-id="5a0bf-168"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![running app](start-mvc/_static/1.png)</span></span>

* <span data-ttu-id="5a0bf-169">Visual Studio başlatır [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ve uygulamanızı çalışır.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-169">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="5a0bf-170">Adres çubuğuna gösteren uyarı `localhost:port#` gibi bir şey `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-170">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a0bf-171">Çünkü `localhost` standart, yerel bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-171">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5a0bf-172">Visual Studio, bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-172">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="5a0bf-173">Yukarıdaki görüntüde, bağlantı noktası numarasını 5000'dir.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-173">In the image above, the port number is 5000.</span></span> <span data-ttu-id="5a0bf-174">Tarayıcı gösterir URL'de `localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-174">The URL in the browser shows `localhost:5000`.</span></span> <span data-ttu-id="5a0bf-175">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-175">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="5a0bf-176">Uygulamayı başlatma **Ctrl + F5** (hata ayıklama olmayan mod), kod değişiklikleri yapabilir, dosyayı kaydetmek, tarayıcıyı yenileyin ve kod değişikliklerini görebilirsiniz olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-176">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5a0bf-177">Geliştiricilerin çoğu, hızlı bir şekilde uygulamayı başlatın ve değişiklikleri görmek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-177">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="5a0bf-178">Uygulamada hata ayıklama veya hata ayıklama olmayan moddan başlatabilirsiniz **hata ayıklama** menü öğesi:</span><span class="sxs-lookup"><span data-stu-id="5a0bf-178">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menü hata ayıklama](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="5a0bf-180">Dokunarak uygulamayı hata ayıklaması yapabilirsiniz **IIS Express** düğmesi</span><span class="sxs-lookup"><span data-stu-id="5a0bf-180">You can debug the app by tapping the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="5a0bf-182">Varsayılan şablonu size çalışma **hakkında giriş** ve **kişi** bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-182">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="5a0bf-183">Yukarıdaki tarayıcı resimde, bu bağlantıları göstermez.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-183">The browser image above doesn't show these links.</span></span> <span data-ttu-id="5a0bf-184">Tarayıcınız boyutuna bağlı olarak, gösterileceğinin Gezinti simgesine tıklamanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-184">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![sağ üstteki gezinti simgesi](start-mvc/_static/2.png)

<span data-ttu-id="5a0bf-186">Hata ayıklama modunda çalışıyormuş dokunun **Shift + F5** hata ayıklamayı durdurmak için.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-186">If you were running in debug mode, tap **Shift-F5** to stop debugging.</span></span>

<span data-ttu-id="5a0bf-187">Bu öğreticinin sonraki bölümünde, biz MVC konusunda bilgi ve biraz kod yazmaya.</span><span class="sxs-lookup"><span data-stu-id="5a0bf-187">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> [<span data-ttu-id="5a0bf-188">Next</span><span class="sxs-lookup"><span data-stu-id="5a0bf-188">Next</span></span>](adding-controller.md)  
