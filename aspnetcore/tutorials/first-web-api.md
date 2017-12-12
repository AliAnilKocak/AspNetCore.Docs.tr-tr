---
title: "Windows için ASP.NET Core ve Visual Studio ile Web API oluşturma"
author: rick-anderson
description: "ASP.NET Core MVC ve Windows için Visual Studio ile web API'si oluşturma"
keywords: "ASP.NET Core, Webapı, Web API'si, REST, HTTP, hizmeti, HTTP hizmeti"
ms.author: riande
manager: wpickett
ms.date: 08/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-web-api
ms.openlocfilehash: da47296fd952300ce60121603834a9f22be3c339
ms.sourcegitcommit: 703593d5fd14076e79be2ba75a5b8da12a60ab15
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/05/2017
---
#<a name="create-a-web-api-with-aspnet-core-and-visual-studio-for-windows"></a><span data-ttu-id="fb2df-104">ASP.NET Core ve Windows için Visual Studio ile web API oluşturma</span><span class="sxs-lookup"><span data-stu-id="fb2df-104">Create a web API with ASP.NET Core and Visual Studio for Windows</span></span>

<span data-ttu-id="fb2df-105">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [CAN Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="fb2df-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="fb2df-106">Bu öğretici, "Yapılacaklar" öğeleri listesini yönetmek için bir web API oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fb2df-106">This tutorial builds a web API for managing a list of "to-do" items.</span></span> <span data-ttu-id="fb2df-107">Bir kullanıcı arabirimi (UI) oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="fb2df-107">A user interface (UI) is not created.</span></span>

<span data-ttu-id="fb2df-108">Bu öğretici 3 sürümü vardır:</span><span class="sxs-lookup"><span data-stu-id="fb2df-108">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="fb2df-109">Windows: Web API ile Windows için Visual Studio (Bu öğretici)</span><span class="sxs-lookup"><span data-stu-id="fb2df-109">Windows: Web API with Visual Studio for Windows (This tutorial)</span></span>
* <span data-ttu-id="fb2df-110">macOS: [Mac için Visual Studio ile Web API](xref:tutorials/first-web-api-mac)</span><span class="sxs-lookup"><span data-stu-id="fb2df-110">macOS: [Web API with Visual Studio for Mac](xref:tutorials/first-web-api-mac)</span></span>
* <span data-ttu-id="fb2df-111">macOS, Linux, Windows: [Visual Studio Code ile Web API](xref:tutorials/web-api-vsc)</span><span class="sxs-lookup"><span data-stu-id="fb2df-111">macOS, Linux, Windows: [Web API with Visual Studio Code](xref:tutorials/web-api-vsc)</span></span>

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[intro to web API](../includes/webApi/intro.md)]

## <a name="prerequisites"></a><span data-ttu-id="fb2df-112">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="fb2df-112">Prerequisites</span></span>

[!INCLUDE[install 2.0](../includes/install2.0.md)]

<span data-ttu-id="fb2df-113">Bkz: [bu PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf) ASP.NET Core 1.1 sürümünün.</span><span class="sxs-lookup"><span data-stu-id="fb2df-113">See [this PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf) for the ASP.NET Core 1.1 version.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="fb2df-114">Projeyi oluşturma</span><span class="sxs-lookup"><span data-stu-id="fb2df-114">Create the project</span></span>

<span data-ttu-id="fb2df-115">Visual Studio'dan seçin **dosya** menüsünde > **yeni** > **proje**.</span><span class="sxs-lookup"><span data-stu-id="fb2df-115">From Visual Studio, select **File** menu, > **New** > **Project**.</span></span>

<span data-ttu-id="fb2df-116">Seçin **ASP.NET çekirdek Web uygulaması (.NET Core)** proje şablonu.</span><span class="sxs-lookup"><span data-stu-id="fb2df-116">Select the **ASP.NET Core Web Application (.NET Core)** project template.</span></span> <span data-ttu-id="fb2df-117">Proje adı `TodoApi` seçip **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="fb2df-117">Name the project `TodoApi` and select **OK**.</span></span>

![Yeni Proje iletişim kutusu](first-web-api/_static/new-project.png)

<span data-ttu-id="fb2df-119">İçinde **yeni ASP.NET çekirdek Web uygulaması - TodoApi** iletişim kutusunda **Web API** şablonu.</span><span class="sxs-lookup"><span data-stu-id="fb2df-119">In the **New ASP.NET Core Web Application - TodoApi** dialog, select the **Web API** template.</span></span> <span data-ttu-id="fb2df-120">Seçin **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="fb2df-120">Select **OK**.</span></span> <span data-ttu-id="fb2df-121">Yapmak **değil** seçin **Docker desteğini etkinleştir**.</span><span class="sxs-lookup"><span data-stu-id="fb2df-121">Do **not** select **Enable Docker Support**.</span></span>

![ASP.NET Core şablonlardan seçili Web API projesi şablonuyla yeni ASP.NET Web uygulaması iletişim kutusu](first-web-api/_static/web-api-project.png)

### <a name="launch-the-app"></a><span data-ttu-id="fb2df-123">Uygulamayı başlatın</span><span class="sxs-lookup"><span data-stu-id="fb2df-123">Launch the app</span></span>

<span data-ttu-id="fb2df-124">Visual Studio'da uygulamayı başlatmak için CTRL + F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="fb2df-124">In Visual Studio, press CTRL+F5 to launch the app.</span></span> <span data-ttu-id="fb2df-125">Visual Studio bir tarayıcı ile başlatarak `http://localhost:port/api/values`, burada *bağlantı noktası* rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="fb2df-125">Visual Studio launches a browser and navigates to `http://localhost:port/api/values`, where *port* is a randomly chosen port number.</span></span> <span data-ttu-id="fb2df-126">Chrome, Microsoft Edge ve Firefox aşağıdaki çıkış görüntüler:</span><span class="sxs-lookup"><span data-stu-id="fb2df-126">Chrome, Microsoft Edge, and Firefox display the following output:</span></span>

```
["value1","value2"]
```

### <a name="add-a-model-class"></a><span data-ttu-id="fb2df-127">Bir model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="fb2df-127">Add a model class</span></span>

<span data-ttu-id="fb2df-128">Uygulama verileri temsil eden bir nesne modelidir.</span><span class="sxs-lookup"><span data-stu-id="fb2df-128">A model is an object that represents the data in the app.</span></span> <span data-ttu-id="fb2df-129">Bu durumda, yalnızca bir Yapılacaklar öğesi modelidir.</span><span class="sxs-lookup"><span data-stu-id="fb2df-129">In this case, the only model is a to-do item.</span></span>

<span data-ttu-id="fb2df-130">"Modelleri" adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fb2df-130">Add a folder named "Models".</span></span> <span data-ttu-id="fb2df-131">Çözüm Gezgini'nde projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="fb2df-131">In Solution Explorer, right-click the project.</span></span> <span data-ttu-id="fb2df-132">Seçin **ekleme** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="fb2df-132">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="fb2df-133">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="fb2df-133">Name the folder *Models*.</span></span>

<span data-ttu-id="fb2df-134">Not: Modeli sınıfları projede herhangi bir yere gidin.</span><span class="sxs-lookup"><span data-stu-id="fb2df-134">Note: The model classes go anywhere in the project.</span></span> <span data-ttu-id="fb2df-135">*Modelleri* klasörü kural tarafından model sınıfları için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fb2df-135">The *Models* folder is used by convention for model classes.</span></span>

<span data-ttu-id="fb2df-136">Ekleme bir `TodoItem` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="fb2df-136">Add a `TodoItem` class.</span></span> <span data-ttu-id="fb2df-137">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="fb2df-137">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="fb2df-138">Sınıf adını `TodoItem` seçip **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="fb2df-138">Name the class `TodoItem` and select **Add**.</span></span>

<span data-ttu-id="fb2df-139">Güncelleştirme `TodoItem` aşağıdaki kodla sınıfı:</span><span class="sxs-lookup"><span data-stu-id="fb2df-139">Update the `TodoItem` class with the following code:</span></span>

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="fb2df-140">Veritabanı oluşturur `Id` zaman bir `TodoItem` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="fb2df-140">The database generates the `Id` when a `TodoItem` is created.</span></span>

### <a name="create-the-database-context"></a><span data-ttu-id="fb2df-141">Veritabanı bağlamı oluşturma</span><span class="sxs-lookup"><span data-stu-id="fb2df-141">Create the database context</span></span>

<span data-ttu-id="fb2df-142">*Veritabanı bağlamı* verilen veri modeli için Entity Framework işlevselliği koordinatları ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="fb2df-142">The *database context* is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="fb2df-143">Bu sınıf türetme tarafından oluşturulan `Microsoft.EntityFrameworkCore.DbContext` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="fb2df-143">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

<span data-ttu-id="fb2df-144">Ekleme bir `TodoContext` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="fb2df-144">Add a `TodoContext` class.</span></span> <span data-ttu-id="fb2df-145">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="fb2df-145">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="fb2df-146">Sınıf adını `TodoContext` seçip **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="fb2df-146">Name the class `TodoContext` and select **Add**.</span></span>

<span data-ttu-id="fb2df-147">Sınıfı aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="fb2df-147">Replace the class with the following code:</span></span>

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

### <a name="add-a-controller"></a><span data-ttu-id="fb2df-148">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="fb2df-148">Add a controller</span></span>

<span data-ttu-id="fb2df-149">Çözüm Gezgini'nde sağ *denetleyicileri* klasör.</span><span class="sxs-lookup"><span data-stu-id="fb2df-149">In Solution Explorer, right-click the *Controllers* folder.</span></span> <span data-ttu-id="fb2df-150">Seçin **ekleme** > **yeni öğe**.</span><span class="sxs-lookup"><span data-stu-id="fb2df-150">Select **Add** > **New Item**.</span></span> <span data-ttu-id="fb2df-151">İçinde **Yeni Öğe Ekle** iletişim kutusunda **Web API denetleyicisi sınıfı** şablonu.</span><span class="sxs-lookup"><span data-stu-id="fb2df-151">In the **Add New Item** dialog, select the **Web API Controller Class** template.</span></span> <span data-ttu-id="fb2df-152">Sınıf adını `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="fb2df-152">Name the class `TodoController`.</span></span>

![Yeni öğe iletişim denetleyicisiyle seçili arama kutusu ve web API denetleyicisi ekleyin](first-web-api/_static/new_controller.png)

<span data-ttu-id="fb2df-154">Sınıfı aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="fb2df-154">Replace the class with the following code:</span></span>

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a><span data-ttu-id="fb2df-155">Uygulamayı başlatın</span><span class="sxs-lookup"><span data-stu-id="fb2df-155">Launch the app</span></span>

<span data-ttu-id="fb2df-156">Visual Studio'da uygulamayı başlatmak için CTRL + F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="fb2df-156">In Visual Studio, press CTRL+F5 to launch the app.</span></span> <span data-ttu-id="fb2df-157">Visual Studio bir tarayıcı ile başlatarak `http://localhost:port/api/values`, burada *bağlantı noktası* rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="fb2df-157">Visual Studio launches a browser and navigates to `http://localhost:port/api/values`, where *port* is a randomly chosen port number.</span></span> <span data-ttu-id="fb2df-158">Gidin `Todo` denetleyicisinde `http://localhost:port/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="fb2df-158">Navigate to the `Todo` controller at `http://localhost:port/api/todo`.</span></span>

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]

