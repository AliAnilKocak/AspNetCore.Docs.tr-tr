---
title: "Öğretici: ASP.NET Core bir Web API 'SI oluşturma"
author: rick-anderson
description: ASP.NET Core ile Web API 'SI oluşturmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 96b4c030c1d91f97725d1f3623c7b4023ad99ff3
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880631"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="681a1-103">Öğretici: ASP.NET Core bir Web API 'SI oluşturma</span><span class="sxs-lookup"><span data-stu-id="681a1-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="681a1-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="681a1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="681a1-105">Bu öğretici, bir web API ASP.NET Core ile oluşturmaya ilişkin temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="681a1-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="681a1-106">Bu öğreticide şunların nasıl yapıladığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="681a1-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="681a1-107">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="681a1-107">Create a web API project.</span></span>
> * <span data-ttu-id="681a1-108">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="681a1-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="681a1-109">CRUD yöntemleriyle bir denetleyiciyi dolandırın.</span><span class="sxs-lookup"><span data-stu-id="681a1-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="681a1-110">Yönlendirmeyi, URL yollarını ve dönüş değerlerini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="681a1-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="681a1-111">Web API'si Postman ile çağırın.</span><span class="sxs-lookup"><span data-stu-id="681a1-111">Call the web API with Postman.</span></span>

<span data-ttu-id="681a1-112">Sonunda, bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="681a1-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="681a1-113">Genel bakış</span><span class="sxs-lookup"><span data-stu-id="681a1-113">Overview</span></span>

<span data-ttu-id="681a1-114">Bu öğretici yandaki API oluşturur:</span><span class="sxs-lookup"><span data-stu-id="681a1-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="681a1-115">API</span><span class="sxs-lookup"><span data-stu-id="681a1-115">API</span></span> | <span data-ttu-id="681a1-116">Açıklama</span><span class="sxs-lookup"><span data-stu-id="681a1-116">Description</span></span> | <span data-ttu-id="681a1-117">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="681a1-117">Request body</span></span> | <span data-ttu-id="681a1-118">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="681a1-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="681a1-119">/Api/TodoItems al</span><span class="sxs-lookup"><span data-stu-id="681a1-119">GET /api/TodoItems</span></span> | <span data-ttu-id="681a1-120">Tüm yapılacak iş öğeleri al</span><span class="sxs-lookup"><span data-stu-id="681a1-120">Get all to-do items</span></span> | <span data-ttu-id="681a1-121">Yok.</span><span class="sxs-lookup"><span data-stu-id="681a1-121">None</span></span> | <span data-ttu-id="681a1-122">Yapılacaklar öğelerinin bir dizisi</span><span class="sxs-lookup"><span data-stu-id="681a1-122">Array of to-do items</span></span>|
|<span data-ttu-id="681a1-123">/Api/TodoItems/{id} al</span><span class="sxs-lookup"><span data-stu-id="681a1-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="681a1-124">Bir öğeyi Kimliğine göre Al</span><span class="sxs-lookup"><span data-stu-id="681a1-124">Get an item by ID</span></span> | <span data-ttu-id="681a1-125">Yok.</span><span class="sxs-lookup"><span data-stu-id="681a1-125">None</span></span> | <span data-ttu-id="681a1-126">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="681a1-126">To-do item</span></span>|
|<span data-ttu-id="681a1-127">POST/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="681a1-127">POST /api/TodoItems</span></span> | <span data-ttu-id="681a1-128">Yeni Öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="681a1-128">Add a new item</span></span> | <span data-ttu-id="681a1-129">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="681a1-129">To-do item</span></span> | <span data-ttu-id="681a1-130">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="681a1-130">To-do item</span></span> |
|<span data-ttu-id="681a1-131">/Api/TodoItems/{id} koy</span><span class="sxs-lookup"><span data-stu-id="681a1-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="681a1-132">Mevcut öğeyi güncelleştirin &nbsp;</span><span class="sxs-lookup"><span data-stu-id="681a1-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="681a1-133">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="681a1-133">To-do item</span></span> | <span data-ttu-id="681a1-134">Yok.</span><span class="sxs-lookup"><span data-stu-id="681a1-134">None</span></span> |
|<span data-ttu-id="681a1-135">/Api/TodoItems/{id} &nbsp; SIL &nbsp;</span><span class="sxs-lookup"><span data-stu-id="681a1-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="681a1-136">Öğeyi Sil &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="681a1-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="681a1-137">Yok.</span><span class="sxs-lookup"><span data-stu-id="681a1-137">None</span></span> | <span data-ttu-id="681a1-138">Yok.</span><span class="sxs-lookup"><span data-stu-id="681a1-138">None</span></span>|

<span data-ttu-id="681a1-139">Aşağıdaki diyagramda, bu uygulamanın tasarımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="681a1-139">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="681a1-145">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="681a1-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="681a1-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="681a1-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="681a1-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="681a1-148">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="681a1-149">Bir web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="681a1-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="681a1-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="681a1-151">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="681a1-152">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="681a1-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="681a1-153">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="681a1-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="681a1-154">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,0** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="681a1-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="681a1-155">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="681a1-155">Select the **API** template and click **Create**.</span></span>

![VS yeni proje iletişim kutusu](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="681a1-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="681a1-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="681a1-158">Açık [tümleşik Terminalini](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="681a1-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="681a1-159">Dizinleri (`cd`) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="681a1-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="681a1-160">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="681a1-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="681a1-161">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="681a1-162">Yukarıdaki komutlar:</span><span class="sxs-lookup"><span data-stu-id="681a1-162">The preceding commands:</span></span>

  * <span data-ttu-id="681a1-163">Yeni bir Web API projesi oluşturur ve Visual Studio Code açar.</span><span class="sxs-lookup"><span data-stu-id="681a1-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="681a1-164">Sonraki bölümde gerekli olan NuGet paketlerini ekler.</span><span class="sxs-lookup"><span data-stu-id="681a1-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="681a1-165">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="681a1-166">**Yeni çözüm**> **Dosya** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-166">Select **File** > **New Solution**.</span></span>

  ![Yeni çözüm macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="681a1-168">**.NET Core** > **App** > **API** > ' **yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="681a1-170">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda, **hedef Framework** \* *.NET Core 3,0*' i seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="681a1-171">Girin *TodoApi* için **proje adı** seçip **Oluştur**.</span><span class="sxs-lookup"><span data-stu-id="681a1-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="681a1-173">Proje klasöründe bir komut terminali açın ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="681a1-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="681a1-174">API'yi test etme</span><span class="sxs-lookup"><span data-stu-id="681a1-174">Test the API</span></span>

<span data-ttu-id="681a1-175">Proje şablonu oluşturur bir `WeatherForecast` API.</span><span class="sxs-lookup"><span data-stu-id="681a1-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="681a1-176">Çağrı `Get` uygulamayı test etmek için bir tarayıcıdan yöntemi.</span><span class="sxs-lookup"><span data-stu-id="681a1-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="681a1-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="681a1-178">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="681a1-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="681a1-179">Visual Studio bir tarayıcı ile başlatarak `https://localhost:<port>/WeatherForecast`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="681a1-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="681a1-180">IIS Express sertifika güven varsa soran bir iletişim kutusu alırsanız seçin **Evet**.</span><span class="sxs-lookup"><span data-stu-id="681a1-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="681a1-181">İçinde **Güvenlik Uyarısı** ardından, görüntülenen iletişim seçin **Evet**.</span><span class="sxs-lookup"><span data-stu-id="681a1-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="681a1-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="681a1-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="681a1-183">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="681a1-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="681a1-184">Bir tarayıcıda aşağıdaki URL'ye gidin: [ https://localhost:5001/WeatherForecast ](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="681a1-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="681a1-185">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="681a1-186">Uygulamayı başlatmak için **hata ayıklamayı başlat** > **Çalıştır** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="681a1-187">Mac için Visual Studio bir tarayıcı ile başlatarak `https://localhost:<port>`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="681a1-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="681a1-188">HTTP 404 (bulunamadı) hatası döndürülür.</span><span class="sxs-lookup"><span data-stu-id="681a1-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="681a1-189">Append `/WeatherForecast` URL'sine (URL'yi `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="681a1-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="681a1-190">Aşağıdakine benzer bir JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="681a1-190">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="681a1-191">Bir model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="681a1-191">Add a model class</span></span>

<span data-ttu-id="681a1-192">A *modeli* uygulamayı yöneten verilerini temsil eden sınıflar kümesidir.</span><span class="sxs-lookup"><span data-stu-id="681a1-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="681a1-193">Tek bir modeldir bu uygulama için `TodoItem` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="681a1-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="681a1-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="681a1-195">İçinde **Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="681a1-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="681a1-196">Seçin **ekleme** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="681a1-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="681a1-197">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="681a1-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="681a1-198">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="681a1-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="681a1-199">Sınıf adı *Todoıtem* seçip **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="681a1-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="681a1-200">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="681a1-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="681a1-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="681a1-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="681a1-202">Adlı bir klasör ekleme *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="681a1-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="681a1-203">Ekleme bir `TodoItem` sınıfının *modelleri* aşağıdaki kodla klasörü:</span><span class="sxs-lookup"><span data-stu-id="681a1-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="681a1-204">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="681a1-205">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="681a1-205">Right-click the project.</span></span> <span data-ttu-id="681a1-206">Seçin **ekleme** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="681a1-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="681a1-207">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="681a1-207">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="681a1-209">*Modeller* klasörüne sağ tıklayın ve > **yeni dosya** **ekle** ' yi > **genel** > **boş sınıf**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="681a1-210">Sınıf adı *Todoıtem*ve ardından **yeni**.</span><span class="sxs-lookup"><span data-stu-id="681a1-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="681a1-211">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="681a1-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="681a1-212">`Id` Özelliği işlevlerinin bir ilişkisel veritabanında benzersiz anahtar.</span><span class="sxs-lookup"><span data-stu-id="681a1-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="681a1-213">Model sınıfları herhangi bir projede gidip ancak *modelleri* klasörü, kural olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="681a1-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="681a1-214">Veritabanı bağlamı Ekle</span><span class="sxs-lookup"><span data-stu-id="681a1-214">Add a database context</span></span>

<span data-ttu-id="681a1-215">*Veritabanı bağlamı* koordine eden bir veri modeli için Entity Framework işlevsellik ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="681a1-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="681a1-216">Bu sınıf türetme tarafından oluşturulan `Microsoft.EntityFrameworkCore.DbContext` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="681a1-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="681a1-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="681a1-218">Microsoft. EntityFrameworkCore. SqlServer ekleyin</span><span class="sxs-lookup"><span data-stu-id="681a1-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="681a1-219">**Araçlar** menüsünde **nuget Paket Yöneticisi > çözüm Için NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="681a1-220">**Araştır** sekmesini seçin ve arama kutusuna **Microsoft. Entityframeworkcore. SqlServer** yazın.</span><span class="sxs-lookup"><span data-stu-id="681a1-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="681a1-221">Sol bölmedeki **Microsoft. EntityFrameworkCore. SqlServer** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="681a1-222">Sağ bölmedeki **Proje** onay kutusunu seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="681a1-223">`Microsoft.EntityFrameworkCore.InMemory` NuGet paketini eklemek için yukarıdaki yönergeleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="681a1-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet Paket Yöneticisi](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="681a1-225">TodoContext veritabanı bağlamını ekleme</span><span class="sxs-lookup"><span data-stu-id="681a1-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="681a1-226">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="681a1-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="681a1-227">Sınıf adı *TodoContext* tıklatıp **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="681a1-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="681a1-228">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="681a1-229">Ekleme bir `TodoContext` sınıfının *modelleri* klasör.</span><span class="sxs-lookup"><span data-stu-id="681a1-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="681a1-230">Aşağıdaki kodu girin:</span><span class="sxs-lookup"><span data-stu-id="681a1-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="681a1-231">Veritabanı bağlamı Kaydet</span><span class="sxs-lookup"><span data-stu-id="681a1-231">Register the database context</span></span>

<span data-ttu-id="681a1-232">ASP.NET Core DB bağlamı gibi hizmetler ile kaydedilmelidir [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcı.</span><span class="sxs-lookup"><span data-stu-id="681a1-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="681a1-233">Kapsayıcı hizmeti denetleyicilerine sağlar.</span><span class="sxs-lookup"><span data-stu-id="681a1-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="681a1-234">Güncelleştirme *Startup.cs* aşağıdaki vurgulanmış kodu:</span><span class="sxs-lookup"><span data-stu-id="681a1-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="681a1-235">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="681a1-235">The preceding code:</span></span>

* <span data-ttu-id="681a1-236">Kullanılmayan kaldırır `using` bildirimleri.</span><span class="sxs-lookup"><span data-stu-id="681a1-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="681a1-237">Veritabanı bağlamı DI kapsayıcıya ekler.</span><span class="sxs-lookup"><span data-stu-id="681a1-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="681a1-238">Veritabanı bağlamı bir bellek içi veritabanına kullanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="681a1-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="681a1-239">Denetleyiciyi bir denetleyiciye katlama</span><span class="sxs-lookup"><span data-stu-id="681a1-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="681a1-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="681a1-241">Sağ *denetleyicileri* klasör.</span><span class="sxs-lookup"><span data-stu-id="681a1-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="681a1-242">> **yeni yapı Iskelesi öğesi** **Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="681a1-243">**Entity Framework kullanarak ve eylemler Içeren API denetleyicisi**' ni seçin ve ardından **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="681a1-244">**API denetleyiciyi eylemler Ile Ekle ' de Entity Framework** iletişim kutusunu kullanarak:</span><span class="sxs-lookup"><span data-stu-id="681a1-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="681a1-245">**Model sınıfında** **TodoItem (TodoApi. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="681a1-246">**Veri bağlamı sınıfında** **TodoContext (TodoApi. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="681a1-247">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="681a1-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="681a1-248">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="681a1-249">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="681a1-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="681a1-250">Yukarıdaki komutlar:</span><span class="sxs-lookup"><span data-stu-id="681a1-250">The preceding commands:</span></span>

* <span data-ttu-id="681a1-251">Yapı iskelesi için gereken NuGet paketlerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="681a1-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="681a1-252">Scafkatlama altyapısını (`dotnet-aspnet-codegenerator`) kurar.</span><span class="sxs-lookup"><span data-stu-id="681a1-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="681a1-253">`TodoItemsController`yapı iskelesi.</span><span class="sxs-lookup"><span data-stu-id="681a1-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="681a1-254">Oluşturulan kod:</span><span class="sxs-lookup"><span data-stu-id="681a1-254">The generated code:</span></span>

* <span data-ttu-id="681a1-255">Bir API denetleyicisi sınıfı yöntemleri olmadan tanımlar.</span><span class="sxs-lookup"><span data-stu-id="681a1-255">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="681a1-256">Sınıfı [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="681a1-256">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="681a1-257">Bu öznitelik, denetleyicinin web API'si isteklerine yanıt verdiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="681a1-257">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="681a1-258">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz. <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="681a1-258">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="681a1-259">Veritabanı bağlamı eklemesine DI kullanır (`TodoContext`) içine denetleyici.</span><span class="sxs-lookup"><span data-stu-id="681a1-259">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="681a1-260">Her bir veritabanı bağlamı kullanılan [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) denetleyici yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="681a1-260">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="681a1-261">PostTodoItem Create metodunu inceleyin</span><span class="sxs-lookup"><span data-stu-id="681a1-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="681a1-262">`PostTodoItem` ' deki return ifadesini, [NameOf](/dotnet/csharp/language-reference/operators/nameof) işlecini kullanacak şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="681a1-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="681a1-263">Yukarıdaki kod, [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) özniteliğiyle gösterildiği gıbı bır http post yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="681a1-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="681a1-264">Yöntemi, HTTP isteği gövdesinden Yapılacaklar öğenin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="681a1-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="681a1-265"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="681a1-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="681a1-266">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="681a1-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="681a1-267">HTTP 201 sunucuda yeni bir kaynak oluşturan bir HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="681a1-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="681a1-268">Yanıta bir [konum](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) üst bilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="681a1-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="681a1-269">`Location` üstbilgisi, yeni oluşturulan Yapılacaklar öğesinin [URI](https://developer.mozilla.org/docs/Glossary/URI) 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="681a1-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="681a1-270">Daha fazla bilgi için [10.2.2 201 oluşturuldu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="681a1-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="681a1-271">`Location` üst bilgisinin URI 'sini oluşturmak için `GetTodoItem` eyleme başvurur.</span><span class="sxs-lookup"><span data-stu-id="681a1-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="681a1-272">C# `nameof` anahtar sözcüğü, `CreatedAtAction` çağrısında eylem adının sabit kodlanmasını önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="681a1-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="681a1-273">Postman yükleme</span><span class="sxs-lookup"><span data-stu-id="681a1-273">Install Postman</span></span>

<span data-ttu-id="681a1-274">Bu öğreticide Postman web API'si test etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="681a1-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="681a1-275">Yükleme [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="681a1-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="681a1-276">Web uygulaması başlatın.</span><span class="sxs-lookup"><span data-stu-id="681a1-276">Start the web app.</span></span>
* <span data-ttu-id="681a1-277">Postman'i başlatın.</span><span class="sxs-lookup"><span data-stu-id="681a1-277">Start Postman.</span></span>
* <span data-ttu-id="681a1-278">Devre dışı **SSL sertifika doğrulama**</span><span class="sxs-lookup"><span data-stu-id="681a1-278">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="681a1-279">**Dosya** > **ayarları** ' ndan (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="681a1-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="681a1-280">Test denetleyicisi sonra SSL sertifika doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="681a1-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="681a1-281">Postman ile test PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="681a1-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="681a1-282">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="681a1-282">Create a new request.</span></span>
* <span data-ttu-id="681a1-283">HTTP yöntemini `POST`olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="681a1-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="681a1-284">Seçin **gövdesi** sekmesi.</span><span class="sxs-lookup"><span data-stu-id="681a1-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="681a1-285">Seçin **ham** radyo düğmesi.</span><span class="sxs-lookup"><span data-stu-id="681a1-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="681a1-286">Tür kümesine **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="681a1-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="681a1-287">İstek gövdesinde bir yapılacak iş öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="681a1-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="681a1-288">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-288">Select **Send**.</span></span>

  ![Postman ile isteği oluştur](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="681a1-290">Konum üst bilgisi URI test</span><span class="sxs-lookup"><span data-stu-id="681a1-290">Test the location header URI</span></span>

* <span data-ttu-id="681a1-291">Seçin **üstbilgileri** sekmesinde **yanıt** bölmesi.</span><span class="sxs-lookup"><span data-stu-id="681a1-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="681a1-292">Kopyalama **konumu** üst bilgi değeri:</span><span class="sxs-lookup"><span data-stu-id="681a1-292">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üst bilgiler sekmesi](first-web-api/_static/3/create.png)

* <span data-ttu-id="681a1-294">Yöntemini GET öğesine Ayarla.</span><span class="sxs-lookup"><span data-stu-id="681a1-294">Set the method to GET.</span></span>
* <span data-ttu-id="681a1-295">URI 'yi yapıştırın (örneğin, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="681a1-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="681a1-296">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="681a1-297">GET yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="681a1-297">Examine the GET methods</span></span>

<span data-ttu-id="681a1-298">İki GET uç noktası bu yöntemleri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="681a1-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="681a1-299">Tarayıcıdan veya Postman 'dan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="681a1-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="681a1-300">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="681a1-300">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="681a1-301">Aşağıdakine benzer bir yanıt, `GetTodoItems`çağrısı tarafından üretilir:</span><span class="sxs-lookup"><span data-stu-id="681a1-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="681a1-302">Postman ile test al</span><span class="sxs-lookup"><span data-stu-id="681a1-302">Test Get with Postman</span></span>

* <span data-ttu-id="681a1-303">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="681a1-303">Create a new request.</span></span>
* <span data-ttu-id="681a1-304">HTTP yöntemi kümesine **alma**.</span><span class="sxs-lookup"><span data-stu-id="681a1-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="681a1-305">İstek URL'si kümesine `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="681a1-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="681a1-306">Örneğin: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="681a1-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="681a1-307">Ayarlama **iki bölme görünümü** postman'deki.</span><span class="sxs-lookup"><span data-stu-id="681a1-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="681a1-308">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-308">Select **Send**.</span></span>

<span data-ttu-id="681a1-309">Bu uygulama, bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="681a1-309">This app uses an in-memory database.</span></span> <span data-ttu-id="681a1-310">Uygulama durdurulup başlatılırsa, önceki GET isteği herhangi bir veri döndürmez.</span><span class="sxs-lookup"><span data-stu-id="681a1-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="681a1-311">Hiçbir veri döndürülmezse, verileri uygulamaya [gönderin](#post) .</span><span class="sxs-lookup"><span data-stu-id="681a1-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="681a1-312">URL Yönlendirme ve yolları</span><span class="sxs-lookup"><span data-stu-id="681a1-312">Routing and URL paths</span></span>

<span data-ttu-id="681a1-313">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Özniteliği bir HTTP GET isteğine yanıt vermeden bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="681a1-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="681a1-314">Her yöntem için URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="681a1-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="681a1-315">Denetleyicinin şablonu dizesi ile başlayıp `Route` özniteliği:</span><span class="sxs-lookup"><span data-stu-id="681a1-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="681a1-316">Değiştirin `[controller]` denetleyicinin adı ile kural tarafından olduğu "Controller" soneki eksi denetleyici sınıfı adı.</span><span class="sxs-lookup"><span data-stu-id="681a1-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="681a1-317">Bu örnek için denetleyici sınıfı adı **todoıtems**denetleyicisidir, bu nedenle denetleyicinin adı "todoıtems" olur.</span><span class="sxs-lookup"><span data-stu-id="681a1-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="681a1-318">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="681a1-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="681a1-319">`[HttpGet]` özniteliğinin bir yol şablonu varsa (örneğin, `[HttpGet("products")]`), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="681a1-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="681a1-320">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="681a1-320">This sample doesn't use a template.</span></span> <span data-ttu-id="681a1-321">Daha fazla bilgi için [özniteliği Http [eylem] özniteliği ile yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="681a1-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="681a1-322">Aşağıdaki `GetTodoItem` yöntemi `"{id}"` yapılacak iş öğesi benzersiz tanımlayıcısı için bir yer tutucu değişkendir.</span><span class="sxs-lookup"><span data-stu-id="681a1-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="681a1-323">`GetTodoItem` çağrıldığında, URL 'deki `"{id}"` değeri `id` parametresindeki yöntemine sağlanır.</span><span class="sxs-lookup"><span data-stu-id="681a1-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="681a1-324">Döndürülen değerler</span><span class="sxs-lookup"><span data-stu-id="681a1-324">Return values</span></span>

<span data-ttu-id="681a1-325">Dönüş türünü `GetTodoItems` ve `GetTodoItem` yöntemler [actionresult öğesini\<T > türü](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="681a1-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="681a1-326">ASP.NET Core, nesneyi otomatik olarak serileştiren [JSON](https://www.json.org/) ve yanıt iletisinin gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="681a1-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="681a1-327">Yanıt kodu 200 bu dönüş türü için olduğu varsayılırsa işlenmeyen özel durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="681a1-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="681a1-328">İşlenmeyen özel durumları 5xx hatalarla karşılaşırsanız çevrilir.</span><span class="sxs-lookup"><span data-stu-id="681a1-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="681a1-329">`ActionResult` dönüş türleri, geniş HTTP durum kodları temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="681a1-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="681a1-330">Örneğin, `GetTodoItem` iki farklı durum değerleri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="681a1-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="681a1-331">Öğe istenen kimliği eşleşirse, yöntem bir 404 döndürür [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) hata kodu.</span><span class="sxs-lookup"><span data-stu-id="681a1-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="681a1-332">Aksi takdirde yöntem bir JSON yanıt gövdesine 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="681a1-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="681a1-333">Döndüren `item` sonuçları bir HTTP 200 yanıtı.</span><span class="sxs-lookup"><span data-stu-id="681a1-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="681a1-334">PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="681a1-334">The PutTodoItem method</span></span>

<span data-ttu-id="681a1-335">`PutTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="681a1-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="681a1-336">`PutTodoItem` benzer `PostTodoItem`, HTTP PUT kullanır.</span><span class="sxs-lookup"><span data-stu-id="681a1-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="681a1-337">Yanıt [204 (içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="681a1-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="681a1-338">HTTP belirtimine göre bir PUT İsteği tüm güncelleştirilmiş varlık yalnızca değişiklikler değil göndermek istemci gerektirir.</span><span class="sxs-lookup"><span data-stu-id="681a1-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="681a1-339">Kısmi güncelleştirmeleri desteklemek için kullanma [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="681a1-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="681a1-340">`PutTodoItem`çağırırken bir hata alırsanız, veritabanında bir öğe olduğundan emin olmak için `GET` çağırın.</span><span class="sxs-lookup"><span data-stu-id="681a1-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="681a1-341">Test PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="681a1-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="681a1-342">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="681a1-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="681a1-343">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="681a1-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="681a1-344">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="681a1-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="681a1-345">ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="681a1-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="681a1-346">Aşağıdaki görüntüde, Postman güncelleştirme gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="681a1-346">The following image shows the Postman update:</span></span>

![204 (içerik yok) yanıtı gösteren postman konsol](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="681a1-348">DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="681a1-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="681a1-349">`DeleteTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="681a1-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="681a1-350">`DeleteTodoItem` Yanıt [204 (içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="681a1-350">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="681a1-351">Test DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="681a1-351">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="681a1-352">Postman bir yapılacak iş öğesini silmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="681a1-352">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="681a1-353">Yöntem kümesine `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="681a1-353">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="681a1-354">Silinecek nesnenin URI 'sini ayarlayın (örneğin `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="681a1-354">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="681a1-355">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-355">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="681a1-356">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="681a1-356">Call the web API with JavaScript</span></span>

<span data-ttu-id="681a1-357">Bkz. [öğretici: JavaScript ile ASP.NET Core Web API 'Si çağırma](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="681a1-357">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="681a1-358">Bu öğreticide şunların nasıl yapıladığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="681a1-358">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="681a1-359">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="681a1-359">Create a web API project.</span></span>
> * <span data-ttu-id="681a1-360">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="681a1-360">Add a model class and a database context.</span></span>
> * <span data-ttu-id="681a1-361">Bir denetleyici ekleyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="681a1-361">Add a controller.</span></span>
> * <span data-ttu-id="681a1-362">CRUD yöntemleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="681a1-362">Add CRUD methods.</span></span>
> * <span data-ttu-id="681a1-363">Yönlendirmeyi Yapılandırma ve URL yolu.</span><span class="sxs-lookup"><span data-stu-id="681a1-363">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="681a1-364">Dönüş değerleri belirtin.</span><span class="sxs-lookup"><span data-stu-id="681a1-364">Specify return values.</span></span>
> * <span data-ttu-id="681a1-365">Web API'si Postman ile çağırın.</span><span class="sxs-lookup"><span data-stu-id="681a1-365">Call the web API with Postman.</span></span>
> * <span data-ttu-id="681a1-366">JavaScript ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="681a1-366">Call the web API with JavaScript.</span></span>

<span data-ttu-id="681a1-367">Sonunda, web API'si "Yapılacaklar" öğelerini ilişkisel bir veritabanında depolanan yönetebileceği sahip.</span><span class="sxs-lookup"><span data-stu-id="681a1-367">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="681a1-368">Genel bakış</span><span class="sxs-lookup"><span data-stu-id="681a1-368">Overview</span></span>

<span data-ttu-id="681a1-369">Bu öğretici yandaki API oluşturur:</span><span class="sxs-lookup"><span data-stu-id="681a1-369">This tutorial creates the following API:</span></span>

|<span data-ttu-id="681a1-370">API</span><span class="sxs-lookup"><span data-stu-id="681a1-370">API</span></span> | <span data-ttu-id="681a1-371">Açıklama</span><span class="sxs-lookup"><span data-stu-id="681a1-371">Description</span></span> | <span data-ttu-id="681a1-372">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="681a1-372">Request body</span></span> | <span data-ttu-id="681a1-373">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="681a1-373">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="681a1-374">/Api/TodoItems al</span><span class="sxs-lookup"><span data-stu-id="681a1-374">GET /api/TodoItems</span></span> | <span data-ttu-id="681a1-375">Tüm yapılacak iş öğeleri al</span><span class="sxs-lookup"><span data-stu-id="681a1-375">Get all to-do items</span></span> | <span data-ttu-id="681a1-376">Yok.</span><span class="sxs-lookup"><span data-stu-id="681a1-376">None</span></span> | <span data-ttu-id="681a1-377">Yapılacaklar öğelerinin bir dizisi</span><span class="sxs-lookup"><span data-stu-id="681a1-377">Array of to-do items</span></span>|
|<span data-ttu-id="681a1-378">/Api/TodoItems/{id} al</span><span class="sxs-lookup"><span data-stu-id="681a1-378">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="681a1-379">Bir öğeyi Kimliğine göre Al</span><span class="sxs-lookup"><span data-stu-id="681a1-379">Get an item by ID</span></span> | <span data-ttu-id="681a1-380">Yok.</span><span class="sxs-lookup"><span data-stu-id="681a1-380">None</span></span> | <span data-ttu-id="681a1-381">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="681a1-381">To-do item</span></span>|
|<span data-ttu-id="681a1-382">POST/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="681a1-382">POST /api/TodoItems</span></span> | <span data-ttu-id="681a1-383">Yeni Öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="681a1-383">Add a new item</span></span> | <span data-ttu-id="681a1-384">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="681a1-384">To-do item</span></span> | <span data-ttu-id="681a1-385">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="681a1-385">To-do item</span></span> |
|<span data-ttu-id="681a1-386">/Api/TodoItems/{id} koy</span><span class="sxs-lookup"><span data-stu-id="681a1-386">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="681a1-387">Mevcut öğeyi güncelleştirin &nbsp;</span><span class="sxs-lookup"><span data-stu-id="681a1-387">Update an existing item &nbsp;</span></span> | <span data-ttu-id="681a1-388">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="681a1-388">To-do item</span></span> | <span data-ttu-id="681a1-389">Yok.</span><span class="sxs-lookup"><span data-stu-id="681a1-389">None</span></span> |
|<span data-ttu-id="681a1-390">/Api/TodoItems/{id} &nbsp; SIL &nbsp;</span><span class="sxs-lookup"><span data-stu-id="681a1-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="681a1-391">Öğeyi Sil &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="681a1-391">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="681a1-392">Yok.</span><span class="sxs-lookup"><span data-stu-id="681a1-392">None</span></span> | <span data-ttu-id="681a1-393">Yok.</span><span class="sxs-lookup"><span data-stu-id="681a1-393">None</span></span>|

<span data-ttu-id="681a1-394">Aşağıdaki diyagramda, bu uygulamanın tasarımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="681a1-394">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="681a1-400">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="681a1-400">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="681a1-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-401">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="681a1-402">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="681a1-402">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="681a1-403">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-403">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="681a1-404">Bir web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="681a1-404">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="681a1-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-405">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="681a1-406">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-406">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="681a1-407">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="681a1-407">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="681a1-408">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="681a1-408">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="681a1-409">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 2,2** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="681a1-409">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="681a1-410">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="681a1-410">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="681a1-411">**Docker desteğini etkinleştir** **' i seçmeyin** .</span><span class="sxs-lookup"><span data-stu-id="681a1-411">**Don't** select **Enable Docker Support**.</span></span>

![VS yeni proje iletişim kutusu](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="681a1-413">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="681a1-413">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="681a1-414">Açık [tümleşik Terminalini](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="681a1-414">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="681a1-415">Dizinleri (`cd`) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="681a1-415">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="681a1-416">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="681a1-416">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="681a1-417">Bu komutlar yeni bir Web API projesi oluşturur ve yeni proje klasöründe Visual Studio Code yeni bir örneğini açar.</span><span class="sxs-lookup"><span data-stu-id="681a1-417">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="681a1-418">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-418">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="681a1-419">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-419">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="681a1-420">**Yeni çözüm**> **Dosya** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-420">Select **File** > **New Solution**.</span></span>

  ![Yeni çözüm macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="681a1-422">**.NET Core** > **App** > **API** > ' **yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-422">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="681a1-424">İçinde **, yeni ASP.NET Core Web API'sini yapılandırma** iletişim kutusunda varsayılan değerleri kabul **hedef Framework'ü** , \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="681a1-424">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="681a1-425">Girin *TodoApi* için **proje adı** seçip **Oluştur**.</span><span class="sxs-lookup"><span data-stu-id="681a1-425">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="681a1-427">API'yi test etme</span><span class="sxs-lookup"><span data-stu-id="681a1-427">Test the API</span></span>

<span data-ttu-id="681a1-428">Proje şablonu oluşturur bir `values` API.</span><span class="sxs-lookup"><span data-stu-id="681a1-428">The project template creates a `values` API.</span></span> <span data-ttu-id="681a1-429">Çağrı `Get` uygulamayı test etmek için bir tarayıcıdan yöntemi.</span><span class="sxs-lookup"><span data-stu-id="681a1-429">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="681a1-430">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-430">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="681a1-431">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="681a1-431">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="681a1-432">Visual Studio bir tarayıcı ile başlatarak `https://localhost:<port>/api/values`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="681a1-432">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="681a1-433">IIS Express sertifika güven varsa soran bir iletişim kutusu alırsanız seçin **Evet**.</span><span class="sxs-lookup"><span data-stu-id="681a1-433">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="681a1-434">İçinde **Güvenlik Uyarısı** ardından, görüntülenen iletişim seçin **Evet**.</span><span class="sxs-lookup"><span data-stu-id="681a1-434">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="681a1-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="681a1-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="681a1-436">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="681a1-436">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="681a1-437">Bir tarayıcıda aşağıdaki URL'ye gidin: [ https://localhost:5001/api/values ](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="681a1-437">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="681a1-438">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="681a1-439">Uygulamayı başlatmak için **hata ayıklamayı başlat** > **Çalıştır** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-439">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="681a1-440">Mac için Visual Studio bir tarayıcı ile başlatarak `https://localhost:<port>`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="681a1-440">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="681a1-441">HTTP 404 (bulunamadı) hatası döndürülür.</span><span class="sxs-lookup"><span data-stu-id="681a1-441">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="681a1-442">Append `/api/values` URL'sine (URL'yi `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="681a1-442">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="681a1-443">Aşağıdaki JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="681a1-443">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="681a1-444">Bir model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="681a1-444">Add a model class</span></span>

<span data-ttu-id="681a1-445">A *modeli* uygulamayı yöneten verilerini temsil eden sınıflar kümesidir.</span><span class="sxs-lookup"><span data-stu-id="681a1-445">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="681a1-446">Tek bir modeldir bu uygulama için `TodoItem` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="681a1-446">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="681a1-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-447">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="681a1-448">İçinde **Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="681a1-448">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="681a1-449">Seçin **ekleme** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="681a1-449">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="681a1-450">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="681a1-450">Name the folder *Models*.</span></span>

* <span data-ttu-id="681a1-451">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="681a1-451">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="681a1-452">Sınıf adı *Todoıtem* seçip **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="681a1-452">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="681a1-453">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="681a1-453">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="681a1-454">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="681a1-454">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="681a1-455">Adlı bir klasör ekleme *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="681a1-455">Add a folder named *Models*.</span></span>

* <span data-ttu-id="681a1-456">Ekleme bir `TodoItem` sınıfının *modelleri* aşağıdaki kodla klasörü:</span><span class="sxs-lookup"><span data-stu-id="681a1-456">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="681a1-457">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-457">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="681a1-458">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="681a1-458">Right-click the project.</span></span> <span data-ttu-id="681a1-459">Seçin **ekleme** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="681a1-459">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="681a1-460">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="681a1-460">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="681a1-462">*Modeller* klasörüne sağ tıklayın ve > **yeni dosya** **ekle** ' yi > **genel** > **boş sınıf**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-462">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="681a1-463">Sınıf adı *Todoıtem*ve ardından **yeni**.</span><span class="sxs-lookup"><span data-stu-id="681a1-463">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="681a1-464">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="681a1-464">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="681a1-465">`Id` Özelliği işlevlerinin bir ilişkisel veritabanında benzersiz anahtar.</span><span class="sxs-lookup"><span data-stu-id="681a1-465">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="681a1-466">Model sınıfları herhangi bir projede gidip ancak *modelleri* klasörü, kural olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="681a1-466">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="681a1-467">Veritabanı bağlamı Ekle</span><span class="sxs-lookup"><span data-stu-id="681a1-467">Add a database context</span></span>

<span data-ttu-id="681a1-468">*Veritabanı bağlamı* koordine eden bir veri modeli için Entity Framework işlevsellik ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="681a1-468">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="681a1-469">Bu sınıf türetme tarafından oluşturulan `Microsoft.EntityFrameworkCore.DbContext` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="681a1-469">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="681a1-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-470">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="681a1-471">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="681a1-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="681a1-472">Sınıf adı *TodoContext* tıklatıp **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="681a1-472">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="681a1-473">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="681a1-474">Ekleme bir `TodoContext` sınıfının *modelleri* klasör.</span><span class="sxs-lookup"><span data-stu-id="681a1-474">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="681a1-475">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="681a1-475">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="681a1-476">Veritabanı bağlamı Kaydet</span><span class="sxs-lookup"><span data-stu-id="681a1-476">Register the database context</span></span>

<span data-ttu-id="681a1-477">ASP.NET Core DB bağlamı gibi hizmetler ile kaydedilmelidir [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcı.</span><span class="sxs-lookup"><span data-stu-id="681a1-477">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="681a1-478">Kapsayıcı hizmeti denetleyicilerine sağlar.</span><span class="sxs-lookup"><span data-stu-id="681a1-478">The container provides the service to controllers.</span></span>

<span data-ttu-id="681a1-479">Güncelleştirme *Startup.cs* aşağıdaki vurgulanmış kodu:</span><span class="sxs-lookup"><span data-stu-id="681a1-479">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="681a1-480">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="681a1-480">The preceding code:</span></span>

* <span data-ttu-id="681a1-481">Kullanılmayan kaldırır `using` bildirimleri.</span><span class="sxs-lookup"><span data-stu-id="681a1-481">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="681a1-482">Veritabanı bağlamı DI kapsayıcıya ekler.</span><span class="sxs-lookup"><span data-stu-id="681a1-482">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="681a1-483">Veritabanı bağlamı bir bellek içi veritabanına kullanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="681a1-483">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="681a1-484">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="681a1-484">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="681a1-485">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-485">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="681a1-486">Sağ *denetleyicileri* klasör.</span><span class="sxs-lookup"><span data-stu-id="681a1-486">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="681a1-487">> **Yeni öğe** **Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-487">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="681a1-488">İçinde **Yeni Öğe Ekle** iletişim kutusunda **API denetleyici sınıfı** şablonu.</span><span class="sxs-lookup"><span data-stu-id="681a1-488">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="681a1-489">Sınıf adı *TodoController*seçip **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="681a1-489">Name the class *TodoController*, and select **Add**.</span></span>

  ![Yeni öğe iletişim denetleyicisiyle seçilen arama kutusu ve web API denetleyicisi Ekle](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="681a1-491">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-491">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="681a1-492">İçinde *denetleyicileri* klasör adında bir sınıf oluşturma `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="681a1-492">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="681a1-493">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="681a1-493">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="681a1-494">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="681a1-494">The preceding code:</span></span>

* <span data-ttu-id="681a1-495">Bir API denetleyicisi sınıfı yöntemleri olmadan tanımlar.</span><span class="sxs-lookup"><span data-stu-id="681a1-495">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="681a1-496">Sınıfı [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="681a1-496">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="681a1-497">Bu öznitelik, denetleyicinin web API'si isteklerine yanıt verdiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="681a1-497">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="681a1-498">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz. <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="681a1-498">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="681a1-499">Veritabanı bağlamı eklemesine DI kullanır (`TodoContext`) içine denetleyici.</span><span class="sxs-lookup"><span data-stu-id="681a1-499">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="681a1-500">Her bir veritabanı bağlamı kullanılan [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) denetleyici yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="681a1-500">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="681a1-501">Adlı bir öğe ekler `Item1` veritabanı boşsa veritabanı.</span><span class="sxs-lookup"><span data-stu-id="681a1-501">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="681a1-502">Her çalıştığında bu kod oluşturucusunun içinde yeni bir HTTP isteği olduğundan.</span><span class="sxs-lookup"><span data-stu-id="681a1-502">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="681a1-503">Tüm öğeleri silerseniz, oluşturucu oluşturur `Item1` API yöntemi çağrıldığında tekrar başlattığınızda.</span><span class="sxs-lookup"><span data-stu-id="681a1-503">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="681a1-504">Bu nedenle, gerçekten işe yaradı silme işlemi işe yaramadı gibi görünebilir.</span><span class="sxs-lookup"><span data-stu-id="681a1-504">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="681a1-505">Get yöntemleri ekleyin</span><span class="sxs-lookup"><span data-stu-id="681a1-505">Add Get methods</span></span>

<span data-ttu-id="681a1-506">Yapılacak iş öğeleri alır bir API sağlamak için aşağıdaki yöntemi ekleyin. `TodoController` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="681a1-506">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="681a1-507">İki GET uç noktası bu yöntemleri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="681a1-507">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="681a1-508">Hala çalışıyorsa uygulamayı durdurun.</span><span class="sxs-lookup"><span data-stu-id="681a1-508">Stop the app if it's still running.</span></span> <span data-ttu-id="681a1-509">Ardından, en son değişiklikleri dahil etmek için yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="681a1-509">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="681a1-510">Bir tarayıcıdan iki uç nokta çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="681a1-510">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="681a1-511">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="681a1-511">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="681a1-512">Şu HTTP yanıtı çağrısı tarafından üretilen `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="681a1-512">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="681a1-513">URL Yönlendirme ve yolları</span><span class="sxs-lookup"><span data-stu-id="681a1-513">Routing and URL paths</span></span>

<span data-ttu-id="681a1-514">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Özniteliği bir HTTP GET isteğine yanıt vermeden bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="681a1-514">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="681a1-515">Her yöntem için URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="681a1-515">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="681a1-516">Denetleyicinin şablonu dizesi ile başlayıp `Route` özniteliği:</span><span class="sxs-lookup"><span data-stu-id="681a1-516">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="681a1-517">Değiştirin `[controller]` denetleyicinin adı ile kural tarafından olduğu "Controller" soneki eksi denetleyici sınıfı adı.</span><span class="sxs-lookup"><span data-stu-id="681a1-517">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="681a1-518">Bu örnek, denetleyici sınıfı adı olan **Todo**Denetleyici adı "todo" Bu nedenle denetleyicisi.</span><span class="sxs-lookup"><span data-stu-id="681a1-518">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="681a1-519">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="681a1-519">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="681a1-520">`[HttpGet]` özniteliğinin bir yol şablonu varsa (örneğin, `[HttpGet("products")]`), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="681a1-520">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="681a1-521">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="681a1-521">This sample doesn't use a template.</span></span> <span data-ttu-id="681a1-522">Daha fazla bilgi için [özniteliği Http [eylem] özniteliği ile yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="681a1-522">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="681a1-523">Aşağıdaki `GetTodoItem` yöntemi `"{id}"` yapılacak iş öğesi benzersiz tanımlayıcısı için bir yer tutucu değişkendir.</span><span class="sxs-lookup"><span data-stu-id="681a1-523">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="681a1-524">Zaman `GetTodoItem` çağrılır, değerini `"{id}"` yöntemine URL'de sağlanan kendi`id` parametresi.</span><span class="sxs-lookup"><span data-stu-id="681a1-524">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="681a1-525">Döndürülen değerler</span><span class="sxs-lookup"><span data-stu-id="681a1-525">Return values</span></span>

<span data-ttu-id="681a1-526">Dönüş türünü `GetTodoItems` ve `GetTodoItem` yöntemler [actionresult öğesini\<T > türü](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="681a1-526">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="681a1-527">ASP.NET Core, nesneyi otomatik olarak serileştiren [JSON](https://www.json.org/) ve yanıt iletisinin gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="681a1-527">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="681a1-528">Yanıt kodu 200 bu dönüş türü için olduğu varsayılırsa işlenmeyen özel durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="681a1-528">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="681a1-529">İşlenmeyen özel durumları 5xx hatalarla karşılaşırsanız çevrilir.</span><span class="sxs-lookup"><span data-stu-id="681a1-529">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="681a1-530">`ActionResult` dönüş türleri, geniş HTTP durum kodları temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="681a1-530">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="681a1-531">Örneğin, `GetTodoItem` iki farklı durum değerleri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="681a1-531">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="681a1-532">Öğe istenen kimliği eşleşirse, yöntem bir 404 döndürür [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) hata kodu.</span><span class="sxs-lookup"><span data-stu-id="681a1-532">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="681a1-533">Aksi takdirde yöntem bir JSON yanıt gövdesine 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="681a1-533">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="681a1-534">Döndüren `item` sonuçları bir HTTP 200 yanıtı.</span><span class="sxs-lookup"><span data-stu-id="681a1-534">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="681a1-535">Test GetTodoItems yöntemi</span><span class="sxs-lookup"><span data-stu-id="681a1-535">Test the GetTodoItems method</span></span>

<span data-ttu-id="681a1-536">Bu öğreticide Postman web API'si test etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="681a1-536">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="681a1-537">[Postman](https://www.getpostman.com/downloads/)'yi yükleme.</span><span class="sxs-lookup"><span data-stu-id="681a1-537">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="681a1-538">Web uygulaması başlatın.</span><span class="sxs-lookup"><span data-stu-id="681a1-538">Start the web app.</span></span>
* <span data-ttu-id="681a1-539">Postman'i başlatın.</span><span class="sxs-lookup"><span data-stu-id="681a1-539">Start Postman.</span></span>
* <span data-ttu-id="681a1-540">**SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="681a1-540">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="681a1-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-541">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="681a1-542">**Dosya** > **ayarları** ' ndan (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="681a1-542">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="681a1-543">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="681a1-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="681a1-544">**Postman** > **tercihleri** ' nden (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="681a1-544">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="681a1-545">Alternatif olarak, wranı seçin ve **Ayarlar**' ı seçip SSL sertifikası doğrulamasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="681a1-545">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="681a1-546">Test denetleyicisi sonra SSL sertifika doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="681a1-546">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="681a1-547">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="681a1-547">Create a new request.</span></span>
  * <span data-ttu-id="681a1-548">HTTP yöntemi kümesine **alma**.</span><span class="sxs-lookup"><span data-stu-id="681a1-548">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="681a1-549">İstek URL'si kümesine `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="681a1-549">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="681a1-550">Örneğin: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="681a1-550">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="681a1-551">Ayarlama **iki bölme görünümü** postman'deki.</span><span class="sxs-lookup"><span data-stu-id="681a1-551">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="681a1-552">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-552">Select **Send**.</span></span>

![Get isteğiyle postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="681a1-554">Create yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="681a1-554">Add a Create method</span></span>

<span data-ttu-id="681a1-555">Aşağıdaki `PostTodoItem` yöntemini *Controllers/TodoController. cs*içine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="681a1-555">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="681a1-556">Yukarıdaki kod, [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) özniteliğiyle gösterildiği gıbı bır http post yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="681a1-556">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="681a1-557">Yöntemi, HTTP isteği gövdesinden Yapılacaklar öğenin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="681a1-557">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="681a1-558">`CreatedAtAction` Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="681a1-558">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="681a1-559">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="681a1-559">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="681a1-560">HTTP 201 sunucuda yeni bir kaynak oluşturan bir HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="681a1-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="681a1-561">Yanıta bir `Location` üst bilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="681a1-561">Adds a `Location` header to the response.</span></span> <span data-ttu-id="681a1-562">`Location` üstbilgisi, yeni oluşturulan Yapılacaklar öğesinin URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="681a1-562">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="681a1-563">Daha fazla bilgi için [10.2.2 201 oluşturuldu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="681a1-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="681a1-564">`Location` üst bilgisinin URI 'sini oluşturmak için `GetTodoItem` eyleme başvurur.</span><span class="sxs-lookup"><span data-stu-id="681a1-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="681a1-565">C# `nameof` anahtar sözcüğü, `CreatedAtAction` çağrısında eylem adının sabit kodlanmasını önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="681a1-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="681a1-566">Test PostTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="681a1-566">Test the PostTodoItem method</span></span>

* <span data-ttu-id="681a1-567">Projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="681a1-567">Build the project.</span></span>
* <span data-ttu-id="681a1-568">Postman HTTP yöntemi kümesine `POST`.</span><span class="sxs-lookup"><span data-stu-id="681a1-568">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="681a1-569">Seçin **gövdesi** sekmesi.</span><span class="sxs-lookup"><span data-stu-id="681a1-569">Select the **Body** tab.</span></span>
* <span data-ttu-id="681a1-570">Seçin **ham** radyo düğmesi.</span><span class="sxs-lookup"><span data-stu-id="681a1-570">Select the **raw** radio button.</span></span>
* <span data-ttu-id="681a1-571">Tür kümesine **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="681a1-571">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="681a1-572">İstek gövdesinde bir yapılacak iş öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="681a1-572">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="681a1-573">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-573">Select **Send**.</span></span>

  ![Postman ile isteği oluştur](first-web-api/_static/create.png)

  <span data-ttu-id="681a1-575">Bir 405 yöntemine Izin verilmiyor hatası alırsanız, `PostTodoItem` yöntemi eklendikten sonra projenin derlenmesinin sonucu büyük olasılıkla oluşur.</span><span class="sxs-lookup"><span data-stu-id="681a1-575">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="681a1-576">Konum üst bilgisi URI test</span><span class="sxs-lookup"><span data-stu-id="681a1-576">Test the location header URI</span></span>

* <span data-ttu-id="681a1-577">Seçin **üstbilgileri** sekmesinde **yanıt** bölmesi.</span><span class="sxs-lookup"><span data-stu-id="681a1-577">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="681a1-578">Kopyalama **konumu** üst bilgi değeri:</span><span class="sxs-lookup"><span data-stu-id="681a1-578">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üst bilgiler sekmesi](first-web-api/_static/pmc2.png)

* <span data-ttu-id="681a1-580">Yöntemini GET öğesine Ayarla.</span><span class="sxs-lookup"><span data-stu-id="681a1-580">Set the method to GET.</span></span>
* <span data-ttu-id="681a1-581">URI 'yi yapıştırın (örneğin, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="681a1-581">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="681a1-582">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-582">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="681a1-583">PutTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="681a1-583">Add a PutTodoItem method</span></span>

<span data-ttu-id="681a1-584">Aşağıdaki `PutTodoItem` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="681a1-584">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="681a1-585">`PutTodoItem` benzer `PostTodoItem`, HTTP PUT kullanır.</span><span class="sxs-lookup"><span data-stu-id="681a1-585">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="681a1-586">Yanıt [204 (içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="681a1-586">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="681a1-587">HTTP belirtimine göre bir PUT İsteği tüm güncelleştirilmiş varlık yalnızca değişiklikler değil göndermek istemci gerektirir.</span><span class="sxs-lookup"><span data-stu-id="681a1-587">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="681a1-588">Kısmi güncelleştirmeleri desteklemek için kullanma [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="681a1-588">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="681a1-589">`PutTodoItem`çağırırken bir hata alırsanız, veritabanında bir öğe olduğundan emin olmak için `GET` çağırın.</span><span class="sxs-lookup"><span data-stu-id="681a1-589">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="681a1-590">Test PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="681a1-590">Test the PutTodoItem method</span></span>

<span data-ttu-id="681a1-591">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="681a1-591">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="681a1-592">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="681a1-592">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="681a1-593">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="681a1-593">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="681a1-594">Kimliğine sahip bir yapılacak iş öğesi güncelleştirme = 1 ve "balık akış için" adını girin:</span><span class="sxs-lookup"><span data-stu-id="681a1-594">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="681a1-595">Aşağıdaki görüntüde, Postman güncelleştirme gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="681a1-595">The following image shows the Postman update:</span></span>

![204 (içerik yok) yanıtı gösteren postman konsol](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="681a1-597">DeleteTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="681a1-597">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="681a1-598">Aşağıdaki `DeleteTodoItem` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="681a1-598">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="681a1-599">`DeleteTodoItem` Yanıt [204 (içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="681a1-599">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="681a1-600">Test DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="681a1-600">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="681a1-601">Postman bir yapılacak iş öğesini silmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="681a1-601">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="681a1-602">Yöntem kümesine `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="681a1-602">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="681a1-603">Silinecek nesnenin URI 'sini ayarlayın (örneğin `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="681a1-603">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="681a1-604">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="681a1-604">Select **Send**.</span></span>

<span data-ttu-id="681a1-605">Örnek uygulama, tüm öğeleri silmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="681a1-605">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="681a1-606">Ancak, son öğe silindiğinde, API 'nin bir sonraki çağrılışında model sınıfı Oluşturucu tarafından yeni bir tane oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="681a1-606">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="681a1-607">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="681a1-607">Call the web API with JavaScript</span></span>

<span data-ttu-id="681a1-608">Bu bölümde, Web API 'sini çağırmak için JavaScript kullanan bir HTML sayfası eklenir.</span><span class="sxs-lookup"><span data-stu-id="681a1-608">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="681a1-609">jQuery isteği başlatır.</span><span class="sxs-lookup"><span data-stu-id="681a1-609">jQuery initiates the request.</span></span> <span data-ttu-id="681a1-610">JavaScript, sayfayı Web API 'sinin yanıtından alınan ayrıntılarla güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="681a1-610">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="681a1-611">Uygulamayı [statik dosyalara sunacak](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) şekilde yapılandırın ve aşağıdaki vurgulanmış kodla *Startup.cs* güncelleştirerek [varsayılan dosya eşlemesini etkinleştirin](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) :</span><span class="sxs-lookup"><span data-stu-id="681a1-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="681a1-612">Oluşturma bir *wwwroot* proje dizininde klasör.</span><span class="sxs-lookup"><span data-stu-id="681a1-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="681a1-613">Adlı bir HTML dosyası ekleyin *index.html* için *wwwroot* dizin.</span><span class="sxs-lookup"><span data-stu-id="681a1-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="681a1-614">Dosyanın içeriğini aşağıdaki biçimlendirme ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="681a1-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="681a1-615">Adlı bir JavaScript dosyası ekleyin *site.js* için *wwwroot* dizin.</span><span class="sxs-lookup"><span data-stu-id="681a1-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="681a1-616">Dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="681a1-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="681a1-617">ASP.NET Core proje başlatma ayarlarında bir değişiklik HTML sayfasını yerel olarak test etmek için gerekli:</span><span class="sxs-lookup"><span data-stu-id="681a1-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="681a1-618">Açık *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="681a1-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="681a1-619">Kaldırma `launchUrl` , açmak için uygulamayı zorlamak için özellik *index.html*&mdash;projenin varsayılan dosya.</span><span class="sxs-lookup"><span data-stu-id="681a1-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="681a1-620">Bu örnek, Web API 'sinin tüm CRUD yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="681a1-620">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="681a1-621">API çağrıları açıklamaları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="681a1-621">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="681a1-622">Yapılacaklar öğelerinin bir listesini alın</span><span class="sxs-lookup"><span data-stu-id="681a1-622">Get a list of to-do items</span></span>

<span data-ttu-id="681a1-623">jQuery, bir to-do öğesi dizisini temsil eden JSON döndüren Web API 'sine bir HTTP GET isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="681a1-623">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="681a1-624">`success` İstek başarılı olursa geri çağırma işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="681a1-624">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="681a1-625">Geri çağırma içinde DOM Yapılacaklar bilgilerle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="681a1-625">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="681a1-626">Yapılacak İş Öğesi Ekle</span><span class="sxs-lookup"><span data-stu-id="681a1-626">Add a to-do item</span></span>

<span data-ttu-id="681a1-627">jQuery, istek gövdesinde Yapılacaklar öğesiyle bir HTTP POST isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="681a1-627">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="681a1-628">`accepts` Ve `contentType` seçeneklerini ayarlamak `application/json` gönderilen ve alınan medya türü belirtmek için.</span><span class="sxs-lookup"><span data-stu-id="681a1-628">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="681a1-629">Yapılacak iş öğesi kullanarak JSON'a dönüştürülür [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="681a1-629">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="681a1-630">API'yi bir başarılı durum kodu döndürdüğünde `getData` işlevi HTML tablosu güncelleştirmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="681a1-630">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="681a1-631">Yapılacak iş öğesini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="681a1-631">Update a to-do item</span></span>

<span data-ttu-id="681a1-632">Yapılacak iş öğesi güncelleştirilirken bir eklemeye benzerdir.</span><span class="sxs-lookup"><span data-stu-id="681a1-632">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="681a1-633">`url` Öğenin benzersiz tanıtıcısı eklemek için değişiklikleri ve `type` olduğu `PUT`.</span><span class="sxs-lookup"><span data-stu-id="681a1-633">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="681a1-634">Yapılacak iş öğesi silme</span><span class="sxs-lookup"><span data-stu-id="681a1-634">Delete a to-do item</span></span>

<span data-ttu-id="681a1-635">Yapılacak iş öğesi silme gerçekleştirilir ayarlayarak `type` AJAX çağrısı hedefi üzerinde `DELETE` ve öğenin benzersiz tanıtıcısı URL'yi belirterek.</span><span class="sxs-lookup"><span data-stu-id="681a1-635">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="681a1-636">Web API 'sine kimlik doğrulama desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="681a1-636">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="681a1-637">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="681a1-637">Additional resources</span></span>

<span data-ttu-id="681a1-638">[Görüntülemek veya Bu öğretici için örnek kodu indirdikten](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="681a1-638">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="681a1-639">Bkz: [nasıl indirileceğini](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="681a1-639">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="681a1-640">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="681a1-640">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="681a1-641">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="681a1-641">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
