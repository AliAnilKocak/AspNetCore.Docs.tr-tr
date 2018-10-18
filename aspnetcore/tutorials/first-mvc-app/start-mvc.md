---
title: ASP.NET Core MVC ve Visual Studio ile çalışmaya başlama
author: rick-anderson
description: ASP.NET Core MVC ve Visual Studio ile çalışmaya başlama hakkında bilgi edinin.
ms.author: riande
ms.date: 10/07/2017
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: fe555e4cfcaec5d4bb8ccee00b06d1bbcaae9dcd
ms.sourcegitcommit: f43f430a166a7ec137fcad12ded0372747227498
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49391212"
---
# <a name="get-started-with-aspnet-core-mvc-and-visual-studio"></a><span data-ttu-id="6e163-103">ASP.NET Core MVC ve Visual Studio ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="6e163-103">Get started with ASP.NET Core MVC and Visual Studio</span></span>

<span data-ttu-id="6e163-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6e163-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="6e163-105">Bu öğreticinin 3 sürümü vardır:</span><span class="sxs-lookup"><span data-stu-id="6e163-105">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="6e163-106">macOS: [Mac için Visual Studio ile ASP.NET Core MVC uygulaması oluşturma](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="6e163-106">macOS: [Create an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="6e163-107">Windows: [Visual Studio ile bir ASP.NET Core MVC uygulaması oluşturma](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="6e163-107">Windows: [Create an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="6e163-108">macOS, Linux ve Windows: [Visual Studio Code ile ASP.NET Core MVC uygulaması oluşturma](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="6e163-108">macOS, Linux, and Windows: [Create an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span>

## <a name="install-visual-studio-and-net-core"></a><span data-ttu-id="6e163-109">Visual Studio ve .NET Core yükleyin</span><span class="sxs-lookup"><span data-stu-id="6e163-109">Install Visual Studio and .NET Core</span></span>

::: moniker range=">= aspnetcore-2.1"

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="6e163-110">Bir web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="6e163-110">Create a web app</span></span>

<span data-ttu-id="6e163-111">Visual Studio'dan seçin **Dosya > Yeni > Proje**.</span><span class="sxs-lookup"><span data-stu-id="6e163-111">From Visual Studio, select  **File > New > Project**.</span></span>

![Dosya > Yeni > Proje](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="6e163-113">Tamamlamak **yeni proje** iletişim:</span><span class="sxs-lookup"><span data-stu-id="6e163-113">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="6e163-114">Sol bölmede, dokunun **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="6e163-114">In the left pane, tap **.NET Core**</span></span>
* <span data-ttu-id="6e163-115">Orta bölmede dokunun **ASP.NET Core Web uygulaması (.NET Core)**</span><span class="sxs-lookup"><span data-stu-id="6e163-115">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="6e163-116">(Kod kopyaladığınızda, ad alanı eşleşecek şekilde "MvcMovie" proje adı önemlidir.) "MvcMovie" proje adı</span><span class="sxs-lookup"><span data-stu-id="6e163-116">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="6e163-117">Dokunun **Tamam**</span><span class="sxs-lookup"><span data-stu-id="6e163-117">Tap **OK**</span></span>

![<span data-ttu-id="6e163-118">Yeni Proje iletişim kutusunda, sol bölmede, ASP.NET Core web'de .net core</span><span class="sxs-lookup"><span data-stu-id="6e163-118">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2-21.png)

<span data-ttu-id="6e163-119">Tamamlamak **yeni ASP.NET Core Web uygulaması (.NET Core) - MvcMovie** iletişim:</span><span class="sxs-lookup"><span data-stu-id="6e163-119">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="6e163-120">Sürüm Seçici açılan kutusunda seçin **ASP.NET Core 2.1**</span><span class="sxs-lookup"><span data-stu-id="6e163-120">In the version selector drop-down box select **ASP.NET Core 2.1**</span></span>
* <span data-ttu-id="6e163-121">Seçin **Web uygulaması (Model-View-Controller)**</span><span class="sxs-lookup"><span data-stu-id="6e163-121">Select **Web Application (Model-View-Controller)**</span></span>
* <span data-ttu-id="6e163-122">Dokunun **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="6e163-122">Tap **OK**.</span></span>

![<span data-ttu-id="6e163-123">Yeni Proje iletişim kutusunda, sol bölmede, ASP.NET Core web'de .net core</span><span class="sxs-lookup"><span data-stu-id="6e163-123">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="6e163-124">Visual Studio, yeni oluşturduğunuz MVC projesi için varsayılan bir şablon kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6e163-124">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="6e163-125">Çalışan bir uygulamayı şu anda bir proje adı girerek ve bazı Seçenekler'i seçerek sizde.</span><span class="sxs-lookup"><span data-stu-id="6e163-125">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="6e163-126">Bu temel başlangıç projesini ve başlatmak için iyi bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="6e163-126">This is a basic starter project, and it's a good place to start.</span></span>

<span data-ttu-id="6e163-127">Dokunun **F5** uygulamayı hata ayıklama modunda çalıştırmak için veya **Ctrl-F5** olmayan hata ayıklama modunda.</span><span class="sxs-lookup"><span data-stu-id="6e163-127">Tap **F5** to run the app in debug mode or **Ctrl-F5** in non-debug mode.</span></span>
<span data-ttu-id="6e163-128"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![Uygulamayı çalıştırma](start-mvc/_static/1.png)</span><span class="sxs-lookup"><span data-stu-id="6e163-128"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![running app](start-mvc/_static/1.png)</span></span>

* <span data-ttu-id="6e163-129">Visual Studio başlatır [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ve uygulamanızı çalışır.</span><span class="sxs-lookup"><span data-stu-id="6e163-129">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="6e163-130">Adres çubuğuna gösteren uyarı `localhost:port#` gibi bir şey `example.com`.</span><span class="sxs-lookup"><span data-stu-id="6e163-130">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6e163-131">Çünkü `localhost` standart, yerel bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="6e163-131">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="6e163-132">Visual Studio, bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6e163-132">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="6e163-133">Yukarıdaki görüntüde, bağlantı noktası numarasını 5000'dir.</span><span class="sxs-lookup"><span data-stu-id="6e163-133">In the image above, the port number is 5000.</span></span> <span data-ttu-id="6e163-134">Tarayıcı gösterir URL'de `localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="6e163-134">The URL in the browser shows `localhost:5000`.</span></span> <span data-ttu-id="6e163-135">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="6e163-135">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="6e163-136">Uygulamayı başlatma **Ctrl + F5** (hata ayıklama olmayan mod), kod değişiklikleri yapabilir, dosyayı kaydetmek, tarayıcıyı yenileyin ve kod değişikliklerini görebilirsiniz olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e163-136">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="6e163-137">Geliştiricilerin çoğu, hızlı bir şekilde uygulamayı başlatın ve değişiklikleri görmek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="6e163-137">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="6e163-138">Uygulamada hata ayıklama veya hata ayıklama olmayan moddan başlatabilirsiniz **hata ayıklama** menü öğesi:</span><span class="sxs-lookup"><span data-stu-id="6e163-138">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menü hata ayıklama](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="6e163-140">Dokunarak uygulamayı hata ayıklaması yapabilirsiniz **IIS Express** düğmesi</span><span class="sxs-lookup"><span data-stu-id="6e163-140">You can debug the app by tapping the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="6e163-142">Varsayılan şablonu size çalışma **hakkında giriş** ve **kişi** bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="6e163-142">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="6e163-143">Yukarıdaki tarayıcı resimde, bu bağlantıları göstermez.</span><span class="sxs-lookup"><span data-stu-id="6e163-143">The browser image above doesn't show these links.</span></span> <span data-ttu-id="6e163-144">Tarayıcınız boyutuna bağlı olarak, gösterileceğinin Gezinti simgesine tıklamanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="6e163-144">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![sağ üstteki gezinti simgesi](start-mvc/_static/2.png)

<span data-ttu-id="6e163-146">Hata ayıklama modunda çalışıyormuş dokunun **Shift + F5** hata ayıklamayı durdurmak için.</span><span class="sxs-lookup"><span data-stu-id="6e163-146">If you were running in debug mode, tap **Shift-F5** to stop debugging.</span></span>

<span data-ttu-id="6e163-147">Bu öğreticinin sonraki bölümünde, biz MVC konusunda bilgi ve biraz kod yazmaya.</span><span class="sxs-lookup"><span data-stu-id="6e163-147">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="6e163-148">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="6e163-148">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

[!INCLUDE [](~/includes/net-core-prereqs.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="6e163-149">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="6e163-149">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

<span data-ttu-id="6e163-150">Visual Studio Community 2017'yi yükleyin.</span><span class="sxs-lookup"><span data-stu-id="6e163-150">Install Visual Studio Community 2017.</span></span> <span data-ttu-id="6e163-151">Topluluk indir'i seçin.</span><span class="sxs-lookup"><span data-stu-id="6e163-151">Select the Community download.</span></span> <span data-ttu-id="6e163-152">Visual Studio 2017 varsa bu adımı atlayın.</span><span class="sxs-lookup"><span data-stu-id="6e163-152">Skip this step if you have Visual Studio 2017 installed.</span></span>

* [<span data-ttu-id="6e163-153">Visual Studio 2017 giriş sayfası yükleyicisi</span><span class="sxs-lookup"><span data-stu-id="6e163-153">Visual Studio 2017 Home page installer</span></span>](https://www.visualstudio.com/)

<span data-ttu-id="6e163-154">Yükleyiciyi çalıştırın ve aşağıdaki iş yükleri seçin:</span><span class="sxs-lookup"><span data-stu-id="6e163-154">Run the installer and select the following workloads:</span></span>

* <span data-ttu-id="6e163-155">**ASP.NET ve web geliştirme** (altında **Web ve bulut**)</span><span class="sxs-lookup"><span data-stu-id="6e163-155">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
* <span data-ttu-id="6e163-156">**.NET core çoklu platform geliştirme** (altında **diğer araç takımları**)</span><span class="sxs-lookup"><span data-stu-id="6e163-156">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

![\*\*ASP.NET ve web geliştirme \*\* (altında \*\* Web ve bulut \*\*)](start-mvc/_static/web_workload.png)

![\* \*.NET çekirdek arası arası platfrom geliştirme \*\* (altında \*\* diğer araç takımları \*\*)](start-mvc/_static/x_plat_wl.png)

---

## <a name="create-a-web-app"></a><span data-ttu-id="6e163-159">Bir web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="6e163-159">Create a web app</span></span>

<span data-ttu-id="6e163-160">Visual Studio'dan seçin **Dosya > Yeni > Proje**.</span><span class="sxs-lookup"><span data-stu-id="6e163-160">From Visual Studio, select  **File > New > Project**.</span></span>

![Dosya > Yeni > Proje](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="6e163-162">Tamamlamak **yeni proje** iletişim:</span><span class="sxs-lookup"><span data-stu-id="6e163-162">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="6e163-163">Sol bölmede, dokunun **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="6e163-163">In the left pane, tap **.NET Core**</span></span>
* <span data-ttu-id="6e163-164">Orta bölmede dokunun **ASP.NET Core Web uygulaması (.NET Core)**</span><span class="sxs-lookup"><span data-stu-id="6e163-164">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="6e163-165">(Kod kopyaladığınızda, ad alanı eşleşecek şekilde "MvcMovie" proje adı önemlidir.) "MvcMovie" proje adı</span><span class="sxs-lookup"><span data-stu-id="6e163-165">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="6e163-166">Dokunun **Tamam**</span><span class="sxs-lookup"><span data-stu-id="6e163-166">Tap **OK**</span></span>

![<span data-ttu-id="6e163-167">Yeni Proje iletişim kutusunda, sol bölmede, ASP.NET Core web'de .net core</span><span class="sxs-lookup"><span data-stu-id="6e163-167">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2.png)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="6e163-168">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="6e163-168">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="6e163-169">Tamamlamak **yeni ASP.NET Core Web uygulaması (.NET Core) - MvcMovie** iletişim:</span><span class="sxs-lookup"><span data-stu-id="6e163-169">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="6e163-170">Sürüm Seçici açılan kutusunda seçin **ASP.NET Core 2.-**</span><span class="sxs-lookup"><span data-stu-id="6e163-170">In the version selector drop-down box select **ASP.NET Core 2.-**</span></span>
* <span data-ttu-id="6e163-171">Seçin **Application(Model-View-Controller) Web**</span><span class="sxs-lookup"><span data-stu-id="6e163-171">Select **Web Application(Model-View-Controller)**</span></span>
* <span data-ttu-id="6e163-172">Dokunun **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="6e163-172">Tap **OK**.</span></span>

![<span data-ttu-id="6e163-173">Yeni Proje iletişim kutusunda, sol bölmede, ASP.NET Core web'de .net core</span><span class="sxs-lookup"><span data-stu-id="6e163-173">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22.png)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="6e163-174">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="6e163-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="6e163-175">Tamamlamak **yeni ASP.NET Core Web uygulaması (.NET Core) - MvcMovie** iletişim:</span><span class="sxs-lookup"><span data-stu-id="6e163-175">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="6e163-176">Sürüm Seçici açılan kutusunda tap içindeki **ASP.NET Core 1.1**</span><span class="sxs-lookup"><span data-stu-id="6e163-176">In the version selector drop-down box tap **ASP.NET Core 1.1**</span></span>
* <span data-ttu-id="6e163-177">Dokunun **Web uygulaması**</span><span class="sxs-lookup"><span data-stu-id="6e163-177">Tap **Web Application**</span></span>
* <span data-ttu-id="6e163-178">Varsayılan tutun **kimlik doğrulaması yok**</span><span class="sxs-lookup"><span data-stu-id="6e163-178">Keep the default **No Authentication**</span></span>
* <span data-ttu-id="6e163-179">Dokunun **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="6e163-179">Tap **OK**.</span></span>

![Yeni ASP.NET Core web uygulaması](start-mvc/_static/p3.png)

---

<span data-ttu-id="6e163-181">Visual Studio, yeni oluşturduğunuz MVC projesi için varsayılan bir şablon kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6e163-181">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="6e163-182">Çalışan bir uygulamayı şu anda bir proje adı girerek ve bazı Seçenekler'i seçerek sizde.</span><span class="sxs-lookup"><span data-stu-id="6e163-182">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="6e163-183">Bu temel başlangıç projesini ve başlatmak için iyi bir yerdir,</span><span class="sxs-lookup"><span data-stu-id="6e163-183">This is a basic starter project, and it's a good place to start,</span></span>

<span data-ttu-id="6e163-184">Dokunun **F5** uygulamayı hata ayıklama modunda çalıştırmak için veya **Ctrl-F5** olmayan hata ayıklama modunda.</span><span class="sxs-lookup"><span data-stu-id="6e163-184">Tap **F5** to run the app in debug mode or **Ctrl-F5** in non-debug mode.</span></span>
<span data-ttu-id="6e163-185"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![Uygulamayı çalıştırma](start-mvc/_static/1.png)</span><span class="sxs-lookup"><span data-stu-id="6e163-185"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![running app](start-mvc/_static/1.png)</span></span>

* <span data-ttu-id="6e163-186">Visual Studio başlatır [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ve uygulamanızı çalışır.</span><span class="sxs-lookup"><span data-stu-id="6e163-186">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="6e163-187">Adres çubuğuna gösteren uyarı `localhost:port#` gibi bir şey `example.com`.</span><span class="sxs-lookup"><span data-stu-id="6e163-187">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6e163-188">Çünkü `localhost` standart, yerel bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="6e163-188">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="6e163-189">Visual Studio, bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6e163-189">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="6e163-190">Yukarıdaki görüntüde, bağlantı noktası numarasını 5000'dir.</span><span class="sxs-lookup"><span data-stu-id="6e163-190">In the image above, the port number is 5000.</span></span> <span data-ttu-id="6e163-191">Tarayıcı gösterir URL'de `localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="6e163-191">The URL in the browser shows `localhost:5000`.</span></span> <span data-ttu-id="6e163-192">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="6e163-192">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="6e163-193">Uygulamayı başlatma **Ctrl + F5** (hata ayıklama olmayan mod), kod değişiklikleri yapabilir, dosyayı kaydetmek, tarayıcıyı yenileyin ve kod değişikliklerini görebilirsiniz olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e163-193">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="6e163-194">Geliştiricilerin çoğu, hızlı bir şekilde uygulamayı başlatın ve değişiklikleri görmek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="6e163-194">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="6e163-195">Uygulamada hata ayıklama veya hata ayıklama olmayan moddan başlatabilirsiniz **hata ayıklama** menü öğesi:</span><span class="sxs-lookup"><span data-stu-id="6e163-195">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menü hata ayıklama](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="6e163-197">Dokunarak uygulamayı hata ayıklaması yapabilirsiniz **IIS Express** düğmesi</span><span class="sxs-lookup"><span data-stu-id="6e163-197">You can debug the app by tapping the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="6e163-199">Varsayılan şablonu size çalışma **hakkında giriş** ve **kişi** bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="6e163-199">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="6e163-200">Yukarıdaki tarayıcı resimde, bu bağlantıları göstermez.</span><span class="sxs-lookup"><span data-stu-id="6e163-200">The browser image above doesn't show these links.</span></span> <span data-ttu-id="6e163-201">Tarayıcınız boyutuna bağlı olarak, gösterileceğinin Gezinti simgesine tıklamanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="6e163-201">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![sağ üstteki gezinti simgesi](start-mvc/_static/2.png)

<span data-ttu-id="6e163-203">Hata ayıklama modunda çalışıyormuş dokunun **Shift + F5** hata ayıklamayı durdurmak için.</span><span class="sxs-lookup"><span data-stu-id="6e163-203">If you were running in debug mode, tap **Shift-F5** to stop debugging.</span></span>

<span data-ttu-id="6e163-204">Bu öğreticinin sonraki bölümünde, biz MVC konusunda bilgi ve biraz kod yazmaya.</span><span class="sxs-lookup"><span data-stu-id="6e163-204">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> [<span data-ttu-id="6e163-205">Next</span><span class="sxs-lookup"><span data-stu-id="6e163-205">Next</span></span>](adding-controller.md)  
