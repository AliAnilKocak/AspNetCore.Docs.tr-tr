---
title: ASP.NET Core MVC ve Visual Studio ile çalışmaya başlama
author: rick-anderson
description: ASP.NET Core MVC ve Visual Studio ile çalışmaya başlama hakkında bilgi edinin.
manager: wpickett
ms.author: riande
ms.date: 10/07/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 3272700c7739778a6a341ae8ee424fd69605ca53
ms.sourcegitcommit: 43bd79667bbdc8a07bd39fb4cd6f7ad3e70212fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34729723"
---
# <a name="get-started-with-aspnet-core-mvc-and-visual-studio"></a><span data-ttu-id="6d653-103">ASP.NET Core MVC ve Visual Studio ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="6d653-103">Get started with ASP.NET Core MVC and Visual Studio</span></span>

<span data-ttu-id="6d653-104">tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6d653-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="6d653-105">Bu öğretici 3 sürümü vardır:</span><span class="sxs-lookup"><span data-stu-id="6d653-105">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="6d653-106">macOS: [Mac için Visual Studio ile ASP.NET Core MVC uygulama oluşturma](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="6d653-106">macOS: [Create an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="6d653-107">Windows: [Visual Studio ile ASP.NET Core MVC uygulaması oluşturma](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="6d653-107">Windows: [Create an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="6d653-108">macOS, Linux ve Windows: [Visual Studio Code ile bir ASP.NET Core MVC uygulaması oluşturma](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="6d653-108">macOS, Linux, and Windows: [Create an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span>

## <a name="install-visual-studio-and-net-core"></a><span data-ttu-id="6d653-109">Visual Studio ve .NET Core yükleyin</span><span class="sxs-lookup"><span data-stu-id="6d653-109">Install Visual Studio and .NET Core</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="6d653-110">[! [] (~/İncludes/net-core-prereqs-windows.md) içerir [](~/includes/net-core-prereqs-windows.md)]</span><span class="sxs-lookup"><span data-stu-id="6d653-110">[!INCLUDE [](~/includes/net-core-prereqs-windows.md) [](~/includes/net-core-prereqs-windows.md)]</span></span>

## <a name="create-a-web-app"></a><span data-ttu-id="6d653-111">Bir web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="6d653-111">Create a web app</span></span>

<span data-ttu-id="6d653-112">Visual Studio'dan seçin **Dosya > Yeni > Proje**.</span><span class="sxs-lookup"><span data-stu-id="6d653-112">From Visual Studio, select  **File > New > Project**.</span></span>

![Dosya > Yeni > Proje](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="6d653-114">Tamamlamak **yeni proje** iletişim:</span><span class="sxs-lookup"><span data-stu-id="6d653-114">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="6d653-115">Sol bölmede, dokunun **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="6d653-115">In the left pane, tap **.NET Core**</span></span>
* <span data-ttu-id="6d653-116">Orta bölmede, dokunun **ASP.NET çekirdek Web uygulaması (.NET çekirdek)**</span><span class="sxs-lookup"><span data-stu-id="6d653-116">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="6d653-117">(Kod kopyaladığınızda, ad alanı eşleşecek şekilde "MvcMovie" proje adı önemlidir.) "MvcMovie" proje adı</span><span class="sxs-lookup"><span data-stu-id="6d653-117">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="6d653-118">Dokunun **Tamam**</span><span class="sxs-lookup"><span data-stu-id="6d653-118">Tap **OK**</span></span>

![<span data-ttu-id="6d653-119">Yeni Proje iletişim kutusunda, sol bölmede, ASP.NET Core web .net core</span><span class="sxs-lookup"><span data-stu-id="6d653-119">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2-21.png)

<span data-ttu-id="6d653-120">Tamamlamak **yeni ASP.NET çekirdek Web uygulaması (.NET Core) - MvcMovie** iletişim:</span><span class="sxs-lookup"><span data-stu-id="6d653-120">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="6d653-121">Sürüm Seçici açılan kutusunda **ASP.NET Core 2.1**</span><span class="sxs-lookup"><span data-stu-id="6d653-121">In the version selector drop-down box select **ASP.NET Core 2.1**</span></span>
* <span data-ttu-id="6d653-122">Seçin **Application(Model-View-Controller) Web**</span><span class="sxs-lookup"><span data-stu-id="6d653-122">Select **Web Application(Model-View-Controller)**</span></span>
* <span data-ttu-id="6d653-123">Dokunun **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="6d653-123">Tap **OK**.</span></span>

![<span data-ttu-id="6d653-124">Yeni Proje iletişim kutusunda, sol bölmede, ASP.NET Core web .net core</span><span class="sxs-lookup"><span data-stu-id="6d653-124">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="6d653-125">Visual Studio, yeni oluşturduğunuz MVC proje için varsayılan bir şablon kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6d653-125">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="6d653-126">Bir çalışma şu anda bir proje adı girerek ve birkaç seçenek seçerek uygulamanız.</span><span class="sxs-lookup"><span data-stu-id="6d653-126">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="6d653-127">Bu temel başlangıç projesi ve başlatmak için uygun bir yerdir,</span><span class="sxs-lookup"><span data-stu-id="6d653-127">This is a basic starter project, and it's a good place to start,</span></span>

<span data-ttu-id="6d653-128">Dokunun **F5** uygulamayı hata ayıklama modunda çalıştırmak için veya **Ctrl-F5** olmayan hata ayıklama modunda.</span><span class="sxs-lookup"><span data-stu-id="6d653-128">Tap **F5** to run the app in debug mode or **Ctrl-F5** in non-debug mode.</span></span>
<!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![Uygulamayı çalıştırma](start-mvc/_static/1.png)

* <span data-ttu-id="6d653-130">Visual Studio başlatır [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ve uygulamanızı çalışır.</span><span class="sxs-lookup"><span data-stu-id="6d653-130">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="6d653-131">Adres çubuğunun bildirim `localhost:port#` bir şey yok gibi ve `example.com`.</span><span class="sxs-lookup"><span data-stu-id="6d653-131">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6d653-132">Çünkü `localhost` , yerel bilgisayarınızın standart barındırıcı adıdır.</span><span class="sxs-lookup"><span data-stu-id="6d653-132">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="6d653-133">Visual Studio web projesini oluşturduğunda, rastgele bir bağlantı noktası web sunucusu için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6d653-133">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="6d653-134">Yukarıdaki resimde 5000 bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="6d653-134">In the image above, the port number is 5000.</span></span> <span data-ttu-id="6d653-135">Tarayıcı gösterir URL'de `localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="6d653-135">The URL in the browser shows `localhost:5000`.</span></span> <span data-ttu-id="6d653-136">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="6d653-136">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="6d653-137">Uygulama başlatma **Ctrl + F5** (olmayan hata ayıklama modu), kod değişiklikleri yapabilir, dosyayı kaydedin, tarayıcıyı yenilemek ve kod değişiklikleri görmek olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="6d653-137">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="6d653-138">Çoğu geliştirici, hızlı bir şekilde uygulamayı başlatın ve değişiklikleri görmek için olmayan hata ayıklama modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="6d653-138">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="6d653-139">Hata ayıklama veya hata ayıklama olmayan modundan uygulamada başlatabilirsiniz **hata ayıklama** menü öğesi:</span><span class="sxs-lookup"><span data-stu-id="6d653-139">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menü hata ayıklama](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="6d653-141">Uygulama dokunarak ayıklayabilirsiniz **IIS Express** düğmesi</span><span class="sxs-lookup"><span data-stu-id="6d653-141">You can debug the app by tapping the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="6d653-143">Varsayılan şablonu, çalışma sunar **hakkında giriş** ve **kişi** bağlantılar.</span><span class="sxs-lookup"><span data-stu-id="6d653-143">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="6d653-144">Yukarıdaki tarayıcı resimde bu bağlantıları göstermez.</span><span class="sxs-lookup"><span data-stu-id="6d653-144">The browser image above doesn't show these links.</span></span> <span data-ttu-id="6d653-145">Tarayıcınız boyutuna bağlı olarak, bunları göstermek için Gezinti simgesini gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="6d653-145">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![sağ üstteki gezinti simgesi](start-mvc/_static/2.png)

<span data-ttu-id="6d653-147">Hata ayıklama modunda çalışıyormuş dokunun **Shift + F5** hata ayıklamasını durdurmak için.</span><span class="sxs-lookup"><span data-stu-id="6d653-147">If you were running in debug mode, tap **Shift-F5** to stop debugging.</span></span>

<span data-ttu-id="6d653-148">Bu öğreticinin sonraki bölümünde, biz MVC hakkında bilgi edinin ve biraz kod yazmaya başlamadan.</span><span class="sxs-lookup"><span data-stu-id="6d653-148">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="6d653-149">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="6d653-149">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

<span data-ttu-id="6d653-150">[! [] (~/İncludes/net-core-prereqs.md) içerir [](~/includes/net-core-prereqs.md)]</span><span class="sxs-lookup"><span data-stu-id="6d653-150">[!INCLUDE [](~/includes/net-core-prereqs.md) [](~/includes/net-core-prereqs.md)]</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="6d653-151">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="6d653-151">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

<span data-ttu-id="6d653-152">Visual Studio Community 2017 yükleyin.</span><span class="sxs-lookup"><span data-stu-id="6d653-152">Install Visual Studio Community 2017.</span></span> <span data-ttu-id="6d653-153">Topluluk indirme seçin.</span><span class="sxs-lookup"><span data-stu-id="6d653-153">Select the Community download.</span></span> <span data-ttu-id="6d653-154">Visual Studio yüklü 2017 varsa bu adımı atlayın.</span><span class="sxs-lookup"><span data-stu-id="6d653-154">Skip this step if you have Visual Studio 2017 installed.</span></span>

* [<span data-ttu-id="6d653-155">Visual Studio 2017 giriş sayfası yükleyicisi</span><span class="sxs-lookup"><span data-stu-id="6d653-155">Visual Studio 2017 Home page installer</span></span>](https://www.visualstudio.com/)

<span data-ttu-id="6d653-156">Yükleyiciyi çalıştırın ve aşağıdaki iş yüklerini seçin:</span><span class="sxs-lookup"><span data-stu-id="6d653-156">Run the installer and select the following workloads:</span></span>

* <span data-ttu-id="6d653-157">**ASP.NET ve web geliştirme** (altında **Web ve bulut**)</span><span class="sxs-lookup"><span data-stu-id="6d653-157">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
* <span data-ttu-id="6d653-158">**.NET core platformlar arası geliştirme** (altında **diğer Toolsets**)</span><span class="sxs-lookup"><span data-stu-id="6d653-158">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

![**ASP.NET ve web geliştirme ** (altında ** Web ve bulut **)](start-mvc/_static/web_workload.png)

![* *.NET çekirdek arası arası platfrom geliştirme ** (altında ** diğer Toolsets **)](start-mvc/_static/x_plat_wl.png)

---

## <a name="create-a-web-app"></a><span data-ttu-id="6d653-161">Bir web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="6d653-161">Create a web app</span></span>

<span data-ttu-id="6d653-162">Visual Studio'dan seçin **Dosya > Yeni > Proje**.</span><span class="sxs-lookup"><span data-stu-id="6d653-162">From Visual Studio, select  **File > New > Project**.</span></span>

![Dosya > Yeni > Proje](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="6d653-164">Tamamlamak **yeni proje** iletişim:</span><span class="sxs-lookup"><span data-stu-id="6d653-164">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="6d653-165">Sol bölmede, dokunun **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="6d653-165">In the left pane, tap **.NET Core**</span></span>
* <span data-ttu-id="6d653-166">Orta bölmede, dokunun **ASP.NET çekirdek Web uygulaması (.NET çekirdek)**</span><span class="sxs-lookup"><span data-stu-id="6d653-166">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="6d653-167">(Kod kopyaladığınızda, ad alanı eşleşecek şekilde "MvcMovie" proje adı önemlidir.) "MvcMovie" proje adı</span><span class="sxs-lookup"><span data-stu-id="6d653-167">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="6d653-168">Dokunun **Tamam**</span><span class="sxs-lookup"><span data-stu-id="6d653-168">Tap **OK**</span></span>

![<span data-ttu-id="6d653-169">Yeni Proje iletişim kutusunda, sol bölmede, ASP.NET Core web .net core</span><span class="sxs-lookup"><span data-stu-id="6d653-169">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2.png)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="6d653-170">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="6d653-170">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="6d653-171">Tamamlamak **yeni ASP.NET çekirdek Web uygulaması (.NET Core) - MvcMovie** iletişim:</span><span class="sxs-lookup"><span data-stu-id="6d653-171">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="6d653-172">Sürüm Seçici açılan kutusunda **ASP.NET Core 2.-**</span><span class="sxs-lookup"><span data-stu-id="6d653-172">In the version selector drop-down box select **ASP.NET Core 2.-**</span></span>
* <span data-ttu-id="6d653-173">Seçin **Application(Model-View-Controller) Web**</span><span class="sxs-lookup"><span data-stu-id="6d653-173">Select **Web Application(Model-View-Controller)**</span></span>
* <span data-ttu-id="6d653-174">Dokunun **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="6d653-174">Tap **OK**.</span></span>

![<span data-ttu-id="6d653-175">Yeni Proje iletişim kutusunda, sol bölmede, ASP.NET Core web .net core</span><span class="sxs-lookup"><span data-stu-id="6d653-175">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22.png)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="6d653-176">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="6d653-176">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="6d653-177">Tamamlamak **yeni ASP.NET çekirdek Web uygulaması (.NET Core) - MvcMovie** iletişim:</span><span class="sxs-lookup"><span data-stu-id="6d653-177">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="6d653-178">Sürüm Seçici açılan kutu dokunun içinde **ASP.NET Core 1.1**</span><span class="sxs-lookup"><span data-stu-id="6d653-178">In the version selector drop-down box tap **ASP.NET Core 1.1**</span></span>
* <span data-ttu-id="6d653-179">Dokunun **Web uygulaması**</span><span class="sxs-lookup"><span data-stu-id="6d653-179">Tap **Web Application**</span></span>
* <span data-ttu-id="6d653-180">Varsayılan tutmak **doğrulaması yok**</span><span class="sxs-lookup"><span data-stu-id="6d653-180">Keep the default **No Authentication**</span></span>
* <span data-ttu-id="6d653-181">Dokunun **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="6d653-181">Tap **OK**.</span></span>

![Yeni ASP.NET Core web uygulaması](start-mvc/_static/p3.png)

---

<span data-ttu-id="6d653-183">Visual Studio, yeni oluşturduğunuz MVC proje için varsayılan bir şablon kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6d653-183">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="6d653-184">Bir çalışma şu anda bir proje adı girerek ve birkaç seçenek seçerek uygulamanız.</span><span class="sxs-lookup"><span data-stu-id="6d653-184">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="6d653-185">Bu temel başlangıç projesi ve başlatmak için uygun bir yerdir,</span><span class="sxs-lookup"><span data-stu-id="6d653-185">This is a basic starter project, and it's a good place to start,</span></span>

<span data-ttu-id="6d653-186">Dokunun **F5** uygulamayı hata ayıklama modunda çalıştırmak için veya **Ctrl-F5** olmayan hata ayıklama modunda.</span><span class="sxs-lookup"><span data-stu-id="6d653-186">Tap **F5** to run the app in debug mode or **Ctrl-F5** in non-debug mode.</span></span>
<!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![Uygulamayı çalıştırma](start-mvc/_static/1.png)

* <span data-ttu-id="6d653-188">Visual Studio başlatır [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ve uygulamanızı çalışır.</span><span class="sxs-lookup"><span data-stu-id="6d653-188">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="6d653-189">Adres çubuğunun bildirim `localhost:port#` bir şey yok gibi ve `example.com`.</span><span class="sxs-lookup"><span data-stu-id="6d653-189">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6d653-190">Çünkü `localhost` , yerel bilgisayarınızın standart barındırıcı adıdır.</span><span class="sxs-lookup"><span data-stu-id="6d653-190">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="6d653-191">Visual Studio web projesini oluşturduğunda, rastgele bir bağlantı noktası web sunucusu için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6d653-191">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="6d653-192">Yukarıdaki resimde 5000 bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="6d653-192">In the image above, the port number is 5000.</span></span> <span data-ttu-id="6d653-193">Tarayıcı gösterir URL'de `localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="6d653-193">The URL in the browser shows `localhost:5000`.</span></span> <span data-ttu-id="6d653-194">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="6d653-194">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="6d653-195">Uygulama başlatma **Ctrl + F5** (olmayan hata ayıklama modu), kod değişiklikleri yapabilir, dosyayı kaydedin, tarayıcıyı yenilemek ve kod değişiklikleri görmek olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="6d653-195">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="6d653-196">Çoğu geliştirici, hızlı bir şekilde uygulamayı başlatın ve değişiklikleri görmek için olmayan hata ayıklama modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="6d653-196">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="6d653-197">Hata ayıklama veya hata ayıklama olmayan modundan uygulamada başlatabilirsiniz **hata ayıklama** menü öğesi:</span><span class="sxs-lookup"><span data-stu-id="6d653-197">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menü hata ayıklama](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="6d653-199">Uygulama dokunarak ayıklayabilirsiniz **IIS Express** düğmesi</span><span class="sxs-lookup"><span data-stu-id="6d653-199">You can debug the app by tapping the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="6d653-201">Varsayılan şablonu, çalışma sunar **hakkında giriş** ve **kişi** bağlantılar.</span><span class="sxs-lookup"><span data-stu-id="6d653-201">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="6d653-202">Yukarıdaki tarayıcı resimde bu bağlantıları göstermez.</span><span class="sxs-lookup"><span data-stu-id="6d653-202">The browser image above doesn't show these links.</span></span> <span data-ttu-id="6d653-203">Tarayıcınız boyutuna bağlı olarak, bunları göstermek için Gezinti simgesini gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="6d653-203">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![sağ üstteki gezinti simgesi](start-mvc/_static/2.png)

<span data-ttu-id="6d653-205">Hata ayıklama modunda çalışıyormuş dokunun **Shift + F5** hata ayıklamasını durdurmak için.</span><span class="sxs-lookup"><span data-stu-id="6d653-205">If you were running in debug mode, tap **Shift-F5** to stop debugging.</span></span>

<span data-ttu-id="6d653-206">Bu öğreticinin sonraki bölümünde, biz MVC hakkında bilgi edinin ve biraz kod yazmaya başlamadan.</span><span class="sxs-lookup"><span data-stu-id="6d653-206">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

::: moniker-end
> [!div class="step-by-step"]
> [<span data-ttu-id="6d653-207">Next</span><span class="sxs-lookup"><span data-stu-id="6d653-207">Next</span></span>](adding-controller.md)  
