---
title: "Öğretici: ASP.NET Core ile Web API 'SI oluşturma"
author: rick-anderson
description: ASP.NET Core ile Web API 'SI oluşturmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 366323416061bf729c092419f2f6a5912884252b
ms.sourcegitcommit: 5d25a7f22c50ca6fdd0f8ecd8e525822e1b35b7a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/28/2019
ms.locfileid: "71551727"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="6b1e7-103">Öğretici: ASP.NET Core ile Web API 'SI oluşturma</span><span class="sxs-lookup"><span data-stu-id="6b1e7-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="6b1e7-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="6b1e7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="6b1e7-105">Bu öğretici, bir web API ASP.NET Core ile oluşturmaya ilişkin temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6b1e7-106">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6b1e7-107">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-107">Create a web API project.</span></span>
> * <span data-ttu-id="6b1e7-108">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="6b1e7-109">CRUD yöntemleriyle bir denetleyiciyi dolandırın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="6b1e7-110">Yönlendirmeyi, URL yollarını ve dönüş değerlerini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="6b1e7-111">Web API'si Postman ile çağırın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-111">Call the web API with Postman.</span></span>

<span data-ttu-id="6b1e7-112">Sonunda, bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="6b1e7-113">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="6b1e7-113">Overview</span></span>

<span data-ttu-id="6b1e7-114">Bu öğretici yandaki API oluşturur:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="6b1e7-115">API</span><span class="sxs-lookup"><span data-stu-id="6b1e7-115">API</span></span> | <span data-ttu-id="6b1e7-116">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6b1e7-116">Description</span></span> | <span data-ttu-id="6b1e7-117">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-117">Request body</span></span> | <span data-ttu-id="6b1e7-118">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="6b1e7-119">/Api/TodoItems al</span><span class="sxs-lookup"><span data-stu-id="6b1e7-119">GET /api/TodoItems</span></span> | <span data-ttu-id="6b1e7-120">Tüm yapılacak iş öğeleri al</span><span class="sxs-lookup"><span data-stu-id="6b1e7-120">Get all to-do items</span></span> | <span data-ttu-id="6b1e7-121">Yok.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-121">None</span></span> | <span data-ttu-id="6b1e7-122">Yapılacaklar öğelerinin bir dizisi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-122">Array of to-do items</span></span>|
|<span data-ttu-id="6b1e7-123">/Api/TodoItems/{id} al</span><span class="sxs-lookup"><span data-stu-id="6b1e7-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="6b1e7-124">Bir öğeyi Kimliğine göre Al</span><span class="sxs-lookup"><span data-stu-id="6b1e7-124">Get an item by ID</span></span> | <span data-ttu-id="6b1e7-125">Yok.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-125">None</span></span> | <span data-ttu-id="6b1e7-126">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-126">To-do item</span></span>|
|<span data-ttu-id="6b1e7-127">POST/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="6b1e7-127">POST /api/TodoItems</span></span> | <span data-ttu-id="6b1e7-128">Yeni Öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="6b1e7-128">Add a new item</span></span> | <span data-ttu-id="6b1e7-129">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-129">To-do item</span></span> | <span data-ttu-id="6b1e7-130">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-130">To-do item</span></span> |
|<span data-ttu-id="6b1e7-131">/Api/TodoItems/{id} koy</span><span class="sxs-lookup"><span data-stu-id="6b1e7-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="6b1e7-132">Mevcut öğeyi güncelleştirin &nbsp;</span><span class="sxs-lookup"><span data-stu-id="6b1e7-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="6b1e7-133">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-133">To-do item</span></span> | <span data-ttu-id="6b1e7-134">Yok.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-134">None</span></span> |
|<span data-ttu-id="6b1e7-135">/Api/TodoItems/{id} &nbsp; Sil&nbsp;</span><span class="sxs-lookup"><span data-stu-id="6b1e7-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="6b1e7-136">Öğeyi Sil &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="6b1e7-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="6b1e7-137">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="6b1e7-137">None</span></span> | <span data-ttu-id="6b1e7-138">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="6b1e7-138">None</span></span>|

<span data-ttu-id="6b1e7-139">Aşağıdaki diyagramda, bu uygulamanın tasarımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-139">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="6b1e7-145">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="6b1e7-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b1e7-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b1e7-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b1e7-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6b1e7-148">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="6b1e7-149">Bir web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="6b1e7-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b1e7-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b1e7-151">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6b1e7-152">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="6b1e7-153">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="6b1e7-154">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,0** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="6b1e7-155">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-155">Select the **API** template and click **Create**.</span></span>

![VS yeni proje iletişim kutusu](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b1e7-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b1e7-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b1e7-158">Açık [tümleşik Terminalini](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6b1e7-159">Dizinleri (`cd`) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="6b1e7-160">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="6b1e7-161">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="6b1e7-162">Yukarıdaki komutlar:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-162">The preceding commands:</span></span>

  * <span data-ttu-id="6b1e7-163">Yeni bir Web API projesi oluşturur ve Visual Studio Code açar.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="6b1e7-164">Sonraki bölümde gerekli olan NuGet paketlerini ekler.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6b1e7-165">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6b1e7-166">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-166">Select **File** > **New Solution**.</span></span>

  ![Yeni çözüm macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="6b1e7-168">**.NET Core** > **uygulama** **API 'si** ileri ' **yi**seçin. > ></span><span class="sxs-lookup"><span data-stu-id="6b1e7-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="6b1e7-170">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda, **hedef Framework** \* *.NET Core 3,0*' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="6b1e7-171">Girin *TodoApi* için **proje adı** seçip **Oluştur**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="6b1e7-173">Proje klasöründe bir komut terminali açın ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="6b1e7-174">API'yi test etme</span><span class="sxs-lookup"><span data-stu-id="6b1e7-174">Test the API</span></span>

<span data-ttu-id="6b1e7-175">Proje şablonu oluşturur bir `WeatherForecast` API.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="6b1e7-176">Çağrı `Get` uygulamayı test etmek için bir tarayıcıdan yöntemi.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b1e7-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b1e7-178">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="6b1e7-179">Visual Studio bir tarayıcı ile başlatarak `https://localhost:<port>/WeatherForecast`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="6b1e7-180">IIS Express sertifika güven varsa soran bir iletişim kutusu alırsanız seçin **Evet**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="6b1e7-181">İçinde **Güvenlik Uyarısı** ardından, görüntülenen iletişim seçin **Evet**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b1e7-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b1e7-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6b1e7-183">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="6b1e7-184">Bir tarayıcıda aşağıdaki URL'ye gidin: [ https://localhost:5001/WeatherForecast ](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6b1e7-185">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6b1e7-186">Uygulamayı başlatmak için**hata ayıklamayı Başlat** ' **ı seçin.**  > </span><span class="sxs-lookup"><span data-stu-id="6b1e7-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="6b1e7-187">Mac için Visual Studio bir tarayıcı ile başlatarak `https://localhost:<port>`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="6b1e7-188">HTTP 404 (bulunamadı) hatası döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="6b1e7-189">Append `/WeatherForecast` URL'sine (URL'yi `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="6b1e7-190">Aşağıdakine benzer bir JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="6b1e7-191">Bir model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="6b1e7-191">Add a model class</span></span>

<span data-ttu-id="6b1e7-192">A *modeli* uygulamayı yöneten verilerini temsil eden sınıflar kümesidir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="6b1e7-193">Tek bir modeldir bu uygulama için `TodoItem` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b1e7-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b1e7-195">İçinde **Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="6b1e7-196">Seçin **ekleme** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="6b1e7-197">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="6b1e7-198">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="6b1e7-199">Sınıf adı *Todoıtem* seçip **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="6b1e7-200">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b1e7-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b1e7-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b1e7-202">Adlı bir klasör ekleme *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="6b1e7-203">Ekleme bir `TodoItem` sınıfının *modelleri* aşağıdaki kodla klasörü:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6b1e7-204">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6b1e7-205">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-205">Right-click the project.</span></span> <span data-ttu-id="6b1e7-206">Seçin **ekleme** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="6b1e7-207">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-207">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="6b1e7-209">*Modeller* klasörüne sağ tıklayın ve **yeni dosya** > **Ekle** > **genel** > **boş sınıfı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="6b1e7-210">Sınıf adı *Todoıtem*ve ardından **yeni**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="6b1e7-211">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="6b1e7-212">`Id` Özelliği işlevlerinin bir ilişkisel veritabanında benzersiz anahtar.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="6b1e7-213">Model sınıfları herhangi bir projede gidip ancak *modelleri* klasörü, kural olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="6b1e7-214">Veritabanı bağlamı Ekle</span><span class="sxs-lookup"><span data-stu-id="6b1e7-214">Add a database context</span></span>

<span data-ttu-id="6b1e7-215">*Veritabanı bağlamı* koordine eden bir veri modeli için Entity Framework işlevsellik ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="6b1e7-216">Bu sınıf türetme tarafından oluşturulan `Microsoft.EntityFrameworkCore.DbContext` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b1e7-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="6b1e7-218">Microsoft. EntityFrameworkCore. SqlServer ekleyin</span><span class="sxs-lookup"><span data-stu-id="6b1e7-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="6b1e7-219">**Araçlar** menüsünde **nuget Paket Yöneticisi > çözüm Için NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="6b1e7-220">**Araştır** sekmesini seçin ve arama kutusuna **Microsoft. Entityframeworkcore. SqlServer** yazın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="6b1e7-221">Sol bölmedeki **Microsoft. EntityFrameworkCore. SqlServer** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-221">Select  **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="6b1e7-222">Sağ bölmedeki **Proje** onay kutusunu seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="6b1e7-223">Önceki yönergeleri kullanarak `Microsoft.EntityFrameworkCore.InMemory` NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet Paket Yöneticisi](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="6b1e7-225">TodoContext veritabanı bağlamını ekleme</span><span class="sxs-lookup"><span data-stu-id="6b1e7-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="6b1e7-226">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="6b1e7-227">Sınıf adı *TodoContext* tıklatıp **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6b1e7-228">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6b1e7-229">Ekleme bir `TodoContext` sınıfının *modelleri* klasör.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="6b1e7-230">Aşağıdaki kodu girin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="6b1e7-231">Veritabanı bağlamı Kaydet</span><span class="sxs-lookup"><span data-stu-id="6b1e7-231">Register the database context</span></span>

<span data-ttu-id="6b1e7-232">ASP.NET Core DB bağlamı gibi hizmetler ile kaydedilmelidir [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcı.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6b1e7-233">Kapsayıcı hizmeti denetleyicilerine sağlar.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="6b1e7-234">Güncelleştirme *Startup.cs* aşağıdaki vurgulanmış kodu:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="6b1e7-235">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-235">The preceding code:</span></span>

* <span data-ttu-id="6b1e7-236">Kullanılmayan kaldırır `using` bildirimleri.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="6b1e7-237">Veritabanı bağlamı DI kapsayıcıya ekler.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="6b1e7-238">Veritabanı bağlamı bir bellek içi veritabanına kullanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="6b1e7-239">Denetleyiciyi bir denetleyiciye katlama</span><span class="sxs-lookup"><span data-stu-id="6b1e7-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b1e7-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b1e7-241">Sağ *denetleyicileri* klasör.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="6b1e7-242">**Yeni yapı iskelesi öğesi** **Ekle** > öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="6b1e7-243">**Entity Framework kullanarak ve eylemler Içeren API denetleyicisi**' ni seçin ve ardından **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="6b1e7-244">**API denetleyiciyi eylemler Ile Ekle ' de Entity Framework** iletişim kutusunu kullanarak:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="6b1e7-245">**Model sınıfında** **TodoItem (TodoApi. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="6b1e7-246">**Veri bağlamı sınıfında** **TodoContext (TodoApi. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="6b1e7-247">**Ekle** 'yi seçin</span><span class="sxs-lookup"><span data-stu-id="6b1e7-247">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6b1e7-248">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6b1e7-249">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="6b1e7-250">Yukarıdaki komutlar:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-250">The preceding commands:</span></span>

* <span data-ttu-id="6b1e7-251">Yapı iskelesi için gereken NuGet paketlerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="6b1e7-252">Scafkatlama altyapısını (`dotnet-aspnet-codegenerator`) kurar.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="6b1e7-253">Yapı iskelesi `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="6b1e7-254">Oluşturulan kod:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-254">The generated code:</span></span>

* <span data-ttu-id="6b1e7-255">Bir API denetleyicisi sınıfı yöntemleri olmadan tanımlar.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-255">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="6b1e7-256">Sınıfı [[Apicontroller]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) özniteliğiyle süsler.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-256">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="6b1e7-257">Bu öznitelik, denetleyicinin web API'si isteklerine yanıt verdiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-257">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="6b1e7-258">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-258">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="6b1e7-259">Veritabanı bağlamı eklemesine DI kullanır (`TodoContext`) içine denetleyici.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-259">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="6b1e7-260">Her bir veritabanı bağlamı kullanılan [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) denetleyici yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-260">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="6b1e7-261">PostTodoItem Create metodunu inceleyin</span><span class="sxs-lookup"><span data-stu-id="6b1e7-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="6b1e7-262">İçindeki `PostTodoItem` return ifadesini, [NameOf](/dotnet/csharp/language-reference/operators/nameof) işlecini kullanmak için değiştirin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="6b1e7-263">Yukarıdaki kod tarafından belirtildiği gibi bir HTTP POST yöntemi olup [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) özniteliği.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-263">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="6b1e7-264">Yöntemi, HTTP isteği gövdesinden Yapılacaklar öğenin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="6b1e7-265"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="6b1e7-266">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="6b1e7-267">HTTP 201 sunucuda yeni bir kaynak oluşturan bir HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="6b1e7-268">Yanıta bir [konum](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) üst bilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="6b1e7-269">Üst bilgi, yeni oluşturulan Yapılacaklar öğesinin URI 'sini belirtir. [](https://developer.mozilla.org/docs/Glossary/URI) `Location`</span><span class="sxs-lookup"><span data-stu-id="6b1e7-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="6b1e7-270">Daha fazla bilgi için [10.2.2 201 oluşturuldu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="6b1e7-271">`Location` Üstbilginin URI 'sini oluşturma eylemine başvurur. `GetTodoItem`</span><span class="sxs-lookup"><span data-stu-id="6b1e7-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="6b1e7-272">Anahtar sözcüğü, `CreatedAtAction` çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır. C# `nameof`</span><span class="sxs-lookup"><span data-stu-id="6b1e7-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="6b1e7-273">Postman yükleme</span><span class="sxs-lookup"><span data-stu-id="6b1e7-273">Install Postman</span></span>

<span data-ttu-id="6b1e7-274">Bu öğreticide Postman web API'si test etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="6b1e7-275">Yükleme [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="6b1e7-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="6b1e7-276">Web uygulaması başlatın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-276">Start the web app.</span></span>
* <span data-ttu-id="6b1e7-277">Postman'i başlatın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-277">Start Postman.</span></span>
* <span data-ttu-id="6b1e7-278">Devre dışı **SSL sertifika doğrulama**</span><span class="sxs-lookup"><span data-stu-id="6b1e7-278">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="6b1e7-279">Gelen **Dosya > Ayarlar** (\**genel* sekmesinde), devre dışı **SSL sertifika doğrulama**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-279">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="6b1e7-280">Test denetleyicisi sonra SSL sertifika doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="6b1e7-281">Postman ile test PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="6b1e7-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="6b1e7-282">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-282">Create a new request.</span></span>
* <span data-ttu-id="6b1e7-283">HTTP yöntemini olarak `POST`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="6b1e7-284">Seçin **gövdesi** sekmesi.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="6b1e7-285">Seçin **ham** radyo düğmesi.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="6b1e7-286">Tür kümesine **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="6b1e7-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="6b1e7-287">İstek gövdesinde bir yapılacak iş öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="6b1e7-288">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-288">Select **Send**.</span></span>

  ![Postman ile isteği oluştur](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="6b1e7-290">Konum üst bilgisi URI test</span><span class="sxs-lookup"><span data-stu-id="6b1e7-290">Test the location header URI</span></span>

* <span data-ttu-id="6b1e7-291">Seçin **üstbilgileri** sekmesinde **yanıt** bölmesi.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="6b1e7-292">Kopyalama **konumu** üst bilgi değeri:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-292">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üst bilgiler sekmesi](first-web-api/_static/3/create.png)

* <span data-ttu-id="6b1e7-294">Yöntemini GET öğesine Ayarla.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-294">Set the method to GET.</span></span>
* <span data-ttu-id="6b1e7-295">URİ'sini yapıştırın (örneğin, `https://localhost:5001/api/TodoItems/1`)</span><span class="sxs-lookup"><span data-stu-id="6b1e7-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="6b1e7-296">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="6b1e7-297">GET yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="6b1e7-297">Examine the GET methods</span></span>

<span data-ttu-id="6b1e7-298">İki GET uç noktası bu yöntemleri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="6b1e7-299">Tarayıcıdan veya Postman 'dan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="6b1e7-300">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-300">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="6b1e7-301">Şuna benzer bir yanıt, şu çağrı `GetTodoItems`tarafından üretilir:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="6b1e7-302">Postman ile test al</span><span class="sxs-lookup"><span data-stu-id="6b1e7-302">Test Get with Postman</span></span>

* <span data-ttu-id="6b1e7-303">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-303">Create a new request.</span></span>
* <span data-ttu-id="6b1e7-304">HTTP yöntemi kümesine **alma**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="6b1e7-305">İstek URL'si kümesine `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="6b1e7-306">Örneğin: `https://localhost:5001/api/TodoItems`</span><span class="sxs-lookup"><span data-stu-id="6b1e7-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="6b1e7-307">Ayarlama **iki bölme görünümü** postman'deki.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="6b1e7-308">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-308">Select **Send**.</span></span>

<span data-ttu-id="6b1e7-309">Bu uygulama, bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-309">This app uses an in-memory database.</span></span> <span data-ttu-id="6b1e7-310">Uygulama durdurulup başlatılırsa, önceki GET isteği herhangi bir veri döndürmez.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="6b1e7-311">Hiçbir veri döndürülmezse, verileri uygulamaya [gönderin](#post) .</span><span class="sxs-lookup"><span data-stu-id="6b1e7-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="6b1e7-312">URL Yönlendirme ve yolları</span><span class="sxs-lookup"><span data-stu-id="6b1e7-312">Routing and URL paths</span></span>

<span data-ttu-id="6b1e7-313">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Özniteliği bir HTTP GET isteğine yanıt vermeden bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="6b1e7-314">Her yöntem için URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="6b1e7-315">Denetleyicinin şablonu dizesi ile başlayıp `Route` özniteliği:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="6b1e7-316">Değiştirin `[controller]` denetleyicinin adı ile kural tarafından olduğu "Controller" soneki eksi denetleyici sınıfı adı.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="6b1e7-317">Bu örnek için denetleyici sınıfı adı **todoıtems**denetleyicisidir, bu nedenle denetleyicinin adı "todoıtems" olur.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="6b1e7-318">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="6b1e7-319">Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]`), yola ekleyin. `[HttpGet]`</span><span class="sxs-lookup"><span data-stu-id="6b1e7-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="6b1e7-320">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-320">This sample doesn't use a template.</span></span> <span data-ttu-id="6b1e7-321">Daha fazla bilgi için [özniteliği Http [eylem] özniteliği ile yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="6b1e7-322">Aşağıdaki `GetTodoItem` yöntemi `"{id}"` yapılacak iş öğesi benzersiz tanımlayıcısı için bir yer tutucu değişkendir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="6b1e7-323">Zaman `GetTodoItem` çağrılır, değerini `"{id}"` yöntemine URL'de sağlanan kendi`id` parametresi.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="6b1e7-324">Döndürülen değerler</span><span class="sxs-lookup"><span data-stu-id="6b1e7-324">Return values</span></span>

<span data-ttu-id="6b1e7-325">Dönüş türünü `GetTodoItems` ve `GetTodoItem` yöntemler [actionresult öğesini\<T > türü](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="6b1e7-326">ASP.NET Core, nesneyi otomatik olarak serileştiren [JSON](https://www.json.org/) ve yanıt iletisinin gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="6b1e7-327">Yanıt kodu 200 bu dönüş türü için olduğu varsayılırsa işlenmeyen özel durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="6b1e7-328">İşlenmeyen özel durumları 5xx hatalarla karşılaşırsanız çevrilir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="6b1e7-329">`ActionResult` dönüş türleri, geniş HTTP durum kodları temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="6b1e7-330">Örneğin, `GetTodoItem` iki farklı durum değerleri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="6b1e7-331">Öğe istenen kimliği eşleşirse, yöntem bir 404 döndürür [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) hata kodu.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="6b1e7-332">Aksi takdirde yöntem bir JSON yanıt gövdesine 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="6b1e7-333">Döndüren `item` sonuçları bir HTTP 200 yanıtı.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="6b1e7-334">PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-334">The PutTodoItem method</span></span>

<span data-ttu-id="6b1e7-335">`PutTodoItem` Yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="6b1e7-336">`PutTodoItem` benzer `PostTodoItem`, HTTP PUT kullanır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="6b1e7-337">Yanıt [204 (içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="6b1e7-338">HTTP belirtimine göre bir PUT İsteği tüm güncelleştirilmiş varlık yalnızca değişiklikler değil göndermek istemci gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="6b1e7-339">Kısmi güncelleştirmeleri desteklemek için kullanma [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="6b1e7-340">Çağrılırken `PutTodoItem`bir hata alırsanız, veritabanında bir öğe `GET` olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="6b1e7-341">Test PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="6b1e7-342">Bu örnek, uygulama her başlatıldığında yeniden başlatılması gereken bellek içi veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-342">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="6b1e7-343">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="6b1e7-344">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="6b1e7-345">ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="6b1e7-346">Aşağıdaki görüntüde, Postman güncelleştirme gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-346">The following image shows the Postman update:</span></span>

![204 (içerik yok) yanıtı gösteren postman konsol](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="6b1e7-348">DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="6b1e7-349">`DeleteTodoItem` Yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="6b1e7-350">`DeleteTodoItem` Yanıt [204 (içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-350">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="6b1e7-351">Test DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-351">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="6b1e7-352">Postman bir yapılacak iş öğesini silmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-352">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="6b1e7-353">Yöntem kümesine `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-353">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="6b1e7-354">Silmek, örneğin nesnenin URI ayarlayın `https://localhost:5001/api/TodoItems/1`</span><span class="sxs-lookup"><span data-stu-id="6b1e7-354">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="6b1e7-355">Seçin **Gönder**</span><span class="sxs-lookup"><span data-stu-id="6b1e7-355">Select **Send**</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="6b1e7-356">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="6b1e7-356">Call the web API with JavaScript</span></span>

<span data-ttu-id="6b1e7-357">Öğreticiye bakın [: JavaScript](xref:tutorials/web-api-javascript)ile ASP.NET Core Web API 'si çağırın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-357">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6b1e7-358">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-358">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6b1e7-359">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-359">Create a web API project.</span></span>
> * <span data-ttu-id="6b1e7-360">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-360">Add a model class and a database context.</span></span>
> * <span data-ttu-id="6b1e7-361">Bir denetleyici ekleyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-361">Add a controller.</span></span>
> * <span data-ttu-id="6b1e7-362">CRUD yöntemleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-362">Add CRUD methods.</span></span>
> * <span data-ttu-id="6b1e7-363">Yönlendirmeyi Yapılandırma ve URL yolu.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-363">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="6b1e7-364">Dönüş değerleri belirtin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-364">Specify return values.</span></span>
> * <span data-ttu-id="6b1e7-365">Web API'si Postman ile çağırın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-365">Call the web API with Postman.</span></span>
> * <span data-ttu-id="6b1e7-366">JavaScript ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-366">Call the web API with JavaScript.</span></span>

<span data-ttu-id="6b1e7-367">Sonunda, web API'si "Yapılacaklar" öğelerini ilişkisel bir veritabanında depolanan yönetebileceği sahip.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-367">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="6b1e7-368">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="6b1e7-368">Overview</span></span>

<span data-ttu-id="6b1e7-369">Bu öğretici yandaki API oluşturur:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-369">This tutorial creates the following API:</span></span>

|<span data-ttu-id="6b1e7-370">API</span><span class="sxs-lookup"><span data-stu-id="6b1e7-370">API</span></span> | <span data-ttu-id="6b1e7-371">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6b1e7-371">Description</span></span> | <span data-ttu-id="6b1e7-372">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-372">Request body</span></span> | <span data-ttu-id="6b1e7-373">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-373">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="6b1e7-374">/Api/TodoItems al</span><span class="sxs-lookup"><span data-stu-id="6b1e7-374">GET /api/TodoItems</span></span> | <span data-ttu-id="6b1e7-375">Tüm yapılacak iş öğeleri al</span><span class="sxs-lookup"><span data-stu-id="6b1e7-375">Get all to-do items</span></span> | <span data-ttu-id="6b1e7-376">Yok.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-376">None</span></span> | <span data-ttu-id="6b1e7-377">Yapılacaklar öğelerinin bir dizisi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-377">Array of to-do items</span></span>|
|<span data-ttu-id="6b1e7-378">/Api/TodoItems/{id} al</span><span class="sxs-lookup"><span data-stu-id="6b1e7-378">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="6b1e7-379">Bir öğeyi Kimliğine göre Al</span><span class="sxs-lookup"><span data-stu-id="6b1e7-379">Get an item by ID</span></span> | <span data-ttu-id="6b1e7-380">Yok.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-380">None</span></span> | <span data-ttu-id="6b1e7-381">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-381">To-do item</span></span>|
|<span data-ttu-id="6b1e7-382">POST/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="6b1e7-382">POST /api/TodoItems</span></span> | <span data-ttu-id="6b1e7-383">Yeni Öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="6b1e7-383">Add a new item</span></span> | <span data-ttu-id="6b1e7-384">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-384">To-do item</span></span> | <span data-ttu-id="6b1e7-385">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-385">To-do item</span></span> |
|<span data-ttu-id="6b1e7-386">/Api/TodoItems/{id} koy</span><span class="sxs-lookup"><span data-stu-id="6b1e7-386">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="6b1e7-387">Mevcut öğeyi güncelleştirin &nbsp;</span><span class="sxs-lookup"><span data-stu-id="6b1e7-387">Update an existing item &nbsp;</span></span> | <span data-ttu-id="6b1e7-388">Yapılacak iş öğesi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-388">To-do item</span></span> | <span data-ttu-id="6b1e7-389">Yok.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-389">None</span></span> |
|<span data-ttu-id="6b1e7-390">/Api/TodoItems/{id} &nbsp; Sil&nbsp;</span><span class="sxs-lookup"><span data-stu-id="6b1e7-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="6b1e7-391">Öğeyi Sil &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="6b1e7-391">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="6b1e7-392">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="6b1e7-392">None</span></span> | <span data-ttu-id="6b1e7-393">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="6b1e7-393">None</span></span>|

<span data-ttu-id="6b1e7-394">Aşağıdaki diyagramda, bu uygulamanın tasarımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-394">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="6b1e7-400">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="6b1e7-400">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b1e7-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-401">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b1e7-402">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b1e7-402">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6b1e7-403">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-403">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="6b1e7-404">Bir web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="6b1e7-404">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b1e7-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-405">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b1e7-406">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-406">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6b1e7-407">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-407">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="6b1e7-408">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-408">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="6b1e7-409">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 2,2** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-409">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="6b1e7-410">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-410">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="6b1e7-411">**Docker desteğini etkinleştir** **' i seçmeyin** .</span><span class="sxs-lookup"><span data-stu-id="6b1e7-411">**Don't** select **Enable Docker Support**.</span></span>

![VS yeni proje iletişim kutusu](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b1e7-413">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b1e7-413">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b1e7-414">Açık [tümleşik Terminalini](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-414">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6b1e7-415">Dizinleri (`cd`) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-415">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="6b1e7-416">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-416">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="6b1e7-417">Bu komutlar yeni bir Web API projesi oluşturur ve yeni proje klasöründe Visual Studio Code yeni bir örneğini açar.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-417">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="6b1e7-418">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-418">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6b1e7-419">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-419">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6b1e7-420">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-420">Select **File** > **New Solution**.</span></span>

  ![Yeni çözüm macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="6b1e7-422">**.NET Core** > **uygulama** **API 'si** ileri ' **yi**seçin. > ></span><span class="sxs-lookup"><span data-stu-id="6b1e7-422">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="6b1e7-424">İçinde **, yeni ASP.NET Core Web API'sini yapılandırma** iletişim kutusunda varsayılan değerleri kabul **hedef Framework'ü** , \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-424">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="6b1e7-425">Girin *TodoApi* için **proje adı** seçip **Oluştur**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-425">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="6b1e7-427">API'yi test etme</span><span class="sxs-lookup"><span data-stu-id="6b1e7-427">Test the API</span></span>

<span data-ttu-id="6b1e7-428">Proje şablonu oluşturur bir `values` API.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-428">The project template creates a `values` API.</span></span> <span data-ttu-id="6b1e7-429">Çağrı `Get` uygulamayı test etmek için bir tarayıcıdan yöntemi.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-429">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b1e7-430">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-430">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b1e7-431">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-431">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="6b1e7-432">Visual Studio bir tarayıcı ile başlatarak `https://localhost:<port>/api/values`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-432">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="6b1e7-433">IIS Express sertifika güven varsa soran bir iletişim kutusu alırsanız seçin **Evet**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-433">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="6b1e7-434">İçinde **Güvenlik Uyarısı** ardından, görüntülenen iletişim seçin **Evet**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-434">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b1e7-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b1e7-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6b1e7-436">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-436">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="6b1e7-437">Bir tarayıcıda aşağıdaki URL'ye gidin: [ https://localhost:5001/api/values ](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-437">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6b1e7-438">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6b1e7-439">Uygulamayı başlatmak için**hata ayıklamayı Başlat** ' **ı seçin.**  > </span><span class="sxs-lookup"><span data-stu-id="6b1e7-439">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="6b1e7-440">Mac için Visual Studio bir tarayıcı ile başlatarak `https://localhost:<port>`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-440">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="6b1e7-441">HTTP 404 (bulunamadı) hatası döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-441">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="6b1e7-442">Append `/api/values` URL'sine (URL'yi `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-442">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="6b1e7-443">Aşağıdaki JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-443">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="6b1e7-444">Bir model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="6b1e7-444">Add a model class</span></span>

<span data-ttu-id="6b1e7-445">A *modeli* uygulamayı yöneten verilerini temsil eden sınıflar kümesidir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-445">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="6b1e7-446">Tek bir modeldir bu uygulama için `TodoItem` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-446">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b1e7-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-447">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b1e7-448">İçinde **Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-448">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="6b1e7-449">Seçin **ekleme** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-449">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="6b1e7-450">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-450">Name the folder *Models*.</span></span>

* <span data-ttu-id="6b1e7-451">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-451">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="6b1e7-452">Sınıf adı *Todoıtem* seçip **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-452">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="6b1e7-453">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-453">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b1e7-454">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b1e7-454">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b1e7-455">Adlı bir klasör ekleme *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-455">Add a folder named *Models*.</span></span>

* <span data-ttu-id="6b1e7-456">Ekleme bir `TodoItem` sınıfının *modelleri* aşağıdaki kodla klasörü:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-456">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6b1e7-457">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-457">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6b1e7-458">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-458">Right-click the project.</span></span> <span data-ttu-id="6b1e7-459">Seçin **ekleme** > **yeni klasör**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-459">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="6b1e7-460">Klasör adı *modelleri*.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-460">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="6b1e7-462">*Modeller* klasörüne sağ tıklayın ve **yeni dosya** > **Ekle** > **genel** > **boş sınıfı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-462">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="6b1e7-463">Sınıf adı *Todoıtem*ve ardından **yeni**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-463">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="6b1e7-464">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-464">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="6b1e7-465">`Id` Özelliği işlevlerinin bir ilişkisel veritabanında benzersiz anahtar.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-465">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="6b1e7-466">Model sınıfları herhangi bir projede gidip ancak *modelleri* klasörü, kural olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-466">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="6b1e7-467">Veritabanı bağlamı Ekle</span><span class="sxs-lookup"><span data-stu-id="6b1e7-467">Add a database context</span></span>

<span data-ttu-id="6b1e7-468">*Veritabanı bağlamı* koordine eden bir veri modeli için Entity Framework işlevsellik ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-468">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="6b1e7-469">Bu sınıf türetme tarafından oluşturulan `Microsoft.EntityFrameworkCore.DbContext` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-469">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b1e7-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-470">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b1e7-471">Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="6b1e7-472">Sınıf adı *TodoContext* tıklatıp **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-472">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6b1e7-473">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6b1e7-474">Ekleme bir `TodoContext` sınıfının *modelleri* klasör.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-474">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="6b1e7-475">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-475">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="6b1e7-476">Veritabanı bağlamı Kaydet</span><span class="sxs-lookup"><span data-stu-id="6b1e7-476">Register the database context</span></span>

<span data-ttu-id="6b1e7-477">ASP.NET Core DB bağlamı gibi hizmetler ile kaydedilmelidir [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcı.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-477">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6b1e7-478">Kapsayıcı hizmeti denetleyicilerine sağlar.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-478">The container provides the service to controllers.</span></span>

<span data-ttu-id="6b1e7-479">Güncelleştirme *Startup.cs* aşağıdaki vurgulanmış kodu:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-479">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="6b1e7-480">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-480">The preceding code:</span></span>

* <span data-ttu-id="6b1e7-481">Kullanılmayan kaldırır `using` bildirimleri.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-481">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="6b1e7-482">Veritabanı bağlamı DI kapsayıcıya ekler.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-482">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="6b1e7-483">Veritabanı bağlamı bir bellek içi veritabanına kullanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-483">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="6b1e7-484">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="6b1e7-484">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b1e7-485">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-485">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b1e7-486">Sağ *denetleyicileri* klasör.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-486">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="6b1e7-487">**Yeni öğe** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-487">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="6b1e7-488">İçinde **Yeni Öğe Ekle** iletişim kutusunda **API denetleyici sınıfı** şablonu.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-488">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="6b1e7-489">Sınıf adı *TodoController*seçip **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-489">Name the class *TodoController*, and select **Add**.</span></span>

  ![Yeni öğe iletişim denetleyicisiyle seçilen arama kutusu ve web API denetleyicisi Ekle](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6b1e7-491">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-491">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6b1e7-492">İçinde *denetleyicileri* klasör adında bir sınıf oluşturma `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-492">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="6b1e7-493">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-493">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="6b1e7-494">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-494">The preceding code:</span></span>

* <span data-ttu-id="6b1e7-495">Bir API denetleyicisi sınıfı yöntemleri olmadan tanımlar.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-495">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="6b1e7-496">Sınıfı [[Apicontroller]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) özniteliğiyle süsler.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-496">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="6b1e7-497">Bu öznitelik, denetleyicinin web API'si isteklerine yanıt verdiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-497">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="6b1e7-498">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-498">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="6b1e7-499">Veritabanı bağlamı eklemesine DI kullanır (`TodoContext`) içine denetleyici.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-499">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="6b1e7-500">Her bir veritabanı bağlamı kullanılan [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) denetleyici yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-500">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="6b1e7-501">Adlı bir öğe ekler `Item1` veritabanı boşsa veritabanı.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-501">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="6b1e7-502">Her çalıştığında bu kod oluşturucusunun içinde yeni bir HTTP isteği olduğundan.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-502">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="6b1e7-503">Tüm öğeleri silerseniz, oluşturucu oluşturur `Item1` API yöntemi çağrıldığında tekrar başlattığınızda.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-503">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="6b1e7-504">Bu nedenle, gerçekten işe yaradı silme işlemi işe yaramadı gibi görünebilir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-504">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="6b1e7-505">Get yöntemleri ekleyin</span><span class="sxs-lookup"><span data-stu-id="6b1e7-505">Add Get methods</span></span>

<span data-ttu-id="6b1e7-506">Yapılacak iş öğeleri alır bir API sağlamak için aşağıdaki yöntemi ekleyin. `TodoController` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-506">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="6b1e7-507">İki GET uç noktası bu yöntemleri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-507">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="6b1e7-508">Hala çalışıyorsa uygulamayı durdurun.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-508">Stop the app if it's still running.</span></span> <span data-ttu-id="6b1e7-509">Ardından, en son değişiklikleri dahil etmek için yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-509">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="6b1e7-510">Bir tarayıcıdan iki uç nokta çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-510">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="6b1e7-511">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-511">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="6b1e7-512">Şu HTTP yanıtı çağrısı tarafından üretilen `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-512">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="6b1e7-513">URL Yönlendirme ve yolları</span><span class="sxs-lookup"><span data-stu-id="6b1e7-513">Routing and URL paths</span></span>

<span data-ttu-id="6b1e7-514">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Özniteliği bir HTTP GET isteğine yanıt vermeden bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-514">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="6b1e7-515">Her yöntem için URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-515">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="6b1e7-516">Denetleyicinin şablonu dizesi ile başlayıp `Route` özniteliği:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-516">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="6b1e7-517">Değiştirin `[controller]` denetleyicinin adı ile kural tarafından olduğu "Controller" soneki eksi denetleyici sınıfı adı.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-517">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="6b1e7-518">Bu örnek, denetleyici sınıfı adı olan **Todo**Denetleyici adı "todo" Bu nedenle denetleyicisi.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-518">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="6b1e7-519">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-519">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="6b1e7-520">Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]`), yola ekleyin. `[HttpGet]`</span><span class="sxs-lookup"><span data-stu-id="6b1e7-520">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="6b1e7-521">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-521">This sample doesn't use a template.</span></span> <span data-ttu-id="6b1e7-522">Daha fazla bilgi için [özniteliği Http [eylem] özniteliği ile yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-522">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="6b1e7-523">Aşağıdaki `GetTodoItem` yöntemi `"{id}"` yapılacak iş öğesi benzersiz tanımlayıcısı için bir yer tutucu değişkendir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-523">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="6b1e7-524">Zaman `GetTodoItem` çağrılır, değerini `"{id}"` yöntemine URL'de sağlanan kendi`id` parametresi.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-524">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="6b1e7-525">Döndürülen değerler</span><span class="sxs-lookup"><span data-stu-id="6b1e7-525">Return values</span></span>

<span data-ttu-id="6b1e7-526">Dönüş türünü `GetTodoItems` ve `GetTodoItem` yöntemler [actionresult öğesini\<T > türü](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-526">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="6b1e7-527">ASP.NET Core, nesneyi otomatik olarak serileştiren [JSON](https://www.json.org/) ve yanıt iletisinin gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-527">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="6b1e7-528">Yanıt kodu 200 bu dönüş türü için olduğu varsayılırsa işlenmeyen özel durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-528">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="6b1e7-529">İşlenmeyen özel durumları 5xx hatalarla karşılaşırsanız çevrilir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-529">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="6b1e7-530">`ActionResult` dönüş türleri, geniş HTTP durum kodları temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-530">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="6b1e7-531">Örneğin, `GetTodoItem` iki farklı durum değerleri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-531">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="6b1e7-532">Öğe istenen kimliği eşleşirse, yöntem bir 404 döndürür [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) hata kodu.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-532">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="6b1e7-533">Aksi takdirde yöntem bir JSON yanıt gövdesine 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-533">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="6b1e7-534">Döndüren `item` sonuçları bir HTTP 200 yanıtı.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-534">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="6b1e7-535">Test GetTodoItems yöntemi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-535">Test the GetTodoItems method</span></span>

<span data-ttu-id="6b1e7-536">Bu öğreticide Postman web API'si test etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-536">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="6b1e7-537">Yükleme [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="6b1e7-537">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="6b1e7-538">Web uygulaması başlatın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-538">Start the web app.</span></span>
* <span data-ttu-id="6b1e7-539">Postman'i başlatın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-539">Start Postman.</span></span>
* <span data-ttu-id="6b1e7-540">Devre dışı **SSL sertifika doğrulama**</span><span class="sxs-lookup"><span data-stu-id="6b1e7-540">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b1e7-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-541">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b1e7-542">**Dosya** ayarlarından (Genel sekmesinden) SSL sertifikası doğrulamasını devre dışı bırakın. ></span><span class="sxs-lookup"><span data-stu-id="6b1e7-542">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6b1e7-543">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b1e7-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6b1e7-544">**Postman** > **tercihleri** ' nden (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-544">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="6b1e7-545">Alternatif olarak, wranı seçin ve **Ayarlar**' ı seçip SSL sertifikası doğrulamasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-545">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="6b1e7-546">Test denetleyicisi sonra SSL sertifika doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-546">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="6b1e7-547">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-547">Create a new request.</span></span>
  * <span data-ttu-id="6b1e7-548">HTTP yöntemi kümesine **alma**.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-548">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="6b1e7-549">İstek URL'si kümesine `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-549">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="6b1e7-550">Örneğin: `https://localhost:5001/api/todo`</span><span class="sxs-lookup"><span data-stu-id="6b1e7-550">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="6b1e7-551">Ayarlama **iki bölme görünümü** postman'deki.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-551">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="6b1e7-552">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-552">Select **Send**.</span></span>

![Get isteğiyle postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="6b1e7-554">Create yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="6b1e7-554">Add a Create method</span></span>

<span data-ttu-id="6b1e7-555">`PostTodoItem` *Controllers/TodoController. cs*içindeki şu yöntemi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-555">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="6b1e7-556">Yukarıdaki kod tarafından belirtildiği gibi bir HTTP POST yöntemi olup [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) özniteliği.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-556">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="6b1e7-557">Yöntemi, HTTP isteği gövdesinden Yapılacaklar öğenin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-557">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="6b1e7-558">`CreatedAtAction` Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-558">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="6b1e7-559">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-559">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="6b1e7-560">HTTP 201 sunucuda yeni bir kaynak oluşturan bir HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="6b1e7-561">Yanıta bir `Location` üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-561">Adds a `Location` header to the response.</span></span> <span data-ttu-id="6b1e7-562">`Location` Üst bilgi, yeni oluşturulan Yapılacaklar öğesinin URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-562">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="6b1e7-563">Daha fazla bilgi için [10.2.2 201 oluşturuldu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="6b1e7-564">`Location` Üstbilginin URI 'sini oluşturma eylemine başvurur. `GetTodoItem`</span><span class="sxs-lookup"><span data-stu-id="6b1e7-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="6b1e7-565">Anahtar sözcüğü, `CreatedAtAction` çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır. C# `nameof`</span><span class="sxs-lookup"><span data-stu-id="6b1e7-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="6b1e7-566">Test PostTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-566">Test the PostTodoItem method</span></span>

* <span data-ttu-id="6b1e7-567">Projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-567">Build the project.</span></span>
* <span data-ttu-id="6b1e7-568">Postman HTTP yöntemi kümesine `POST`.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-568">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="6b1e7-569">Seçin **gövdesi** sekmesi.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-569">Select the **Body** tab.</span></span>
* <span data-ttu-id="6b1e7-570">Seçin **ham** radyo düğmesi.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-570">Select the **raw** radio button.</span></span>
* <span data-ttu-id="6b1e7-571">Tür kümesine **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="6b1e7-571">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="6b1e7-572">İstek gövdesinde bir yapılacak iş öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-572">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="6b1e7-573">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-573">Select **Send**.</span></span>

  ![Postman ile isteği oluştur](first-web-api/_static/create.png)

  <span data-ttu-id="6b1e7-575">Bir 405 yöntemine izin verilmiyor hatası alırsanız, `PostTodoItem` Yöntem eklendikten sonra projeyi derlememe sonucu büyük olasılıkla oluşur.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-575">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="6b1e7-576">Konum üst bilgisi URI test</span><span class="sxs-lookup"><span data-stu-id="6b1e7-576">Test the location header URI</span></span>

* <span data-ttu-id="6b1e7-577">Seçin **üstbilgileri** sekmesinde **yanıt** bölmesi.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-577">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="6b1e7-578">Kopyalama **konumu** üst bilgi değeri:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-578">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üst bilgiler sekmesi](first-web-api/_static/pmc2.png)

* <span data-ttu-id="6b1e7-580">Yöntemini GET öğesine Ayarla.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-580">Set the method to GET.</span></span>
* <span data-ttu-id="6b1e7-581">URİ'sini yapıştırın (örneğin, `https://localhost:5001/api/Todo/2`)</span><span class="sxs-lookup"><span data-stu-id="6b1e7-581">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="6b1e7-582">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-582">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="6b1e7-583">PutTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="6b1e7-583">Add a PutTodoItem method</span></span>

<span data-ttu-id="6b1e7-584">Aşağıdaki `PutTodoItem` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-584">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="6b1e7-585">`PutTodoItem` benzer `PostTodoItem`, HTTP PUT kullanır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-585">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="6b1e7-586">Yanıt [204 (içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-586">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="6b1e7-587">HTTP belirtimine göre bir PUT İsteği tüm güncelleştirilmiş varlık yalnızca değişiklikler değil göndermek istemci gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-587">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="6b1e7-588">Kısmi güncelleştirmeleri desteklemek için kullanma [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-588">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="6b1e7-589">Çağrılırken `PutTodoItem`bir hata alırsanız, veritabanında bir öğe `GET` olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-589">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="6b1e7-590">Test PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-590">Test the PutTodoItem method</span></span>

<span data-ttu-id="6b1e7-591">Bu örnek, uygulama her başlatıldığında yeniden başlatılması gereken bellek içi veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-591">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="6b1e7-592">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-592">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="6b1e7-593">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-593">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="6b1e7-594">Kimliğine sahip bir yapılacak iş öğesi güncelleştirme = 1 ve "balık akış için" adını girin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-594">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="6b1e7-595">Aşağıdaki görüntüde, Postman güncelleştirme gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-595">The following image shows the Postman update:</span></span>

![204 (içerik yok) yanıtı gösteren postman konsol](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="6b1e7-597">DeleteTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="6b1e7-597">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="6b1e7-598">Aşağıdaki `DeleteTodoItem` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-598">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="6b1e7-599">`DeleteTodoItem` Yanıt [204 (içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-599">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="6b1e7-600">Test DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="6b1e7-600">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="6b1e7-601">Postman bir yapılacak iş öğesini silmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-601">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="6b1e7-602">Yöntem kümesine `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-602">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="6b1e7-603">Silmek, örneğin nesnenin URI ayarlayın `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="6b1e7-603">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="6b1e7-604">Seçin **Gönder**</span><span class="sxs-lookup"><span data-stu-id="6b1e7-604">Select **Send**</span></span>

<span data-ttu-id="6b1e7-605">Örnek uygulama, tüm öğeleri silmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-605">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="6b1e7-606">Ancak, son öğe silindiğinde, API 'nin bir sonraki çağrılışında model sınıfı Oluşturucu tarafından yeni bir tane oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-606">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="6b1e7-607">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="6b1e7-607">Call the web API with JavaScript</span></span>

<span data-ttu-id="6b1e7-608">Bu bölümde, Web API 'sini çağırmak için JavaScript kullanan bir HTML sayfası eklenir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-608">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="6b1e7-609">Fetch API 'SI isteği başlatır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-609">The Fetch API initiates the request.</span></span> <span data-ttu-id="6b1e7-610">JavaScript, sayfayı Web API 'sinin yanıtından alınan ayrıntılarla güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-610">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="6b1e7-611">Uygulamayı [statik dosyalara sunacak](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) şekilde yapılandırın ve aşağıdaki vurgulanmış kodla *Startup.cs* güncelleştirerek [varsayılan dosya eşlemesini etkinleştirin](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) :</span><span class="sxs-lookup"><span data-stu-id="6b1e7-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="6b1e7-612">Oluşturma bir *wwwroot* proje dizininde klasör.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="6b1e7-613">Adlı bir HTML dosyası ekleyin *index.html* için *wwwroot* dizin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="6b1e7-614">Dosyanın içeriğini aşağıdaki biçimlendirme ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="6b1e7-615">Adlı bir JavaScript dosyası ekleyin *site.js* için *wwwroot* dizin.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="6b1e7-616">Dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="6b1e7-617">ASP.NET Core proje başlatma ayarlarında bir değişiklik HTML sayfasını yerel olarak test etmek için gerekli:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="6b1e7-618">Açık *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="6b1e7-619">Kaldırma `launchUrl` , açmak için uygulamayı zorlamak için özellik *index.html*&mdash;projenin varsayılan dosya.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="6b1e7-620">Bu örnek, Web API 'sinin tüm CRUD yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-620">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="6b1e7-621">API çağrıları açıklamaları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-621">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="6b1e7-622">Yapılacaklar öğelerinin bir listesini alın</span><span class="sxs-lookup"><span data-stu-id="6b1e7-622">Get a list of to-do items</span></span>

<span data-ttu-id="6b1e7-623">Fetch, Web API 'sine bir HTTP GET isteği gönderir ve bu, Yapılacaklar öğeleri dizisini temsil eden JSON döndürür.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-623">Fetch sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="6b1e7-624">`success` İstek başarılı olursa geri çağırma işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-624">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="6b1e7-625">Geri çağırma içinde DOM Yapılacaklar bilgilerle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-625">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="6b1e7-626">Yapılacak İş Öğesi Ekle</span><span class="sxs-lookup"><span data-stu-id="6b1e7-626">Add a to-do item</span></span>

<span data-ttu-id="6b1e7-627">Fetch, istek gövdesinde Yapılacaklar öğesiyle bir HTTP POST isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-627">Fetch sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="6b1e7-628">`accepts` Ve `contentType` seçeneklerini ayarlamak `application/json` gönderilen ve alınan medya türü belirtmek için.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-628">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="6b1e7-629">Yapılacak iş öğesi kullanarak JSON'a dönüştürülür [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-629">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="6b1e7-630">API'yi bir başarılı durum kodu döndürdüğünde `getData` işlevi HTML tablosu güncelleştirmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-630">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="6b1e7-631">Yapılacak iş öğesini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="6b1e7-631">Update a to-do item</span></span>

<span data-ttu-id="6b1e7-632">Yapılacak iş öğesi güncelleştirilirken bir eklemeye benzerdir.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-632">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="6b1e7-633">`url` Öğenin benzersiz tanıtıcısı eklemek için değişiklikleri ve `type` olduğu `PUT`.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-633">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="6b1e7-634">Yapılacak iş öğesi silme</span><span class="sxs-lookup"><span data-stu-id="6b1e7-634">Delete a to-do item</span></span>

<span data-ttu-id="6b1e7-635">Yapılacak iş öğesi silme gerçekleştirilir ayarlayarak `type` AJAX çağrısı hedefi üzerinde `DELETE` ve öğenin benzersiz tanıtıcısı URL'yi belirterek.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-635">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="6b1e7-636">Web API 'sine kimlik doğrulama desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="6b1e7-636">Add authentication support to a web API</span></span>

<span data-ttu-id="6b1e7-637">Bkz. [ıdentityserver4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) öğreticisi.</span><span class="sxs-lookup"><span data-stu-id="6b1e7-637">See the [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6b1e7-638">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6b1e7-638">Additional resources</span></span>

<span data-ttu-id="6b1e7-639">[Görüntülemek veya Bu öğretici için örnek kodu indirdikten](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="6b1e7-640">Bkz: [nasıl indirileceğini](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="6b1e7-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="6b1e7-641">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="6b1e7-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="6b1e7-642">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="6b1e7-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
