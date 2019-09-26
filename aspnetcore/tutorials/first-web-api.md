---
title: "Öğretici: ASP.NET Core ile Web API 'SI oluşturma"
author: rick-anderson
description: ASP.NET Core ile Web API 'SI oluşturmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 5e5215f246c6c7a805a4c99f485d51a2fb3c712d
ms.sourcegitcommit: cf9ffcce4fe0b69fe795aae9ae06e99fdb18bdfc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71306671"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="00585-103">Öğretici: ASP.NET Core ile Web API 'SI oluşturma</span><span class="sxs-lookup"><span data-stu-id="00585-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="00585-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="00585-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="00585-105">Bu öğretici, bir web API ASP.NET Core ile oluşturmaya ilişkin temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="00585-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="00585-106">Bu öğreticide şunların nasıl yapıladığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="00585-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="00585-107">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00585-107">Create a web API project.</span></span>
> * <span data-ttu-id="00585-108">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="00585-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="00585-109">CRUD yöntemleriyle bir denetleyiciyi dolandırın.</span><span class="sxs-lookup"><span data-stu-id="00585-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="00585-110">Yönlendirmeyi, URL yollarını ve dönüş değerlerini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="00585-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="00585-111">Web API'si Postman ile çağırın.</span><span class="sxs-lookup"><span data-stu-id="00585-111">Call the web API with Postman.</span></span>

<span data-ttu-id="00585-112">Sonunda, bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="00585-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="00585-113">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="00585-113">Overview</span></span>

<span data-ttu-id="00585-114">Bu öğretici yandaki API oluşturur:</span><span class="sxs-lookup"><span data-stu-id="00585-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="00585-115">API</span><span class="sxs-lookup"><span data-stu-id="00585-115">API</span></span> | <span data-ttu-id="00585-116">Açıklama</span><span class="sxs-lookup"><span data-stu-id="00585-116">Description</span></span> | <span data-ttu-id="00585-117">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="00585-117">Request body</span></span> | <span data-ttu-id="00585-118">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="00585-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="00585-119">/Api/TodoItems al</span><span class="sxs-lookup"><span data-stu-id="00585-119">GET /api/TodoItems</span></span> | <span data-ttu-id="00585-120">Tüm yapılacak iş öğeleri al</span><span class="sxs-lookup"><span data-stu-id="00585-120">Get all to-do items</span></span> | <span data-ttu-id="00585-121">Yok.</span><span class="sxs-lookup"><span data-stu-id="00585-121">None</span></span> | <span data-ttu-id="00585-122">Yapılacaklar öğelerinin bir dizisi</span><span class="sxs-lookup"><span data-stu-id="00585-122">Array of to-do items</span></span>|
|<span data-ttu-id="00585-123">/Api/TodoItems/{id} al</span><span class="sxs-lookup"><span data-stu-id="00585-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="00585-124">Bir öğeyi Kimliğine göre Al</span><span class="sxs-lookup"><span data-stu-id="00585-124">Get an item by ID</span></span> | <span data-ttu-id="00585-125">Yok.</span><span class="sxs-lookup"><span data-stu-id="00585-125">None</span></span> | <span data-ttu-id="00585-126">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="00585-126">To-do item</span></span>|
|<span data-ttu-id="00585-127">POST/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="00585-127">POST /api/TodoItems</span></span> | <span data-ttu-id="00585-128">Yeni Öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="00585-128">Add a new item</span></span> | <span data-ttu-id="00585-129">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="00585-129">To-do item</span></span> | <span data-ttu-id="00585-130">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="00585-130">To-do item</span></span> |
|<span data-ttu-id="00585-131">/Api/TodoItems/{id} koy</span><span class="sxs-lookup"><span data-stu-id="00585-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="00585-132">Mevcut öğeyi güncelleştirin &nbsp;</span><span class="sxs-lookup"><span data-stu-id="00585-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="00585-133">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="00585-133">To-do item</span></span> | <span data-ttu-id="00585-134">Yok.</span><span class="sxs-lookup"><span data-stu-id="00585-134">None</span></span> |
|<span data-ttu-id="00585-135">/Api/TodoItems/{id} &nbsp; Sil&nbsp;</span><span class="sxs-lookup"><span data-stu-id="00585-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="00585-136">Öğeyi Sil &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="00585-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="00585-137">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="00585-137">None</span></span> | <span data-ttu-id="00585-138">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="00585-138">None</span></span>|

<span data-ttu-id="00585-139">Aşağıdaki diyagramda, bu uygulamanın tasarımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="00585-139">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="00585-145">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="00585-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="00585-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="00585-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00585-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="00585-148">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="00585-149">Bir web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="00585-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="00585-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="00585-151">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="00585-152">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00585-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="00585-153">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00585-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="00585-154">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,0** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00585-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="00585-155">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00585-155">Select the **API** template and click **Create**.</span></span>

![VS yeni proje iletişim kutusu](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="00585-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00585-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="00585-158">Açık [tümleşik Terminalini](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="00585-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="00585-159">Dizinleri (`cd`) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="00585-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="00585-160">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00585-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="00585-161">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="00585-162">Yukarıdaki komutlar:</span><span class="sxs-lookup"><span data-stu-id="00585-162">The preceding commands:</span></span>

  * <span data-ttu-id="00585-163">Yeni bir Web API projesi oluşturur ve Visual Studio Code açar.</span><span class="sxs-lookup"><span data-stu-id="00585-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="00585-164">Sonraki bölümde gerekli olan NuGet paketlerini ekler.</span><span class="sxs-lookup"><span data-stu-id="00585-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="00585-165">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="00585-166">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-166">Select **File** > **New Solution**.</span></span>

  ![Yeni çözüm macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="00585-168">**.NET Core** > **uygulama** **API 'si** ileri ' **yi**seçin. > ></span><span class="sxs-lookup"><span data-stu-id="00585-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="00585-170">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda, **hedef Framework** \* *.NET Core 3,0*' i seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="00585-171">Girin *TodoApi* için **proje adı** seçip **Oluştur**.</span><span class="sxs-lookup"><span data-stu-id="00585-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="00585-173">Proje klasöründe bir komut terminali açın ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00585-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="00585-174">API'yi test etme</span><span class="sxs-lookup"><span data-stu-id="00585-174">Test the API</span></span>

<span data-ttu-id="00585-175">Proje şablonu oluşturur bir `WeatherForecast` API.</span><span class="sxs-lookup"><span data-stu-id="00585-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="00585-176">Çağrı `Get` uygulamayı test etmek için bir tarayıcıdan yöntemi.</span><span class="sxs-lookup"><span data-stu-id="00585-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="00585-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="00585-178">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="00585-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="00585-179">Visual Studio bir tarayıcı ile başlatarak `https://localhost:<port>/WeatherForecast`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="00585-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="00585-180">IIS Express sertifika güven varsa soran bir iletişim kutusu alırsanız seçin **Evet**.</span><span class="sxs-lookup"><span data-stu-id="00585-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="00585-181">İçinde **Güvenlik Uyarısı** ardından, görüntülenen iletişim seçin **Evet**.</span><span class="sxs-lookup"><span data-stu-id="00585-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="00585-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00585-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="00585-183">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="00585-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="00585-184">Bir tarayıcıda aşağıdaki URL'ye gidin: [ https://localhost:5001/WeatherForecast ](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="00585-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="00585-185">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="00585-186">Uygulamayı başlatmak için**hata ayıklamayı Başlat** ' **ı seçin.**  > </span><span class="sxs-lookup"><span data-stu-id="00585-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="00585-187">Mac için Visual Studio bir tarayıcı ile başlatarak `https://localhost:<port>`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="00585-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="00585-188">HTTP 404 (bulunamadı) hatası döndürülür.</span><span class="sxs-lookup"><span data-stu-id="00585-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="00585-189">Append `/WeatherForecast` URL'sine (URL'yi `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="00585-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="00585-190">Aşağıdakine benzer bir JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="00585-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="00585-191">Bir model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="00585-191">Add a model class</span></span>

<span data-ttu-id="00585-192">A *modeli* uygulamayı yöneten verilerini temsil eden sınıflar kümesidir.</span><span class="sxs-lookup"><span data-stu-id="00585-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="00585-193">Tek bir modeldir bu uygulama için `TodoItem` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="00585-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="00585-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="00585-195">İçinde **Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00585-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="00585-196">Seçin **ekleme** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="00585-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="00585-197">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="00585-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="00585-198">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="00585-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="00585-199">Sınıf adı *Todoıtem* seçip **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="00585-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="00585-200">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="00585-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="00585-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00585-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="00585-202">Adlı bir klasör ekleme *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="00585-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="00585-203">Ekleme bir `TodoItem` sınıfının *modelleri* aşağıdaki kodla klasörü:</span><span class="sxs-lookup"><span data-stu-id="00585-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="00585-204">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="00585-205">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00585-205">Right-click the project.</span></span> <span data-ttu-id="00585-206">Seçin **ekleme** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="00585-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="00585-207">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="00585-207">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="00585-209">*Modeller* klasörüne sağ tıklayın ve **yeni dosya** > **Ekle** > **genel** > **boş sınıfı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="00585-210">Sınıf adı *Todoıtem*ve ardından **yeni**.</span><span class="sxs-lookup"><span data-stu-id="00585-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="00585-211">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="00585-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="00585-212">`Id` Özelliği işlevlerinin bir ilişkisel veritabanında benzersiz anahtar.</span><span class="sxs-lookup"><span data-stu-id="00585-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="00585-213">Model sınıfları herhangi bir projede gidip ancak *modelleri* klasörü, kural olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="00585-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="00585-214">Veritabanı bağlamı Ekle</span><span class="sxs-lookup"><span data-stu-id="00585-214">Add a database context</span></span>

<span data-ttu-id="00585-215">*Veritabanı bağlamı* koordine eden bir veri modeli için Entity Framework işlevsellik ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="00585-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="00585-216">Bu sınıf türetme tarafından oluşturulan `Microsoft.EntityFrameworkCore.DbContext` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="00585-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="00585-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="00585-218">Microsoft. EntityFrameworkCore. SqlServer ekleyin</span><span class="sxs-lookup"><span data-stu-id="00585-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="00585-219">**Araçlar** menüsünde **nuget Paket Yöneticisi > çözüm Için NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="00585-220">**Araştır** sekmesini seçin ve arama kutusuna **Microsoft. Entityframeworkcore. SqlServer** yazın.</span><span class="sxs-lookup"><span data-stu-id="00585-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="00585-221">Sol bölmedeki **Microsoft. EntityFrameworkCore. SqlServer** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-221">Select  **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="00585-222">Sağ bölmedeki **Proje** onay kutusunu seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="00585-223">Önceki yönergeleri kullanarak `Microsoft.EntityFrameworkCore.InMemory` NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="00585-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet Paket Yöneticisi](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="00585-225">TodoContext veritabanı bağlamını ekleme</span><span class="sxs-lookup"><span data-stu-id="00585-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="00585-226">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="00585-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="00585-227">Sınıf adı *TodoContext* tıklatıp **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="00585-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="00585-228">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="00585-229">Ekleme bir `TodoContext` sınıfının *modelleri* klasör.</span><span class="sxs-lookup"><span data-stu-id="00585-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="00585-230">Aşağıdaki kodu girin:</span><span class="sxs-lookup"><span data-stu-id="00585-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="00585-231">Veritabanı bağlamı Kaydet</span><span class="sxs-lookup"><span data-stu-id="00585-231">Register the database context</span></span>

<span data-ttu-id="00585-232">ASP.NET Core DB bağlamı gibi hizmetler ile kaydedilmelidir [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcı.</span><span class="sxs-lookup"><span data-stu-id="00585-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="00585-233">Kapsayıcı hizmeti denetleyicilerine sağlar.</span><span class="sxs-lookup"><span data-stu-id="00585-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="00585-234">Güncelleştirme *Startup.cs* aşağıdaki vurgulanmış kodu:</span><span class="sxs-lookup"><span data-stu-id="00585-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="00585-235">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="00585-235">The preceding code:</span></span>

* <span data-ttu-id="00585-236">Kullanılmayan kaldırır `using` bildirimleri.</span><span class="sxs-lookup"><span data-stu-id="00585-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="00585-237">Veritabanı bağlamı DI kapsayıcıya ekler.</span><span class="sxs-lookup"><span data-stu-id="00585-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="00585-238">Veritabanı bağlamı bir bellek içi veritabanına kullanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="00585-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="00585-239">Denetleyiciyi bir denetleyiciye katlama</span><span class="sxs-lookup"><span data-stu-id="00585-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="00585-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="00585-241">Sağ *denetleyicileri* klasör.</span><span class="sxs-lookup"><span data-stu-id="00585-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="00585-242">**Yeni yapı iskelesi öğesi** **Ekle** > öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="00585-243">**Entity Framework kullanarak ve eylemler Içeren API denetleyicisi**' ni seçin ve ardından **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="00585-244">**API denetleyiciyi eylemler Ile Ekle ' de Entity Framework** iletişim kutusunu kullanarak:</span><span class="sxs-lookup"><span data-stu-id="00585-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="00585-245">**Model sınıfında** **TodoItem (TodoAPI. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-245">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="00585-246">**Veri bağlamı sınıfında** **TodoContext (TodoAPI. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-246">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="00585-247">**Ekle** 'yi seçin</span><span class="sxs-lookup"><span data-stu-id="00585-247">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="00585-248">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="00585-249">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00585-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="00585-250">Yukarıdaki komutlar:</span><span class="sxs-lookup"><span data-stu-id="00585-250">The preceding commands:</span></span>

* <span data-ttu-id="00585-251">Yapı iskelesi için gereken NuGet paketlerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="00585-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="00585-252">Scafkatlama altyapısını (`dotnet-aspnet-codegenerator`) kurar.</span><span class="sxs-lookup"><span data-stu-id="00585-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="00585-253">Yapı iskelesi `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="00585-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="00585-254">Oluşturulan kod:</span><span class="sxs-lookup"><span data-stu-id="00585-254">The generated code:</span></span>

* <span data-ttu-id="00585-255">Bir API denetleyicisi sınıfı yöntemleri olmadan tanımlar.</span><span class="sxs-lookup"><span data-stu-id="00585-255">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="00585-256">Sınıfı [[Apicontroller]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) özniteliğiyle süsler.</span><span class="sxs-lookup"><span data-stu-id="00585-256">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="00585-257">Bu öznitelik, denetleyicinin web API'si isteklerine yanıt verdiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="00585-257">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="00585-258">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="00585-258">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="00585-259">Veritabanı bağlamı eklemesine DI kullanır (`TodoContext`) içine denetleyici.</span><span class="sxs-lookup"><span data-stu-id="00585-259">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="00585-260">Her bir veritabanı bağlamı kullanılan [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) denetleyici yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="00585-260">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="00585-261">PostTodoItem Create metodunu inceleyin</span><span class="sxs-lookup"><span data-stu-id="00585-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="00585-262">İçindeki `PostTodoItem` return ifadesini, [NameOf](/dotnet/csharp/language-reference/operators/nameof) işlecini kullanmak için değiştirin:</span><span class="sxs-lookup"><span data-stu-id="00585-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="00585-263">Yukarıdaki kod tarafından belirtildiği gibi bir HTTP POST yöntemi olup [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) özniteliği.</span><span class="sxs-lookup"><span data-stu-id="00585-263">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="00585-264">Yöntemi, HTTP isteği gövdesinden Yapılacaklar öğenin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="00585-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="00585-265"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="00585-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="00585-266">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="00585-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="00585-267">HTTP 201 sunucuda yeni bir kaynak oluşturan bir HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="00585-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="00585-268">Yanıta bir [konum](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) üst bilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="00585-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="00585-269">Üst bilgi, yeni oluşturulan Yapılacaklar öğesinin URI 'sini belirtir. [](https://developer.mozilla.org/docs/Glossary/URI) `Location`</span><span class="sxs-lookup"><span data-stu-id="00585-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="00585-270">Daha fazla bilgi için [10.2.2 201 oluşturuldu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="00585-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="00585-271">`Location` Üstbilginin URI 'sini oluşturma eylemine başvurur. `GetTodoItem`</span><span class="sxs-lookup"><span data-stu-id="00585-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="00585-272">Anahtar sözcüğü, `CreatedAtAction` çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır. C# `nameof`</span><span class="sxs-lookup"><span data-stu-id="00585-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="00585-273">Postman yükleme</span><span class="sxs-lookup"><span data-stu-id="00585-273">Install Postman</span></span>

<span data-ttu-id="00585-274">Bu öğreticide Postman web API'si test etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="00585-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="00585-275">Yükleme [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="00585-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="00585-276">Web uygulaması başlatın.</span><span class="sxs-lookup"><span data-stu-id="00585-276">Start the web app.</span></span>
* <span data-ttu-id="00585-277">Postman'i başlatın.</span><span class="sxs-lookup"><span data-stu-id="00585-277">Start Postman.</span></span>
* <span data-ttu-id="00585-278">Devre dışı **SSL sertifika doğrulama**</span><span class="sxs-lookup"><span data-stu-id="00585-278">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="00585-279">Gelen **Dosya > Ayarlar** (\**genel* sekmesinde), devre dışı **SSL sertifika doğrulama**.</span><span class="sxs-lookup"><span data-stu-id="00585-279">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="00585-280">Test denetleyicisi sonra SSL sertifika doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="00585-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="00585-281">Postman ile test PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="00585-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="00585-282">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00585-282">Create a new request.</span></span>
* <span data-ttu-id="00585-283">HTTP yöntemini olarak `POST`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00585-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="00585-284">Seçin **gövdesi** sekmesi.</span><span class="sxs-lookup"><span data-stu-id="00585-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="00585-285">Seçin **ham** radyo düğmesi.</span><span class="sxs-lookup"><span data-stu-id="00585-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="00585-286">Tür kümesine **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="00585-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="00585-287">İstek gövdesinde bir yapılacak iş öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="00585-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="00585-288">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-288">Select **Send**.</span></span>

  ![Postman ile isteği oluştur](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="00585-290">Konum üst bilgisi URI test</span><span class="sxs-lookup"><span data-stu-id="00585-290">Test the location header URI</span></span>

* <span data-ttu-id="00585-291">Seçin **üstbilgileri** sekmesinde **yanıt** bölmesi.</span><span class="sxs-lookup"><span data-stu-id="00585-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="00585-292">Kopyalama **konumu** üst bilgi değeri:</span><span class="sxs-lookup"><span data-stu-id="00585-292">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üst bilgiler sekmesi](first-web-api/_static/3/create.png)

* <span data-ttu-id="00585-294">Yöntemini GET öğesine Ayarla.</span><span class="sxs-lookup"><span data-stu-id="00585-294">Set the method to GET.</span></span>
* <span data-ttu-id="00585-295">URİ'sini yapıştırın (örneğin, `https://localhost:5001/api/TodoItems/1`)</span><span class="sxs-lookup"><span data-stu-id="00585-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="00585-296">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="00585-297">GET yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="00585-297">Examine the GET methods</span></span>

<span data-ttu-id="00585-298">İki GET uç noktası bu yöntemleri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="00585-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="00585-299">Tarayıcıdan veya Postman 'dan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="00585-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="00585-300">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="00585-300">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="00585-301">Şuna benzer bir yanıt, şu çağrı `GetTodoItems`tarafından üretilir:</span><span class="sxs-lookup"><span data-stu-id="00585-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="00585-302">Postman ile test al</span><span class="sxs-lookup"><span data-stu-id="00585-302">Test Get with Postman</span></span>

* <span data-ttu-id="00585-303">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00585-303">Create a new request.</span></span>
* <span data-ttu-id="00585-304">HTTP yöntemi kümesine **alma**.</span><span class="sxs-lookup"><span data-stu-id="00585-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="00585-305">İstek URL'si kümesine `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="00585-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="00585-306">Örneğin: `https://localhost:5001/api/TodoItems`</span><span class="sxs-lookup"><span data-stu-id="00585-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="00585-307">Ayarlama **iki bölme görünümü** postman'deki.</span><span class="sxs-lookup"><span data-stu-id="00585-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="00585-308">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-308">Select **Send**.</span></span>

<span data-ttu-id="00585-309">Bu uygulama, bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="00585-309">This app uses an in-memory database.</span></span> <span data-ttu-id="00585-310">Uygulama durdurulup başlatılırsa, önceki GET isteği herhangi bir veri döndürmez.</span><span class="sxs-lookup"><span data-stu-id="00585-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="00585-311">Hiçbir veri döndürülmezse, verileri uygulamaya [gönderin](#post) .</span><span class="sxs-lookup"><span data-stu-id="00585-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="00585-312">URL Yönlendirme ve yolları</span><span class="sxs-lookup"><span data-stu-id="00585-312">Routing and URL paths</span></span>

<span data-ttu-id="00585-313">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Özniteliği bir HTTP GET isteğine yanıt vermeden bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="00585-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="00585-314">Her yöntem için URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="00585-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="00585-315">Denetleyicinin şablonu dizesi ile başlayıp `Route` özniteliği:</span><span class="sxs-lookup"><span data-stu-id="00585-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="00585-316">Değiştirin `[controller]` denetleyicinin adı ile kural tarafından olduğu "Controller" soneki eksi denetleyici sınıfı adı.</span><span class="sxs-lookup"><span data-stu-id="00585-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="00585-317">Bu örnek için denetleyici sınıfı adı **todoıtems**denetleyicisidir, bu nedenle denetleyicinin adı "todoıtems" olur.</span><span class="sxs-lookup"><span data-stu-id="00585-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="00585-318">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="00585-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="00585-319">Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]`), yola ekleyin. `[HttpGet]`</span><span class="sxs-lookup"><span data-stu-id="00585-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="00585-320">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="00585-320">This sample doesn't use a template.</span></span> <span data-ttu-id="00585-321">Daha fazla bilgi için [özniteliği Http [eylem] özniteliği ile yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="00585-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="00585-322">Aşağıdaki `GetTodoItem` yöntemi `"{id}"` yapılacak iş öğesi benzersiz tanımlayıcısı için bir yer tutucu değişkendir.</span><span class="sxs-lookup"><span data-stu-id="00585-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="00585-323">Zaman `GetTodoItem` çağrılır, değerini `"{id}"` yöntemine URL'de sağlanan kendi`id` parametresi.</span><span class="sxs-lookup"><span data-stu-id="00585-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="00585-324">Döndürülen değerler</span><span class="sxs-lookup"><span data-stu-id="00585-324">Return values</span></span>

<span data-ttu-id="00585-325">Dönüş türünü `GetTodoItems` ve `GetTodoItem` yöntemler [actionresult öğesini\<T > türü](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="00585-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="00585-326">ASP.NET Core, nesneyi otomatik olarak serileştiren [JSON](https://www.json.org/) ve yanıt iletisinin gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="00585-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="00585-327">Yanıt kodu 200 bu dönüş türü için olduğu varsayılırsa işlenmeyen özel durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="00585-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="00585-328">İşlenmeyen özel durumları 5xx hatalarla karşılaşırsanız çevrilir.</span><span class="sxs-lookup"><span data-stu-id="00585-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="00585-329">`ActionResult` dönüş türleri, geniş HTTP durum kodları temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="00585-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="00585-330">Örneğin, `GetTodoItem` iki farklı durum değerleri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="00585-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="00585-331">Öğe istenen kimliği eşleşirse, yöntem bir 404 döndürür [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) hata kodu.</span><span class="sxs-lookup"><span data-stu-id="00585-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="00585-332">Aksi takdirde yöntem bir JSON yanıt gövdesine 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="00585-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="00585-333">Döndüren `item` sonuçları bir HTTP 200 yanıtı.</span><span class="sxs-lookup"><span data-stu-id="00585-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="00585-334">PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="00585-334">The PutTodoItem method</span></span>

<span data-ttu-id="00585-335">`PutTodoItem` Yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="00585-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="00585-336">`PutTodoItem` benzer `PostTodoItem`, HTTP PUT kullanır.</span><span class="sxs-lookup"><span data-stu-id="00585-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="00585-337">Yanıt [204 (içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="00585-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="00585-338">HTTP belirtimine göre bir PUT İsteği tüm güncelleştirilmiş varlık yalnızca değişiklikler değil göndermek istemci gerektirir.</span><span class="sxs-lookup"><span data-stu-id="00585-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="00585-339">Kısmi güncelleştirmeleri desteklemek için kullanma [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="00585-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="00585-340">Çağrılırken `PutTodoItem`bir hata alırsanız, veritabanında bir öğe `GET` olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="00585-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="00585-341">Test PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="00585-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="00585-342">Bu örnek, uygulama her başlatıldığında yeniden başlatılması gereken bellek içi veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="00585-342">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="00585-343">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="00585-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="00585-344">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="00585-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="00585-345">ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="00585-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="00585-346">Aşağıdaki görüntüde, Postman güncelleştirme gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="00585-346">The following image shows the Postman update:</span></span>

![204 (içerik yok) yanıtı gösteren postman konsol](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="00585-348">DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="00585-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="00585-349">`DeleteTodoItem` Yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="00585-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="00585-350">`DeleteTodoItem` Yanıt [204 (içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="00585-350">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="00585-351">Test DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="00585-351">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="00585-352">Postman bir yapılacak iş öğesini silmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="00585-352">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="00585-353">Yöntem kümesine `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="00585-353">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="00585-354">Silmek, örneğin nesnenin URI ayarlayın `https://localhost:5001/api/TodoItems/1`</span><span class="sxs-lookup"><span data-stu-id="00585-354">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="00585-355">Seçin **Gönder**</span><span class="sxs-lookup"><span data-stu-id="00585-355">Select **Send**</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="00585-356">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="00585-356">Call the web API with JavaScript</span></span>

<span data-ttu-id="00585-357">Öğreticiye bakın [: JavaScript](xref:tutorials/web-api-javascript)ile ASP.NET Core Web API 'si çağırın.</span><span class="sxs-lookup"><span data-stu-id="00585-357">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="00585-358">Bu öğreticide şunların nasıl yapıladığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="00585-358">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="00585-359">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00585-359">Create a web API project.</span></span>
> * <span data-ttu-id="00585-360">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="00585-360">Add a model class and a database context.</span></span>
> * <span data-ttu-id="00585-361">Bir denetleyici ekleyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="00585-361">Add a controller.</span></span>
> * <span data-ttu-id="00585-362">CRUD yöntemleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="00585-362">Add CRUD methods.</span></span>
> * <span data-ttu-id="00585-363">Yönlendirmeyi Yapılandırma ve URL yolu.</span><span class="sxs-lookup"><span data-stu-id="00585-363">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="00585-364">Dönüş değerleri belirtin.</span><span class="sxs-lookup"><span data-stu-id="00585-364">Specify return values.</span></span>
> * <span data-ttu-id="00585-365">Web API'si Postman ile çağırın.</span><span class="sxs-lookup"><span data-stu-id="00585-365">Call the web API with Postman.</span></span>
> * <span data-ttu-id="00585-366">JavaScript ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="00585-366">Call the web API with JavaScript.</span></span>

<span data-ttu-id="00585-367">Sonunda, web API'si "Yapılacaklar" öğelerini ilişkisel bir veritabanında depolanan yönetebileceği sahip.</span><span class="sxs-lookup"><span data-stu-id="00585-367">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="00585-368">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="00585-368">Overview</span></span>

<span data-ttu-id="00585-369">Bu öğretici yandaki API oluşturur:</span><span class="sxs-lookup"><span data-stu-id="00585-369">This tutorial creates the following API:</span></span>

|<span data-ttu-id="00585-370">API</span><span class="sxs-lookup"><span data-stu-id="00585-370">API</span></span> | <span data-ttu-id="00585-371">Açıklama</span><span class="sxs-lookup"><span data-stu-id="00585-371">Description</span></span> | <span data-ttu-id="00585-372">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="00585-372">Request body</span></span> | <span data-ttu-id="00585-373">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="00585-373">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="00585-374">/Api/TodoItems al</span><span class="sxs-lookup"><span data-stu-id="00585-374">GET /api/TodoItems</span></span> | <span data-ttu-id="00585-375">Tüm yapılacak iş öğeleri al</span><span class="sxs-lookup"><span data-stu-id="00585-375">Get all to-do items</span></span> | <span data-ttu-id="00585-376">Yok.</span><span class="sxs-lookup"><span data-stu-id="00585-376">None</span></span> | <span data-ttu-id="00585-377">Yapılacaklar öğelerinin bir dizisi</span><span class="sxs-lookup"><span data-stu-id="00585-377">Array of to-do items</span></span>|
|<span data-ttu-id="00585-378">/Api/TodoItems/{id} al</span><span class="sxs-lookup"><span data-stu-id="00585-378">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="00585-379">Bir öğeyi Kimliğine göre Al</span><span class="sxs-lookup"><span data-stu-id="00585-379">Get an item by ID</span></span> | <span data-ttu-id="00585-380">Yok.</span><span class="sxs-lookup"><span data-stu-id="00585-380">None</span></span> | <span data-ttu-id="00585-381">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="00585-381">To-do item</span></span>|
|<span data-ttu-id="00585-382">POST/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="00585-382">POST /api/TodoItems</span></span> | <span data-ttu-id="00585-383">Yeni Öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="00585-383">Add a new item</span></span> | <span data-ttu-id="00585-384">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="00585-384">To-do item</span></span> | <span data-ttu-id="00585-385">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="00585-385">To-do item</span></span> |
|<span data-ttu-id="00585-386">/Api/TodoItems/{id} koy</span><span class="sxs-lookup"><span data-stu-id="00585-386">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="00585-387">Mevcut öğeyi güncelleştirin &nbsp;</span><span class="sxs-lookup"><span data-stu-id="00585-387">Update an existing item &nbsp;</span></span> | <span data-ttu-id="00585-388">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="00585-388">To-do item</span></span> | <span data-ttu-id="00585-389">Yok.</span><span class="sxs-lookup"><span data-stu-id="00585-389">None</span></span> |
|<span data-ttu-id="00585-390">/Api/TodoItems/{id} &nbsp; Sil&nbsp;</span><span class="sxs-lookup"><span data-stu-id="00585-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="00585-391">Öğeyi Sil &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="00585-391">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="00585-392">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="00585-392">None</span></span> | <span data-ttu-id="00585-393">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="00585-393">None</span></span>|

<span data-ttu-id="00585-394">Aşağıdaki diyagramda, bu uygulamanın tasarımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="00585-394">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="00585-400">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="00585-400">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="00585-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-401">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="00585-402">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00585-402">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="00585-403">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-403">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="00585-404">Bir web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="00585-404">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="00585-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-405">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="00585-406">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-406">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="00585-407">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00585-407">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="00585-408">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00585-408">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="00585-409">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 2,2** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00585-409">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="00585-410">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00585-410">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="00585-411">**Docker desteğini etkinleştir** **' i seçmeyin** .</span><span class="sxs-lookup"><span data-stu-id="00585-411">**Don't** select **Enable Docker Support**.</span></span>

![VS yeni proje iletişim kutusu](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="00585-413">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00585-413">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="00585-414">Açık [tümleşik Terminalini](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="00585-414">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="00585-415">Dizinleri (`cd`) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="00585-415">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="00585-416">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00585-416">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="00585-417">Bu komutlar yeni bir Web API projesi oluşturur ve yeni proje klasöründe Visual Studio Code yeni bir örneğini açar.</span><span class="sxs-lookup"><span data-stu-id="00585-417">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="00585-418">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-418">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="00585-419">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-419">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="00585-420">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-420">Select **File** > **New Solution**.</span></span>

  ![Yeni çözüm macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="00585-422">**.NET Core** > **uygulama** **API 'si** ileri ' **yi**seçin. > ></span><span class="sxs-lookup"><span data-stu-id="00585-422">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="00585-424">İçinde **, yeni ASP.NET Core Web API'sini yapılandırma** iletişim kutusunda varsayılan değerleri kabul **hedef Framework'ü** , \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="00585-424">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="00585-425">Girin *TodoApi* için **proje adı** seçip **Oluştur**.</span><span class="sxs-lookup"><span data-stu-id="00585-425">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="00585-427">API'yi test etme</span><span class="sxs-lookup"><span data-stu-id="00585-427">Test the API</span></span>

<span data-ttu-id="00585-428">Proje şablonu oluşturur bir `values` API.</span><span class="sxs-lookup"><span data-stu-id="00585-428">The project template creates a `values` API.</span></span> <span data-ttu-id="00585-429">Çağrı `Get` uygulamayı test etmek için bir tarayıcıdan yöntemi.</span><span class="sxs-lookup"><span data-stu-id="00585-429">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="00585-430">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-430">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="00585-431">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="00585-431">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="00585-432">Visual Studio bir tarayıcı ile başlatarak `https://localhost:<port>/api/values`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="00585-432">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="00585-433">IIS Express sertifika güven varsa soran bir iletişim kutusu alırsanız seçin **Evet**.</span><span class="sxs-lookup"><span data-stu-id="00585-433">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="00585-434">İçinde **Güvenlik Uyarısı** ardından, görüntülenen iletişim seçin **Evet**.</span><span class="sxs-lookup"><span data-stu-id="00585-434">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="00585-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00585-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="00585-436">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="00585-436">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="00585-437">Bir tarayıcıda aşağıdaki URL'ye gidin: [ https://localhost:5001/api/values ](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="00585-437">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="00585-438">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="00585-439">Uygulamayı başlatmak için**hata ayıklamayı Başlat** ' **ı seçin.**  > </span><span class="sxs-lookup"><span data-stu-id="00585-439">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="00585-440">Mac için Visual Studio bir tarayıcı ile başlatarak `https://localhost:<port>`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="00585-440">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="00585-441">HTTP 404 (bulunamadı) hatası döndürülür.</span><span class="sxs-lookup"><span data-stu-id="00585-441">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="00585-442">Append `/api/values` URL'sine (URL'yi `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="00585-442">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="00585-443">Aşağıdaki JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="00585-443">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="00585-444">Bir model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="00585-444">Add a model class</span></span>

<span data-ttu-id="00585-445">A *modeli* uygulamayı yöneten verilerini temsil eden sınıflar kümesidir.</span><span class="sxs-lookup"><span data-stu-id="00585-445">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="00585-446">Tek bir modeldir bu uygulama için `TodoItem` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="00585-446">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="00585-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-447">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="00585-448">İçinde **Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00585-448">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="00585-449">Seçin **ekleme** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="00585-449">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="00585-450">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="00585-450">Name the folder *Models*.</span></span>

* <span data-ttu-id="00585-451">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="00585-451">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="00585-452">Sınıf adı *Todoıtem* seçip **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="00585-452">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="00585-453">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="00585-453">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="00585-454">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00585-454">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="00585-455">Adlı bir klasör ekleme *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="00585-455">Add a folder named *Models*.</span></span>

* <span data-ttu-id="00585-456">Ekleme bir `TodoItem` sınıfının *modelleri* aşağıdaki kodla klasörü:</span><span class="sxs-lookup"><span data-stu-id="00585-456">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="00585-457">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-457">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="00585-458">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00585-458">Right-click the project.</span></span> <span data-ttu-id="00585-459">Seçin **ekleme** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="00585-459">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="00585-460">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="00585-460">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="00585-462">*Modeller* klasörüne sağ tıklayın ve **yeni dosya** > **Ekle** > **genel** > **boş sınıfı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-462">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="00585-463">Sınıf adı *Todoıtem*ve ardından **yeni**.</span><span class="sxs-lookup"><span data-stu-id="00585-463">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="00585-464">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="00585-464">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="00585-465">`Id` Özelliği işlevlerinin bir ilişkisel veritabanında benzersiz anahtar.</span><span class="sxs-lookup"><span data-stu-id="00585-465">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="00585-466">Model sınıfları herhangi bir projede gidip ancak *modelleri* klasörü, kural olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="00585-466">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="00585-467">Veritabanı bağlamı Ekle</span><span class="sxs-lookup"><span data-stu-id="00585-467">Add a database context</span></span>

<span data-ttu-id="00585-468">*Veritabanı bağlamı* koordine eden bir veri modeli için Entity Framework işlevsellik ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="00585-468">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="00585-469">Bu sınıf türetme tarafından oluşturulan `Microsoft.EntityFrameworkCore.DbContext` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="00585-469">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="00585-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-470">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="00585-471">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="00585-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="00585-472">Sınıf adı *TodoContext* tıklatıp **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="00585-472">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="00585-473">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="00585-474">Ekleme bir `TodoContext` sınıfının *modelleri* klasör.</span><span class="sxs-lookup"><span data-stu-id="00585-474">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="00585-475">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="00585-475">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="00585-476">Veritabanı bağlamı Kaydet</span><span class="sxs-lookup"><span data-stu-id="00585-476">Register the database context</span></span>

<span data-ttu-id="00585-477">ASP.NET Core DB bağlamı gibi hizmetler ile kaydedilmelidir [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcı.</span><span class="sxs-lookup"><span data-stu-id="00585-477">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="00585-478">Kapsayıcı hizmeti denetleyicilerine sağlar.</span><span class="sxs-lookup"><span data-stu-id="00585-478">The container provides the service to controllers.</span></span>

<span data-ttu-id="00585-479">Güncelleştirme *Startup.cs* aşağıdaki vurgulanmış kodu:</span><span class="sxs-lookup"><span data-stu-id="00585-479">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="00585-480">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="00585-480">The preceding code:</span></span>

* <span data-ttu-id="00585-481">Kullanılmayan kaldırır `using` bildirimleri.</span><span class="sxs-lookup"><span data-stu-id="00585-481">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="00585-482">Veritabanı bağlamı DI kapsayıcıya ekler.</span><span class="sxs-lookup"><span data-stu-id="00585-482">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="00585-483">Veritabanı bağlamı bir bellek içi veritabanına kullanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="00585-483">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="00585-484">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="00585-484">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="00585-485">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-485">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="00585-486">Sağ *denetleyicileri* klasör.</span><span class="sxs-lookup"><span data-stu-id="00585-486">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="00585-487">**Yeni öğe** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-487">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="00585-488">İçinde **Yeni Öğe Ekle** iletişim kutusunda **API denetleyici sınıfı** şablonu.</span><span class="sxs-lookup"><span data-stu-id="00585-488">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="00585-489">Sınıf adı *TodoController*seçip **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="00585-489">Name the class *TodoController*, and select **Add**.</span></span>

  ![Yeni öğe iletişim denetleyicisiyle seçilen arama kutusu ve web API denetleyicisi Ekle](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="00585-491">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-491">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="00585-492">İçinde *denetleyicileri* klasör adında bir sınıf oluşturma `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="00585-492">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="00585-493">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="00585-493">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="00585-494">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="00585-494">The preceding code:</span></span>

* <span data-ttu-id="00585-495">Bir API denetleyicisi sınıfı yöntemleri olmadan tanımlar.</span><span class="sxs-lookup"><span data-stu-id="00585-495">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="00585-496">Sınıfı [[Apicontroller]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) özniteliğiyle süsler.</span><span class="sxs-lookup"><span data-stu-id="00585-496">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="00585-497">Bu öznitelik, denetleyicinin web API'si isteklerine yanıt verdiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="00585-497">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="00585-498">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="00585-498">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="00585-499">Veritabanı bağlamı eklemesine DI kullanır (`TodoContext`) içine denetleyici.</span><span class="sxs-lookup"><span data-stu-id="00585-499">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="00585-500">Her bir veritabanı bağlamı kullanılan [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) denetleyici yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="00585-500">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="00585-501">Adlı bir öğe ekler `Item1` veritabanı boşsa veritabanı.</span><span class="sxs-lookup"><span data-stu-id="00585-501">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="00585-502">Her çalıştığında bu kod oluşturucusunun içinde yeni bir HTTP isteği olduğundan.</span><span class="sxs-lookup"><span data-stu-id="00585-502">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="00585-503">Tüm öğeleri silerseniz, oluşturucu oluşturur `Item1` API yöntemi çağrıldığında tekrar başlattığınızda.</span><span class="sxs-lookup"><span data-stu-id="00585-503">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="00585-504">Bu nedenle, gerçekten işe yaradı silme işlemi işe yaramadı gibi görünebilir.</span><span class="sxs-lookup"><span data-stu-id="00585-504">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="00585-505">Get yöntemleri ekleyin</span><span class="sxs-lookup"><span data-stu-id="00585-505">Add Get methods</span></span>

<span data-ttu-id="00585-506">Yapılacak iş öğeleri alır bir API sağlamak için aşağıdaki yöntemi ekleyin. `TodoController` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="00585-506">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="00585-507">İki GET uç noktası bu yöntemleri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="00585-507">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="00585-508">Hala çalışıyorsa uygulamayı durdurun.</span><span class="sxs-lookup"><span data-stu-id="00585-508">Stop the app if it's still running.</span></span> <span data-ttu-id="00585-509">Ardından, en son değişiklikleri dahil etmek için yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00585-509">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="00585-510">Bir tarayıcıdan iki uç nokta çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="00585-510">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="00585-511">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="00585-511">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="00585-512">Şu HTTP yanıtı çağrısı tarafından üretilen `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="00585-512">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="00585-513">URL Yönlendirme ve yolları</span><span class="sxs-lookup"><span data-stu-id="00585-513">Routing and URL paths</span></span>

<span data-ttu-id="00585-514">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Özniteliği bir HTTP GET isteğine yanıt vermeden bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="00585-514">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="00585-515">Her yöntem için URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="00585-515">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="00585-516">Denetleyicinin şablonu dizesi ile başlayıp `Route` özniteliği:</span><span class="sxs-lookup"><span data-stu-id="00585-516">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="00585-517">Değiştirin `[controller]` denetleyicinin adı ile kural tarafından olduğu "Controller" soneki eksi denetleyici sınıfı adı.</span><span class="sxs-lookup"><span data-stu-id="00585-517">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="00585-518">Bu örnek, denetleyici sınıfı adı olan **Todo**Denetleyici adı "todo" Bu nedenle denetleyicisi.</span><span class="sxs-lookup"><span data-stu-id="00585-518">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="00585-519">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="00585-519">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="00585-520">Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]`), yola ekleyin. `[HttpGet]`</span><span class="sxs-lookup"><span data-stu-id="00585-520">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="00585-521">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="00585-521">This sample doesn't use a template.</span></span> <span data-ttu-id="00585-522">Daha fazla bilgi için [özniteliği Http [eylem] özniteliği ile yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="00585-522">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="00585-523">Aşağıdaki `GetTodoItem` yöntemi `"{id}"` yapılacak iş öğesi benzersiz tanımlayıcısı için bir yer tutucu değişkendir.</span><span class="sxs-lookup"><span data-stu-id="00585-523">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="00585-524">Zaman `GetTodoItem` çağrılır, değerini `"{id}"` yöntemine URL'de sağlanan kendi`id` parametresi.</span><span class="sxs-lookup"><span data-stu-id="00585-524">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="00585-525">Döndürülen değerler</span><span class="sxs-lookup"><span data-stu-id="00585-525">Return values</span></span>

<span data-ttu-id="00585-526">Dönüş türünü `GetTodoItems` ve `GetTodoItem` yöntemler [actionresult öğesini\<T > türü](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="00585-526">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="00585-527">ASP.NET Core, nesneyi otomatik olarak serileştiren [JSON](https://www.json.org/) ve yanıt iletisinin gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="00585-527">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="00585-528">Yanıt kodu 200 bu dönüş türü için olduğu varsayılırsa işlenmeyen özel durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="00585-528">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="00585-529">İşlenmeyen özel durumları 5xx hatalarla karşılaşırsanız çevrilir.</span><span class="sxs-lookup"><span data-stu-id="00585-529">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="00585-530">`ActionResult` dönüş türleri, geniş HTTP durum kodları temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="00585-530">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="00585-531">Örneğin, `GetTodoItem` iki farklı durum değerleri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="00585-531">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="00585-532">Öğe istenen kimliği eşleşirse, yöntem bir 404 döndürür [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) hata kodu.</span><span class="sxs-lookup"><span data-stu-id="00585-532">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="00585-533">Aksi takdirde yöntem bir JSON yanıt gövdesine 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="00585-533">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="00585-534">Döndüren `item` sonuçları bir HTTP 200 yanıtı.</span><span class="sxs-lookup"><span data-stu-id="00585-534">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="00585-535">Test GetTodoItems yöntemi</span><span class="sxs-lookup"><span data-stu-id="00585-535">Test the GetTodoItems method</span></span>

<span data-ttu-id="00585-536">Bu öğreticide Postman web API'si test etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="00585-536">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="00585-537">Yükleme [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="00585-537">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="00585-538">Web uygulaması başlatın.</span><span class="sxs-lookup"><span data-stu-id="00585-538">Start the web app.</span></span>
* <span data-ttu-id="00585-539">Postman'i başlatın.</span><span class="sxs-lookup"><span data-stu-id="00585-539">Start Postman.</span></span>
* <span data-ttu-id="00585-540">Devre dışı **SSL sertifika doğrulama**</span><span class="sxs-lookup"><span data-stu-id="00585-540">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="00585-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-541">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="00585-542">**Dosya** ayarlarından (Genel sekmesinden) SSL sertifikası doğrulamasını devre dışı bırakın. ></span><span class="sxs-lookup"><span data-stu-id="00585-542">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="00585-543">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00585-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="00585-544">**Postman** > **tercihleri** ' nden (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="00585-544">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="00585-545">Alternatif olarak, wranı seçin ve **Ayarlar**' ı seçip SSL sertifikası doğrulamasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="00585-545">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="00585-546">Test denetleyicisi sonra SSL sertifika doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="00585-546">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="00585-547">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00585-547">Create a new request.</span></span>
  * <span data-ttu-id="00585-548">HTTP yöntemi kümesine **alma**.</span><span class="sxs-lookup"><span data-stu-id="00585-548">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="00585-549">İstek URL'si kümesine `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="00585-549">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="00585-550">Örneğin: `https://localhost:5001/api/todo`</span><span class="sxs-lookup"><span data-stu-id="00585-550">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="00585-551">Ayarlama **iki bölme görünümü** postman'deki.</span><span class="sxs-lookup"><span data-stu-id="00585-551">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="00585-552">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-552">Select **Send**.</span></span>

![Get isteğiyle postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="00585-554">Create yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="00585-554">Add a Create method</span></span>

<span data-ttu-id="00585-555">`PostTodoItem` *Controllers/TodoController. cs*içindeki şu yöntemi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="00585-555">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="00585-556">Yukarıdaki kod tarafından belirtildiği gibi bir HTTP POST yöntemi olup [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) özniteliği.</span><span class="sxs-lookup"><span data-stu-id="00585-556">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="00585-557">Yöntemi, HTTP isteği gövdesinden Yapılacaklar öğenin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="00585-557">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="00585-558">`CreatedAtAction` Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="00585-558">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="00585-559">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="00585-559">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="00585-560">HTTP 201 sunucuda yeni bir kaynak oluşturan bir HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="00585-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="00585-561">Yanıta bir `Location` üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="00585-561">Adds a `Location` header to the response.</span></span> <span data-ttu-id="00585-562">`Location` Üst bilgi, yeni oluşturulan Yapılacaklar öğesinin URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="00585-562">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="00585-563">Daha fazla bilgi için [10.2.2 201 oluşturuldu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="00585-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="00585-564">`Location` Üstbilginin URI 'sini oluşturma eylemine başvurur. `GetTodoItem`</span><span class="sxs-lookup"><span data-stu-id="00585-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="00585-565">Anahtar sözcüğü, `CreatedAtAction` çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır. C# `nameof`</span><span class="sxs-lookup"><span data-stu-id="00585-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="00585-566">Test PostTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="00585-566">Test the PostTodoItem method</span></span>

* <span data-ttu-id="00585-567">Projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00585-567">Build the project.</span></span>
* <span data-ttu-id="00585-568">Postman HTTP yöntemi kümesine `POST`.</span><span class="sxs-lookup"><span data-stu-id="00585-568">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="00585-569">Seçin **gövdesi** sekmesi.</span><span class="sxs-lookup"><span data-stu-id="00585-569">Select the **Body** tab.</span></span>
* <span data-ttu-id="00585-570">Seçin **ham** radyo düğmesi.</span><span class="sxs-lookup"><span data-stu-id="00585-570">Select the **raw** radio button.</span></span>
* <span data-ttu-id="00585-571">Tür kümesine **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="00585-571">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="00585-572">İstek gövdesinde bir yapılacak iş öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="00585-572">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="00585-573">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-573">Select **Send**.</span></span>

  ![Postman ile isteği oluştur](first-web-api/_static/create.png)

  <span data-ttu-id="00585-575">Bir 405 yöntemine izin verilmiyor hatası alırsanız, `PostTodoItem` Yöntem eklendikten sonra projeyi derlememe sonucu büyük olasılıkla oluşur.</span><span class="sxs-lookup"><span data-stu-id="00585-575">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="00585-576">Konum üst bilgisi URI test</span><span class="sxs-lookup"><span data-stu-id="00585-576">Test the location header URI</span></span>

* <span data-ttu-id="00585-577">Seçin **üstbilgileri** sekmesinde **yanıt** bölmesi.</span><span class="sxs-lookup"><span data-stu-id="00585-577">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="00585-578">Kopyalama **konumu** üst bilgi değeri:</span><span class="sxs-lookup"><span data-stu-id="00585-578">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üst bilgiler sekmesi](first-web-api/_static/pmc2.png)

* <span data-ttu-id="00585-580">Yöntemini GET öğesine Ayarla.</span><span class="sxs-lookup"><span data-stu-id="00585-580">Set the method to GET.</span></span>
* <span data-ttu-id="00585-581">URİ'sini yapıştırın (örneğin, `https://localhost:5001/api/Todo/2`)</span><span class="sxs-lookup"><span data-stu-id="00585-581">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="00585-582">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="00585-582">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="00585-583">PutTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="00585-583">Add a PutTodoItem method</span></span>

<span data-ttu-id="00585-584">Aşağıdaki `PutTodoItem` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="00585-584">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="00585-585">`PutTodoItem` benzer `PostTodoItem`, HTTP PUT kullanır.</span><span class="sxs-lookup"><span data-stu-id="00585-585">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="00585-586">Yanıt [204 (içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="00585-586">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="00585-587">HTTP belirtimine göre bir PUT İsteği tüm güncelleştirilmiş varlık yalnızca değişiklikler değil göndermek istemci gerektirir.</span><span class="sxs-lookup"><span data-stu-id="00585-587">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="00585-588">Kısmi güncelleştirmeleri desteklemek için kullanma [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="00585-588">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="00585-589">Çağrılırken `PutTodoItem`bir hata alırsanız, veritabanında bir öğe `GET` olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="00585-589">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="00585-590">Test PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="00585-590">Test the PutTodoItem method</span></span>

<span data-ttu-id="00585-591">Bu örnek, uygulama her başlatıldığında yeniden başlatılması gereken bellek içi veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="00585-591">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="00585-592">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="00585-592">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="00585-593">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="00585-593">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="00585-594">Kimliğine sahip bir yapılacak iş öğesi güncelleştirme = 1 ve "balık akış için" adını girin:</span><span class="sxs-lookup"><span data-stu-id="00585-594">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="00585-595">Aşağıdaki görüntüde, Postman güncelleştirme gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="00585-595">The following image shows the Postman update:</span></span>

![204 (içerik yok) yanıtı gösteren postman konsol](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="00585-597">DeleteTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="00585-597">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="00585-598">Aşağıdaki `DeleteTodoItem` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="00585-598">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="00585-599">`DeleteTodoItem` Yanıt [204 (içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="00585-599">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="00585-600">Test DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="00585-600">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="00585-601">Postman bir yapılacak iş öğesini silmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="00585-601">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="00585-602">Yöntem kümesine `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="00585-602">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="00585-603">Silmek, örneğin nesnenin URI ayarlayın `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="00585-603">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="00585-604">Seçin **Gönder**</span><span class="sxs-lookup"><span data-stu-id="00585-604">Select **Send**</span></span>

<span data-ttu-id="00585-605">Örnek uygulama, tüm öğeleri silmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="00585-605">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="00585-606">Ancak, son öğe silindiğinde, API 'nin bir sonraki çağrılışında model sınıfı Oluşturucu tarafından yeni bir tane oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="00585-606">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="00585-607">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="00585-607">Call the web API with JavaScript</span></span>

<span data-ttu-id="00585-608">Bu bölümde, Web API 'sini çağırmak için JavaScript kullanan bir HTML sayfası eklenir.</span><span class="sxs-lookup"><span data-stu-id="00585-608">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="00585-609">Fetch API 'SI isteği başlatır.</span><span class="sxs-lookup"><span data-stu-id="00585-609">The Fetch API initiates the request.</span></span> <span data-ttu-id="00585-610">JavaScript, sayfayı Web API 'sinin yanıtından alınan ayrıntılarla güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="00585-610">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="00585-611">Uygulamayı [statik dosyalara sunacak](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) şekilde yapılandırın ve aşağıdaki vurgulanmış kodla *Startup.cs* güncelleştirerek [varsayılan dosya eşlemesini etkinleştirin](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) :</span><span class="sxs-lookup"><span data-stu-id="00585-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="00585-612">Oluşturma bir *wwwroot* proje dizininde klasör.</span><span class="sxs-lookup"><span data-stu-id="00585-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="00585-613">Adlı bir HTML dosyası ekleyin *index.html* için *wwwroot* dizin.</span><span class="sxs-lookup"><span data-stu-id="00585-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="00585-614">Dosyanın içeriğini aşağıdaki biçimlendirme ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="00585-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="00585-615">Adlı bir JavaScript dosyası ekleyin *site.js* için *wwwroot* dizin.</span><span class="sxs-lookup"><span data-stu-id="00585-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="00585-616">Dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="00585-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="00585-617">ASP.NET Core proje başlatma ayarlarında bir değişiklik HTML sayfasını yerel olarak test etmek için gerekli:</span><span class="sxs-lookup"><span data-stu-id="00585-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="00585-618">Açık *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="00585-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="00585-619">Kaldırma `launchUrl` , açmak için uygulamayı zorlamak için özellik *index.html*&mdash;projenin varsayılan dosya.</span><span class="sxs-lookup"><span data-stu-id="00585-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="00585-620">Bu örnek, Web API 'sinin tüm CRUD yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="00585-620">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="00585-621">API çağrıları açıklamaları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="00585-621">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="00585-622">Yapılacaklar öğelerinin bir listesini alın</span><span class="sxs-lookup"><span data-stu-id="00585-622">Get a list of to-do items</span></span>

<span data-ttu-id="00585-623">Fetch, Web API 'sine bir HTTP GET isteği gönderir ve bu, Yapılacaklar öğeleri dizisini temsil eden JSON döndürür.</span><span class="sxs-lookup"><span data-stu-id="00585-623">Fetch sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="00585-624">`success` İstek başarılı olursa geri çağırma işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="00585-624">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="00585-625">Geri çağırma içinde DOM Yapılacaklar bilgilerle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="00585-625">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="00585-626">Yapılacak İş Öğesi Ekle</span><span class="sxs-lookup"><span data-stu-id="00585-626">Add a to-do item</span></span>

<span data-ttu-id="00585-627">Fetch, istek gövdesinde Yapılacaklar öğesiyle bir HTTP POST isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="00585-627">Fetch sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="00585-628">`accepts` Ve `contentType` seçeneklerini ayarlamak `application/json` gönderilen ve alınan medya türü belirtmek için.</span><span class="sxs-lookup"><span data-stu-id="00585-628">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="00585-629">Yapılacak iş öğesi kullanarak JSON'a dönüştürülür [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="00585-629">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="00585-630">API'yi bir başarılı durum kodu döndürdüğünde `getData` işlevi HTML tablosu güncelleştirmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="00585-630">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="00585-631">Yapılacak iş öğesini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="00585-631">Update a to-do item</span></span>

<span data-ttu-id="00585-632">Yapılacak iş öğesi güncelleştirilirken bir eklemeye benzerdir.</span><span class="sxs-lookup"><span data-stu-id="00585-632">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="00585-633">`url` Öğenin benzersiz tanıtıcısı eklemek için değişiklikleri ve `type` olduğu `PUT`.</span><span class="sxs-lookup"><span data-stu-id="00585-633">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="00585-634">Yapılacak iş öğesi silme</span><span class="sxs-lookup"><span data-stu-id="00585-634">Delete a to-do item</span></span>

<span data-ttu-id="00585-635">Yapılacak iş öğesi silme gerçekleştirilir ayarlayarak `type` AJAX çağrısı hedefi üzerinde `DELETE` ve öğenin benzersiz tanıtıcısı URL'yi belirterek.</span><span class="sxs-lookup"><span data-stu-id="00585-635">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="00585-636">Web API 'sine kimlik doğrulama desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="00585-636">Add authentication support to a web API</span></span>

<span data-ttu-id="00585-637">Bkz. [ıdentityserver4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) öğreticisi.</span><span class="sxs-lookup"><span data-stu-id="00585-637">See the [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00585-638">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="00585-638">Additional resources</span></span>

<span data-ttu-id="00585-639">[Görüntülemek veya Bu öğretici için örnek kodu indirdikten](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="00585-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="00585-640">Bkz: [nasıl indirileceğini](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="00585-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="00585-641">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="00585-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="00585-642">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="00585-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
