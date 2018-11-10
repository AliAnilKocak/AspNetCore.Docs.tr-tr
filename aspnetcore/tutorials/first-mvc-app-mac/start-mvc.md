---
title: Mac için Visual Studio ile ASP.NET Core MVC ile çalışmaya başlama
author: rick-anderson
description: ASP.NET Core MVC ve Visual Studio ile çalışmaya başlama hakkında bilgi edinin
ms.author: riande
ms.date: 8/23/2017
uid: tutorials/first-mvc-app-mac/start-mvc
ms.openlocfilehash: 059ac1f7fa94d97adc958be3c0b936cdfa7f6d3e
ms.sourcegitcommit: fc7eb4243188950ae1f1b52669edc007e9d0798d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51225479"
---
# <a name="get-started-with-aspnet-core-mvc-and-visual-studio-for-mac"></a><span data-ttu-id="a3416-103">Mac için Visual Studio ile ASP.NET Core MVC ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="a3416-103">Get started with ASP.NET Core MVC and Visual Studio for Mac</span></span>

<span data-ttu-id="a3416-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a3416-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a3416-105">Bu öğreticide bir ASP.NET Core MVC kullanarak web uygulamasını oluşturmaya ilişkin temel bilgileri öğretir [Mac için Visual Studio](https://www.visualstudio.com/vs/visual-studio-mac/).</span><span class="sxs-lookup"><span data-stu-id="a3416-105">This tutorial teaches you the basics of building an ASP.NET Core MVC web app using [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/).</span></span> 

[!INCLUDE [consider RP](../../includes/razor.md)]

<span data-ttu-id="a3416-106">Bu öğreticinin 3 sürümü vardır:</span><span class="sxs-lookup"><span data-stu-id="a3416-106">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="a3416-107">macOS: [Mac için Visual Studio ile bir ASP.NET Core MVC uygulaması oluşturun](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="a3416-107">macOS: [Build an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="a3416-108">Windows: [Visual Studio ile ASP.NET Core MVC uygulaması oluşturun](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="a3416-108">Windows: [Build an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="a3416-109">Linux, macOS ve Windows: [Visual Studio Code ile ASP.NET Core MVC uygulaması oluşturun](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="a3416-109">Linux, macOS, and Windows: [Build an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a3416-110">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="a3416-110">Prerequisites</span></span>

[!INCLUDE [](~/includes/net-core-prereqs-macos.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="a3416-111">Bir web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="a3416-111">Create a web app</span></span>

<span data-ttu-id="a3416-112">Visual Studio'dan seçin **Dosya > Yeni Çözüm**.</span><span class="sxs-lookup"><span data-stu-id="a3416-112">From Visual Studio, select **File > New Solution**.</span></span>

![Yeni çözüm macOS](../first-web-api-mac/_static/sln.png)

<span data-ttu-id="a3416-114">Seçin **.NET Core uygulaması > ASP.NET Core > ASP.NET Core Web uygulaması (MVC) > sonraki**.</span><span class="sxs-lookup"><span data-stu-id="a3416-114">Select **.NET Core App >  ASP.NET Core > ASP.NET Core Web App (MVC) > Next**.</span></span>

![macOS yeni proje iletişim kutusu](start-mvc/1.png)

<span data-ttu-id="a3416-116">Projeyi adlandırın **MvcMovie**ve ardından **Oluştur**.</span><span class="sxs-lookup"><span data-stu-id="a3416-116">Name the project **MvcMovie**, and then select **Create**.</span></span>

![macOS yeni proje iletişim kutusu](start-mvc/2.png)

### <a name="launch-the-app"></a><span data-ttu-id="a3416-118">Uygulamayı başlatın</span><span class="sxs-lookup"><span data-stu-id="a3416-118">Launch the app</span></span>

<span data-ttu-id="a3416-119">Visual Studio'da **çalıştırın > hata ayıklama olmadan Başlat** uygulamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="a3416-119">In Visual Studio, select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="a3416-120">Visual Studio başlatır [Kestrel](xref:fundamentals/servers/index#kestrel), bir tarayıcı başlatır ve gider `http://localhost:port`burada *bağlantı noktası* bir rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="a3416-120">Visual Studio starts [Kestrel](xref:fundamentals/servers/index#kestrel), launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

![Yeni Proje ile tarayıcı](start-mvc/b1.png)

* <span data-ttu-id="a3416-122">Adres çubuğu gösterir `localhost:port#` gibi bir şey `example.com`.</span><span class="sxs-lookup"><span data-stu-id="a3416-122">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a3416-123">Çünkü `localhost` standart, yerel bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="a3416-123">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="a3416-124">Visual Studio, bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a3416-124">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="a3416-125">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="a3416-125">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="a3416-126">Uygulamada hata ayıklama veya hata ayıklama olmayan moddan başlatabilirsiniz **çalıştırma** menüsü.</span><span class="sxs-lookup"><span data-stu-id="a3416-126">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="a3416-127">Varsayılan şablonu size **hakkında giriş** ve **kişi** bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="a3416-127">The default template gives you **Home, About** and **Contact** links.</span></span> <span data-ttu-id="a3416-128">Yukarıdaki tarayıcı resimde, bu bağlantıları göstermez.</span><span class="sxs-lookup"><span data-stu-id="a3416-128">The browser image above doesn't show these links.</span></span> <span data-ttu-id="a3416-129">Tarayıcınız boyutuna bağlı olarak, gösterileceğinin Gezinti simgesine tıklamanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="a3416-129">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![Yeni Proje ile tarayıcı](start-mvc/b2.png)

<span data-ttu-id="a3416-131">Bu öğreticinin sonraki bölümünde, MVC konusunda bilgi ve biraz kod yazmaya başlayın.</span><span class="sxs-lookup"><span data-stu-id="a3416-131">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a3416-132">Next</span><span class="sxs-lookup"><span data-stu-id="a3416-132">Next</span></span>](adding-controller.md)  
