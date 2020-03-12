---
title: MongoDB ile ASP.NET Core ile web API'si oluşturma
author: prkhandelwal
description: Bu öğreticide, MongoDB NoSQL veritabanı kullanarak ASP.NET Core Web API 'sinin nasıl oluşturulacağı gösterilmektedir.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 0e7881aa93953866c7a90eb62de64c4c000a866c
ms.sourcegitcommit: 40dc9b00131985abcd99bd567647420d798e798a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78935449"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="6837a-103">MongoDB ile ASP.NET Core ile web API'si oluşturma</span><span class="sxs-lookup"><span data-stu-id="6837a-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="6837a-104">By [pratik Khandelwal](https://twitter.com/K2Prk) ve [Scott Ade](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="6837a-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6837a-105">Bu öğretici, bir [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında oluşturma, okuma, güncelleştirme ve SILME (CRUD) işlemlerini gerçekleştiren BIR Web API 'si oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6837a-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="6837a-106">Bu öğreticide şunların nasıl yapıladığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="6837a-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6837a-107">MongoDB yapılandırın</span><span class="sxs-lookup"><span data-stu-id="6837a-107">Configure MongoDB</span></span>
> * <span data-ttu-id="6837a-108">MongoDB veritabanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="6837a-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="6837a-109">MongoDB koleksiyonu ve şema tanımlayın</span><span class="sxs-lookup"><span data-stu-id="6837a-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="6837a-110">Bir web API'sini MongoDB CRUD işlemleri gerçekleştirme</span><span class="sxs-lookup"><span data-stu-id="6837a-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="6837a-111">JSON serileştirmesini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="6837a-111">Customize JSON serialization</span></span>

<span data-ttu-id="6837a-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6837a-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6837a-113">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="6837a-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6837a-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6837a-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="6837a-115">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6837a-115">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="6837a-116">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="6837a-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="6837a-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6837a-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6837a-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6837a-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="6837a-119">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6837a-119">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="6837a-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6837a-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="6837a-121">Visual Studio Code için C#</span><span class="sxs-lookup"><span data-stu-id="6837a-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="6837a-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6837a-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6837a-123">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6837a-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="6837a-124">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6837a-124">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="6837a-125">Mac için Visual Studio sürüm 7,7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6837a-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="6837a-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6837a-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="6837a-127">MongoDB yapılandırın</span><span class="sxs-lookup"><span data-stu-id="6837a-127">Configure MongoDB</span></span>

<span data-ttu-id="6837a-128">Windows kullanıyorsanız MongoDB, varsayılan olarak *mongodb\\C:\\Program Files* 'a yüklenir.</span><span class="sxs-lookup"><span data-stu-id="6837a-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="6837a-129">*\\Program Files\\MongoDB\\Server\\* \<Version_Number >\\`Path`) ortam değişkenine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6837a-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="6837a-130">Bu değişiklik yerden MongoDB erişim sağlar, geliştirme makinenizde.</span><span class="sxs-lookup"><span data-stu-id="6837a-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="6837a-131">Mongo kabuğunu veritabanı oluşturma, koleksiyonları yapın ve belgeleri depolamak için aşağıdaki adımları kullanın.</span><span class="sxs-lookup"><span data-stu-id="6837a-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="6837a-132">Mongo kabuğu komutları hakkında daha fazla bilgi için bkz. [Mongo kabuğu Ile çalışma](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="6837a-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="6837a-133">Geliştirme makinenizde verilerin depolanması için bir dizin seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="6837a-134">Örneğin, *C: Windows üzerinde\\BooksData* .</span><span class="sxs-lookup"><span data-stu-id="6837a-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="6837a-135">Yoksa dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6837a-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="6837a-136">Mongo kabuğunu yeni dizinleri oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="6837a-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="6837a-137">Bir komut kabuğunu açın.</span><span class="sxs-lookup"><span data-stu-id="6837a-137">Open a command shell.</span></span> <span data-ttu-id="6837a-138">Varsayılan bağlantı noktası 27017 mongodb'ye bağlanmak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6837a-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="6837a-139">`<data_directory_path>`, önceki adımda seçtiğiniz dizinle değiştirmeyi unutmayın.</span><span class="sxs-lookup"><span data-stu-id="6837a-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="6837a-140">Başka bir komut kabuğu örneği açın.</span><span class="sxs-lookup"><span data-stu-id="6837a-140">Open another command shell instance.</span></span> <span data-ttu-id="6837a-141">Aşağıdaki komutu çalıştırarak varsayılan test veritabanı'na bağlanma:</span><span class="sxs-lookup"><span data-stu-id="6837a-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="6837a-142">Bir komut kabuğu'nda aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6837a-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="6837a-143">Zaten mevcut değilse, *BookstoreDb* adlı bir veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6837a-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="6837a-144">Veritabanı mevcut değilse, bağlantı işlemleri için açılır.</span><span class="sxs-lookup"><span data-stu-id="6837a-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="6837a-145">Aşağıdaki komutu kullanarak bir `Books` koleksiyonu oluşturun:</span><span class="sxs-lookup"><span data-stu-id="6837a-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="6837a-146">Aşağıdaki sonucu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6837a-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="6837a-147">`Books` koleksiyonu için bir şema tanımlayın ve aşağıdaki komutu kullanarak iki belge ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="6837a-148">Aşağıdaki sonucu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6837a-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="6837a-149">Bu makalede gösterilen KIMLIK, bu örneği çalıştırdığınızda kimliklerle eşleşmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="6837a-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="6837a-150">Aşağıdaki komutu kullanarak veritabanında belgelerini görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="6837a-151">Aşağıdaki sonucu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6837a-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="6837a-152">Şema her belge için `ObjectId` türünde bir otomatik olarak `_id` özelliği ekler.</span><span class="sxs-lookup"><span data-stu-id="6837a-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="6837a-153">Veritabanı hazırdır.</span><span class="sxs-lookup"><span data-stu-id="6837a-153">The database is ready.</span></span> <span data-ttu-id="6837a-154">ASP.NET Core web API'si oluşturmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6837a-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="6837a-155">ASP.NET Core web API projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="6837a-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6837a-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6837a-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6837a-157">**Dosya** > **Yeni** > **projesi**' ne gidin.</span><span class="sxs-lookup"><span data-stu-id="6837a-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="6837a-158">**ASP.NET Core Web uygulaması** proje türünü seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="6837a-159">Projeyi *Booksapı*olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="6837a-160">**.NET Core** hedef çerçevesini ve **3,0 ASP.NET Core**seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="6837a-161">**API** proje şablonunu seçin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="6837a-162">MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) .</span><span class="sxs-lookup"><span data-stu-id="6837a-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="6837a-163">**Paket Yöneticisi konsol** penceresinde, proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="6837a-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="6837a-164">MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6837a-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="6837a-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6837a-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6837a-166">Bir komut kabuğu'nda aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6837a-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="6837a-167">.NET Core'u hedefleyen yeni bir ASP.NET Core web API projesi oluşturulur ve Visual Studio Code'da açılır.</span><span class="sxs-lookup"><span data-stu-id="6837a-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="6837a-168">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' booksapı ' içinde eksik bir iletişim kutusu yok. Bunları ekleyin mi?** .</span><span class="sxs-lookup"><span data-stu-id="6837a-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="6837a-169">**Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-169">Select **Yes**.</span></span>
1. <span data-ttu-id="6837a-170">MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) .</span><span class="sxs-lookup"><span data-stu-id="6837a-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="6837a-171">**Tümleşik Terminal** ' i açın ve proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="6837a-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="6837a-172">MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6837a-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6837a-173">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6837a-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6837a-174">> **yeni çözüm** > **.NET Core** > **App** **dosyasına** gidin.</span><span class="sxs-lookup"><span data-stu-id="6837a-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="6837a-175">**ASP.NET Core Web API** C# proje şablonunu seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="6837a-176">**Hedef çerçeve** açılır listesinden **.NET Core 3,0** ' i seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="6837a-177">**Proje adı**Için *booksapı* girin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="6837a-178">**Çözüm** panelinde, projenin **Bağımlılıklar** düğümünü sağ tıklatın ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="6837a-179">Arama kutusuna *MongoDB. Driver* girin, *MongoDB. Driver* paketini seçin ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="6837a-180">**Lisans kabulü** Iletişim kutusunda **kabul et** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="6837a-181">Varlık modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="6837a-181">Add an entity model</span></span>

1. <span data-ttu-id="6837a-182">Proje köküne *modeller* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6837a-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="6837a-183">*Modeller* dizinine aşağıdaki kodla bir `Book` sınıfı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="6837a-184">Önceki sınıfta `Id` özelliği:</span><span class="sxs-lookup"><span data-stu-id="6837a-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="6837a-185">Ortak dil çalışma zamanı (CLR) nesnesini MongoDB koleksiyonuna eşlemek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="6837a-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="6837a-186">, Bu özelliği belgenin birincil anahtarı olarak belirlemek için [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) ile açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="6837a-186">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="6837a-187">, Parametreyi [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) yapısı yerine tür `string` olarak geçirmeyi sağlayan [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) ile açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="6837a-187">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="6837a-188">Mongo, `string` dönüştürmeyi `ObjectId`olarak işler.</span><span class="sxs-lookup"><span data-stu-id="6837a-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="6837a-189">`BookName` özelliğine [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) özniteliğiyle açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="6837a-189">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="6837a-190">Özniteliğin `Name` değeri, MongoDB koleksiyonundaki özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6837a-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="6837a-191">Yapılandırma modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="6837a-191">Add a configuration model</span></span>

1. <span data-ttu-id="6837a-192">Aşağıdaki veritabanı yapılandırma değerlerini *appSettings. JSON*öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="6837a-193">*Modeller* dizinine aşağıdaki kodla bir *BookstoreDatabaseSettings.cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="6837a-194">Yukarıdaki `BookstoreDatabaseSettings` sınıfı, *appSettings. JSON* dosyasının `BookstoreDatabaseSettings` özellik değerlerini depolamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6837a-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="6837a-195">JSON ve C# Özellik adları, eşleme sürecini kolaylaştırmak için aynı şekilde adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="6837a-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="6837a-196">Aşağıdaki Vurgulanan kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="6837a-197">Önceki kodda:</span><span class="sxs-lookup"><span data-stu-id="6837a-197">In the preceding code:</span></span>

   * <span data-ttu-id="6837a-198">*AppSettings. JSON* dosyasının `BookstoreDatabaseSettings` bölüm bağlandığı yapılandırma örneği, bağımlılık ekleme (dı) kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6837a-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="6837a-199">Örneğin, bir `BookstoreDatabaseSettings` nesnesinin `ConnectionString` özelliği *appSettings. JSON*içindeki `BookstoreDatabaseSettings:ConnectionString` özelliği ile doldurulur.</span><span class="sxs-lookup"><span data-stu-id="6837a-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="6837a-200">`IBookstoreDatabaseSettings` arabirimi, tek bir [hizmet ömrü](xref:fundamentals/dependency-injection#service-lifetimes)ile dı 'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6837a-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="6837a-201">Eklerken, arabirim örneği bir `BookstoreDatabaseSettings` nesnesine çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="6837a-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="6837a-202">`BookstoreDatabaseSettings` ve `IBookstoreDatabaseSettings` başvurularını çözümlemek için aşağıdaki kodu en *üst kısmına ekleyin* :</span><span class="sxs-lookup"><span data-stu-id="6837a-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="6837a-203">CRUD işlemleri hizmeti ekleme</span><span class="sxs-lookup"><span data-stu-id="6837a-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="6837a-204">Proje köküne bir *Hizmetler* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6837a-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="6837a-205">*Hizmetler* dizinine aşağıdaki kodla bir `BookService` sınıfı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="6837a-206">Yukarıdaki kodda, Oluşturucu ekleme yoluyla dı 'den bir `IBookstoreDatabaseSettings` örneği alınır.</span><span class="sxs-lookup"><span data-stu-id="6837a-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="6837a-207">Bu teknik, [yapılandırma modeli ekleme](#add-a-configuration-model) bölümüne eklenen *appSettings. JSON* yapılandırma değerlerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="6837a-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="6837a-208">Aşağıdaki Vurgulanan kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="6837a-209">Önceki kodda, `BookService` sınıfı, tüketen sınıflarda Oluşturucu ekleme işlemini desteklemek için DI ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6837a-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="6837a-210">`BookService`, `MongoClient`doğrudan bağımlılığı sağladığından, tek hizmet ömrü en uygundur.</span><span class="sxs-lookup"><span data-stu-id="6837a-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="6837a-211">Resmi [Mongo istemci yeniden kullanım yönergeleri](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)temelinde `MongoClient`, tek bir hizmet ömrü ile birlikte kaydedilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6837a-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="6837a-212">`BookService` başvurusunu çözümlemek için aşağıdaki kodu *Startup.cs* 'un en üstüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="6837a-213">`BookService` sınıfı, veritabanında CRUD işlemleri gerçekleştirmek için aşağıdaki `MongoDB.Driver` üyeleri kullanır:</span><span class="sxs-lookup"><span data-stu-id="6837a-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="6837a-214">[Mongoclient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash;, veritabanı işlemlerini gerçekleştirmek için sunucu örneğini okur.</span><span class="sxs-lookup"><span data-stu-id="6837a-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="6837a-215">Bu sınıfın oluşturucusu, MongoDB bağlantı dizesini sağlanır:</span><span class="sxs-lookup"><span data-stu-id="6837a-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="6837a-216">[Imongodatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; işlemleri gerçekleştirmek Için Mongo veritabanını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6837a-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="6837a-217">Bu öğretici, belirli bir koleksiyondaki verilere erişim kazanmak için arabirimindeki genel [GetCollection\<TDocument > (koleksiyon)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="6837a-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="6837a-218">Bu yöntem çağrıldıktan sonra, koleksiyonda CRUD işlemleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="6837a-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="6837a-219">`GetCollection<TDocument>(collection)` Yöntem çağrısında:</span><span class="sxs-lookup"><span data-stu-id="6837a-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="6837a-220">`collection`, koleksiyon adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6837a-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="6837a-221">`TDocument`, koleksiyonda depolanan CLR nesne türünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6837a-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="6837a-222">`GetCollection<TDocument>(collection)`, koleksiyonu temsil eden bir [Mongocollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) nesnesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="6837a-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="6837a-223">Bu öğreticide, aşağıdaki yöntemlerden koleksiyonunda çağrılır:</span><span class="sxs-lookup"><span data-stu-id="6837a-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="6837a-224">[Deleteone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash;, belirtilen arama ölçütleriyle eşleşen tek bir belgeyi siler.</span><span class="sxs-lookup"><span data-stu-id="6837a-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="6837a-225">[\<TDocument > bul](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash;, koleksiyonda belirtilen arama ölçütleriyle eşleşen tüm belgeleri döndürür.</span><span class="sxs-lookup"><span data-stu-id="6837a-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="6837a-226">[Insertone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash;, belirtilen nesneyi koleksiyondaki yeni bir belge olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="6837a-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="6837a-227">[Replaceone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash;, belirtilen arama ölçütleriyle eşleşen tek belgeyi, belirtilen nesneyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6837a-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="6837a-228">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="6837a-228">Add a controller</span></span>

<span data-ttu-id="6837a-229">*Controllers* dizinine aşağıdaki kodla bir `BooksController` sınıfı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="6837a-230">Önceki web API denetleyicisi:</span><span class="sxs-lookup"><span data-stu-id="6837a-230">The preceding web API controller:</span></span>

* <span data-ttu-id="6837a-231">CRUD işlemleri gerçekleştirmek için `BookService` sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="6837a-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="6837a-232">GET, POST, PUT ve DELETE HTTP isteklerini desteklemek için eylem yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="6837a-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="6837a-233">Bir [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) yanıtı döndürmek için `Create` eylemi yönteminde <xref:System.Web.Http.ApiController.CreatedAtRoute*> çağırır.</span><span class="sxs-lookup"><span data-stu-id="6837a-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="6837a-234">Durum kodu 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="6837a-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="6837a-235">`CreatedAtRoute` Ayrıca yanıta bir `Location` üst bilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="6837a-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="6837a-236">`Location` üstbilgisi yeni oluşturulan kitabın URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="6837a-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="6837a-237">Web API 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="6837a-237">Test the web API</span></span>

1. <span data-ttu-id="6837a-238">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6837a-238">Build and run the app.</span></span>

1. <span data-ttu-id="6837a-239">Denetleyicinin parametresiz `Get` eylem yöntemini sınamak için `http://localhost:<port>/api/books` gidin.</span><span class="sxs-lookup"><span data-stu-id="6837a-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="6837a-240">Aşağıdaki JSON yanıtı gösterilir:</span><span class="sxs-lookup"><span data-stu-id="6837a-240">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="6837a-241">Denetleyicinin aşırı yüklenmiş `Get` eylem yöntemini sınamak için `http://localhost:<port>/api/books/{id here}` gidin.</span><span class="sxs-lookup"><span data-stu-id="6837a-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="6837a-242">Aşağıdaki JSON yanıtı gösterilir:</span><span class="sxs-lookup"><span data-stu-id="6837a-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="6837a-243">JSON serileştirme seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6837a-243">Configure JSON serialization options</span></span>

<span data-ttu-id="6837a-244">[Web API 'Sini test](#test-the-web-api) etme bölümünde döndürülen JSON yanıtları hakkında iki ayrıntı vardır:</span><span class="sxs-lookup"><span data-stu-id="6837a-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="6837a-245">' Varsayılan ortası büyük/küçük harf özelliği, CLR nesnesinin özellik adlarının Pascal büyük küçük harfleriyle eşleşecek şekilde değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6837a-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="6837a-246">`bookName` özelliği `Name`olarak döndürülmelidir.</span><span class="sxs-lookup"><span data-stu-id="6837a-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="6837a-247">Önceki gereksinimleri karşılamak için aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="6837a-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="6837a-248">JSON.NET, ASP.NET paylaşılan çerçevesinden kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="6837a-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="6837a-249">[Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6837a-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="6837a-250">`Startup.ConfigureServices`' de, aşağıdaki vurgulanmış kodu `AddControllers` yöntemi çağrısına zincirle:</span><span class="sxs-lookup"><span data-stu-id="6837a-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="6837a-251">Önceki değişiklik ile, Web API 'sinin seri hale getirilmiş JSON yanıtındaki Özellik adları CLR nesne türündeki ilgili özellik adlarıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="6837a-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="6837a-252">Örneğin, `Book` sınıfının `Author` özelliği `Author`olarak serileştirir.</span><span class="sxs-lookup"><span data-stu-id="6837a-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="6837a-253">*Modeller/Book. cs*' de, `BookName` özelliğine aşağıdaki [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) özniteliğiyle açıklama ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-253">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="6837a-254">`[JsonProperty]` özniteliğinin değeri `Name`, Web API 'sinin serileştirilmiş JSON yanıtında özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6837a-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="6837a-255">`[JsonProperty]` öznitelik başvurusunu çözümlemek için *modeller/Book. cs* ' nin en üstüne aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="6837a-256">[Web API 'Sini test](#test-the-web-api) etme bölümünde tanımlanan adımları yineleyin.</span><span class="sxs-lookup"><span data-stu-id="6837a-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="6837a-257">JSON Özellik adlarındaki farka dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="6837a-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6837a-258">Bu öğretici, bir [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında oluşturma, okuma, güncelleştirme ve SILME (CRUD) işlemlerini gerçekleştiren BIR Web API 'si oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6837a-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="6837a-259">Bu öğreticide şunların nasıl yapıladığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="6837a-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6837a-260">MongoDB yapılandırın</span><span class="sxs-lookup"><span data-stu-id="6837a-260">Configure MongoDB</span></span>
> * <span data-ttu-id="6837a-261">MongoDB veritabanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="6837a-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="6837a-262">MongoDB koleksiyonu ve şema tanımlayın</span><span class="sxs-lookup"><span data-stu-id="6837a-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="6837a-263">Bir web API'sini MongoDB CRUD işlemleri gerçekleştirme</span><span class="sxs-lookup"><span data-stu-id="6837a-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="6837a-264">JSON serileştirmesini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="6837a-264">Customize JSON serialization</span></span>

<span data-ttu-id="6837a-265">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6837a-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6837a-266">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="6837a-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6837a-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6837a-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="6837a-268">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="6837a-268">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="6837a-269">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="6837a-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="6837a-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6837a-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6837a-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6837a-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="6837a-272">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="6837a-272">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="6837a-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6837a-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="6837a-274">Visual Studio Code için C#</span><span class="sxs-lookup"><span data-stu-id="6837a-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="6837a-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6837a-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6837a-276">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6837a-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="6837a-277">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="6837a-277">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="6837a-278">Mac için Visual Studio sürüm 7,7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6837a-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="6837a-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6837a-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="6837a-280">MongoDB yapılandırın</span><span class="sxs-lookup"><span data-stu-id="6837a-280">Configure MongoDB</span></span>

<span data-ttu-id="6837a-281">Windows kullanıyorsanız MongoDB, varsayılan olarak *mongodb\\C:\\Program Files* 'a yüklenir.</span><span class="sxs-lookup"><span data-stu-id="6837a-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="6837a-282">*\\Program Files\\MongoDB\\Server\\* \<Version_Number >\\`Path`) ortam değişkenine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6837a-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="6837a-283">Bu değişiklik yerden MongoDB erişim sağlar, geliştirme makinenizde.</span><span class="sxs-lookup"><span data-stu-id="6837a-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="6837a-284">Mongo kabuğunu veritabanı oluşturma, koleksiyonları yapın ve belgeleri depolamak için aşağıdaki adımları kullanın.</span><span class="sxs-lookup"><span data-stu-id="6837a-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="6837a-285">Mongo kabuğu komutları hakkında daha fazla bilgi için bkz. [Mongo kabuğu Ile çalışma](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="6837a-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="6837a-286">Geliştirme makinenizde verilerin depolanması için bir dizin seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="6837a-287">Örneğin, *C: Windows üzerinde\\BooksData* .</span><span class="sxs-lookup"><span data-stu-id="6837a-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="6837a-288">Yoksa dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6837a-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="6837a-289">Mongo kabuğunu yeni dizinleri oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="6837a-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="6837a-290">Bir komut kabuğunu açın.</span><span class="sxs-lookup"><span data-stu-id="6837a-290">Open a command shell.</span></span> <span data-ttu-id="6837a-291">Varsayılan bağlantı noktası 27017 mongodb'ye bağlanmak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6837a-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="6837a-292">`<data_directory_path>`, önceki adımda seçtiğiniz dizinle değiştirmeyi unutmayın.</span><span class="sxs-lookup"><span data-stu-id="6837a-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="6837a-293">Başka bir komut kabuğu örneği açın.</span><span class="sxs-lookup"><span data-stu-id="6837a-293">Open another command shell instance.</span></span> <span data-ttu-id="6837a-294">Aşağıdaki komutu çalıştırarak varsayılan test veritabanı'na bağlanma:</span><span class="sxs-lookup"><span data-stu-id="6837a-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="6837a-295">Bir komut kabuğu'nda aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6837a-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="6837a-296">Zaten mevcut değilse, *BookstoreDb* adlı bir veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6837a-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="6837a-297">Veritabanı mevcut değilse, bağlantı işlemleri için açılır.</span><span class="sxs-lookup"><span data-stu-id="6837a-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="6837a-298">Aşağıdaki komutu kullanarak bir `Books` koleksiyonu oluşturun:</span><span class="sxs-lookup"><span data-stu-id="6837a-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="6837a-299">Aşağıdaki sonucu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6837a-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="6837a-300">`Books` koleksiyonu için bir şema tanımlayın ve aşağıdaki komutu kullanarak iki belge ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="6837a-301">Aşağıdaki sonucu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6837a-301">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="6837a-302">Bu makalede gösterilen KIMLIK, bu örneği çalıştırdığınızda kimliklerle eşleşmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="6837a-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="6837a-303">Aşağıdaki komutu kullanarak veritabanında belgelerini görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="6837a-304">Aşağıdaki sonucu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6837a-304">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="6837a-305">Şema her belge için `ObjectId` türünde bir otomatik olarak `_id` özelliği ekler.</span><span class="sxs-lookup"><span data-stu-id="6837a-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="6837a-306">Veritabanı hazırdır.</span><span class="sxs-lookup"><span data-stu-id="6837a-306">The database is ready.</span></span> <span data-ttu-id="6837a-307">ASP.NET Core web API'si oluşturmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6837a-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="6837a-308">ASP.NET Core web API projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="6837a-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6837a-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6837a-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6837a-310">**Dosya** > **Yeni** > **projesi**' ne gidin.</span><span class="sxs-lookup"><span data-stu-id="6837a-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="6837a-311">**ASP.NET Core Web uygulaması** proje türünü seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="6837a-312">Projeyi *Booksapı*olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="6837a-313">**.NET Core** hedef çerçevesini ve **2,2 ASP.NET Core**seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="6837a-314">**API** proje şablonunu seçin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="6837a-315">MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) .</span><span class="sxs-lookup"><span data-stu-id="6837a-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="6837a-316">**Paket Yöneticisi konsol** penceresinde, proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="6837a-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="6837a-317">MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6837a-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="6837a-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6837a-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6837a-319">Bir komut kabuğu'nda aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6837a-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="6837a-320">.NET Core'u hedefleyen yeni bir ASP.NET Core web API projesi oluşturulur ve Visual Studio Code'da açılır.</span><span class="sxs-lookup"><span data-stu-id="6837a-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="6837a-321">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' booksapı ' içinde eksik bir iletişim kutusu yok. Bunları ekleyin mi?** .</span><span class="sxs-lookup"><span data-stu-id="6837a-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="6837a-322">**Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-322">Select **Yes**.</span></span>
1. <span data-ttu-id="6837a-323">MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) .</span><span class="sxs-lookup"><span data-stu-id="6837a-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="6837a-324">**Tümleşik Terminal** ' i açın ve proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="6837a-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="6837a-325">MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6837a-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6837a-326">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6837a-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6837a-327">> **yeni çözüm** > **.NET Core** > **App** **dosyasına** gidin.</span><span class="sxs-lookup"><span data-stu-id="6837a-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="6837a-328">**ASP.NET Core Web API** C# proje şablonunu seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="6837a-329">**Hedef çerçeve** açılır listesinden **.NET Core 2,2** ' i seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="6837a-330">**Proje adı**Için *booksapı* girin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="6837a-331">**Çözüm** panelinde, projenin **Bağımlılıklar** düğümünü sağ tıklatın ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="6837a-332">Arama kutusuna *MongoDB. Driver* girin, *MongoDB. Driver* paketini seçin ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="6837a-333">**Lisans kabulü** Iletişim kutusunda **kabul et** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6837a-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="6837a-334">Varlık modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="6837a-334">Add an entity model</span></span>

1. <span data-ttu-id="6837a-335">Proje köküne *modeller* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6837a-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="6837a-336">*Modeller* dizinine aşağıdaki kodla bir `Book` sınıfı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="6837a-337">Önceki sınıfta `Id` özelliği:</span><span class="sxs-lookup"><span data-stu-id="6837a-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="6837a-338">Ortak dil çalışma zamanı (CLR) nesnesini MongoDB koleksiyonuna eşlemek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="6837a-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="6837a-339">, Bu özelliği belgenin birincil anahtarı olarak belirlemek için [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) ile açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="6837a-339">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="6837a-340">, Parametreyi [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) yapısı yerine tür `string` olarak geçirmeyi sağlayan [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) ile açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="6837a-340">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="6837a-341">Mongo, `string` dönüştürmeyi `ObjectId`olarak işler.</span><span class="sxs-lookup"><span data-stu-id="6837a-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="6837a-342">`BookName` özelliğine [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) özniteliğiyle açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="6837a-342">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="6837a-343">Özniteliğin `Name` değeri, MongoDB koleksiyonundaki özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6837a-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="6837a-344">Yapılandırma modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="6837a-344">Add a configuration model</span></span>

1. <span data-ttu-id="6837a-345">Aşağıdaki veritabanı yapılandırma değerlerini *appSettings. JSON*öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="6837a-346">*Modeller* dizinine aşağıdaki kodla bir *BookstoreDatabaseSettings.cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="6837a-347">Yukarıdaki `BookstoreDatabaseSettings` sınıfı, *appSettings. JSON* dosyasının `BookstoreDatabaseSettings` özellik değerlerini depolamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6837a-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="6837a-348">JSON ve C# Özellik adları, eşleme sürecini kolaylaştırmak için aynı şekilde adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="6837a-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="6837a-349">Aşağıdaki Vurgulanan kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="6837a-350">Önceki kodda:</span><span class="sxs-lookup"><span data-stu-id="6837a-350">In the preceding code:</span></span>

   * <span data-ttu-id="6837a-351">*AppSettings. JSON* dosyasının `BookstoreDatabaseSettings` bölüm bağlandığı yapılandırma örneği, bağımlılık ekleme (dı) kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6837a-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="6837a-352">Örneğin, bir `BookstoreDatabaseSettings` nesnesinin `ConnectionString` özelliği *appSettings. JSON*içindeki `BookstoreDatabaseSettings:ConnectionString` özelliği ile doldurulur.</span><span class="sxs-lookup"><span data-stu-id="6837a-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="6837a-353">`IBookstoreDatabaseSettings` arabirimi, tek bir [hizmet ömrü](xref:fundamentals/dependency-injection#service-lifetimes)ile dı 'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6837a-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="6837a-354">Eklerken, arabirim örneği bir `BookstoreDatabaseSettings` nesnesine çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="6837a-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="6837a-355">`BookstoreDatabaseSettings` ve `IBookstoreDatabaseSettings` başvurularını çözümlemek için aşağıdaki kodu en *üst kısmına ekleyin* :</span><span class="sxs-lookup"><span data-stu-id="6837a-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="6837a-356">CRUD işlemleri hizmeti ekleme</span><span class="sxs-lookup"><span data-stu-id="6837a-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="6837a-357">Proje köküne bir *Hizmetler* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6837a-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="6837a-358">*Hizmetler* dizinine aşağıdaki kodla bir `BookService` sınıfı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="6837a-359">Yukarıdaki kodda, Oluşturucu ekleme yoluyla dı 'den bir `IBookstoreDatabaseSettings` örneği alınır.</span><span class="sxs-lookup"><span data-stu-id="6837a-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="6837a-360">Bu teknik, [yapılandırma modeli ekleme](#add-a-configuration-model) bölümüne eklenen *appSettings. JSON* yapılandırma değerlerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="6837a-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="6837a-361">Aşağıdaki Vurgulanan kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="6837a-362">Önceki kodda, `BookService` sınıfı, tüketen sınıflarda Oluşturucu ekleme işlemini desteklemek için DI ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6837a-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="6837a-363">`BookService`, `MongoClient`doğrudan bağımlılığı sağladığından, tek hizmet ömrü en uygundur.</span><span class="sxs-lookup"><span data-stu-id="6837a-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="6837a-364">Resmi [Mongo istemci yeniden kullanım yönergeleri](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)temelinde `MongoClient`, tek bir hizmet ömrü ile birlikte kaydedilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6837a-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="6837a-365">`BookService` başvurusunu çözümlemek için aşağıdaki kodu *Startup.cs* 'un en üstüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="6837a-366">`BookService` sınıfı, veritabanında CRUD işlemleri gerçekleştirmek için aşağıdaki `MongoDB.Driver` üyeleri kullanır:</span><span class="sxs-lookup"><span data-stu-id="6837a-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="6837a-367">[Mongoclient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash;, veritabanı işlemlerini gerçekleştirmek için sunucu örneğini okur.</span><span class="sxs-lookup"><span data-stu-id="6837a-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="6837a-368">Bu sınıfın oluşturucusu, MongoDB bağlantı dizesini sağlanır:</span><span class="sxs-lookup"><span data-stu-id="6837a-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="6837a-369">[Imongodatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; işlemleri gerçekleştirmek Için Mongo veritabanını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6837a-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="6837a-370">Bu öğretici, belirli bir koleksiyondaki verilere erişim kazanmak için arabirimindeki genel [GetCollection\<TDocument > (koleksiyon)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="6837a-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="6837a-371">Bu yöntem çağrıldıktan sonra, koleksiyonda CRUD işlemleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="6837a-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="6837a-372">`GetCollection<TDocument>(collection)` Yöntem çağrısında:</span><span class="sxs-lookup"><span data-stu-id="6837a-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="6837a-373">`collection`, koleksiyon adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6837a-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="6837a-374">`TDocument`, koleksiyonda depolanan CLR nesne türünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6837a-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="6837a-375">`GetCollection<TDocument>(collection)`, koleksiyonu temsil eden bir [Mongocollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) nesnesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="6837a-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="6837a-376">Bu öğreticide, aşağıdaki yöntemlerden koleksiyonunda çağrılır:</span><span class="sxs-lookup"><span data-stu-id="6837a-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="6837a-377">[Deleteone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash;, belirtilen arama ölçütleriyle eşleşen tek bir belgeyi siler.</span><span class="sxs-lookup"><span data-stu-id="6837a-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="6837a-378">[\<TDocument > bul](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash;, koleksiyonda belirtilen arama ölçütleriyle eşleşen tüm belgeleri döndürür.</span><span class="sxs-lookup"><span data-stu-id="6837a-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="6837a-379">[Insertone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash;, belirtilen nesneyi koleksiyondaki yeni bir belge olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="6837a-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="6837a-380">[Replaceone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash;, belirtilen arama ölçütleriyle eşleşen tek belgeyi, belirtilen nesneyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6837a-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="6837a-381">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="6837a-381">Add a controller</span></span>

<span data-ttu-id="6837a-382">*Controllers* dizinine aşağıdaki kodla bir `BooksController` sınıfı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="6837a-383">Önceki web API denetleyicisi:</span><span class="sxs-lookup"><span data-stu-id="6837a-383">The preceding web API controller:</span></span>

* <span data-ttu-id="6837a-384">CRUD işlemleri gerçekleştirmek için `BookService` sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="6837a-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="6837a-385">GET, POST, PUT ve DELETE HTTP isteklerini desteklemek için eylem yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="6837a-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="6837a-386">Bir [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) yanıtı döndürmek için `Create` eylemi yönteminde <xref:System.Web.Http.ApiController.CreatedAtRoute*> çağırır.</span><span class="sxs-lookup"><span data-stu-id="6837a-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="6837a-387">Durum kodu 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="6837a-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="6837a-388">`CreatedAtRoute` Ayrıca yanıta bir `Location` üst bilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="6837a-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="6837a-389">`Location` üstbilgisi yeni oluşturulan kitabın URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="6837a-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="6837a-390">Web API 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="6837a-390">Test the web API</span></span>

1. <span data-ttu-id="6837a-391">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6837a-391">Build and run the app.</span></span>

1. <span data-ttu-id="6837a-392">Denetleyicinin parametresiz `Get` eylem yöntemini sınamak için `http://localhost:<port>/api/books` gidin.</span><span class="sxs-lookup"><span data-stu-id="6837a-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="6837a-393">Aşağıdaki JSON yanıtı gösterilir:</span><span class="sxs-lookup"><span data-stu-id="6837a-393">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="6837a-394">Denetleyicinin aşırı yüklenmiş `Get` eylem yöntemini sınamak için `http://localhost:<port>/api/books/{id here}` gidin.</span><span class="sxs-lookup"><span data-stu-id="6837a-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="6837a-395">Aşağıdaki JSON yanıtı gösterilir:</span><span class="sxs-lookup"><span data-stu-id="6837a-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="6837a-396">JSON serileştirme seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6837a-396">Configure JSON serialization options</span></span>

<span data-ttu-id="6837a-397">[Web API 'Sini test](#test-the-web-api) etme bölümünde döndürülen JSON yanıtları hakkında iki ayrıntı vardır:</span><span class="sxs-lookup"><span data-stu-id="6837a-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="6837a-398">' Varsayılan ortası büyük/küçük harf özelliği, CLR nesnesinin özellik adlarının Pascal büyük küçük harfleriyle eşleşecek şekilde değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6837a-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="6837a-399">`bookName` özelliği `Name`olarak döndürülmelidir.</span><span class="sxs-lookup"><span data-stu-id="6837a-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="6837a-400">Önceki gereksinimleri karşılamak için aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="6837a-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="6837a-401">`Startup.ConfigureServices`' de, aşağıdaki vurgulanmış kodu `AddMvc` yöntemi çağrısına zincirle:</span><span class="sxs-lookup"><span data-stu-id="6837a-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="6837a-402">Önceki değişiklik ile, Web API 'sinin seri hale getirilmiş JSON yanıtındaki Özellik adları CLR nesne türündeki ilgili özellik adlarıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="6837a-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="6837a-403">Örneğin, `Book` sınıfının `Author` özelliği `Author`olarak serileştirir.</span><span class="sxs-lookup"><span data-stu-id="6837a-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="6837a-404">*Modeller/Book. cs*' de, `BookName` özelliğine aşağıdaki [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) özniteliğiyle açıklama ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-404">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="6837a-405">`[JsonProperty]` özniteliğinin değeri `Name`, Web API 'sinin serileştirilmiş JSON yanıtında özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6837a-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="6837a-406">`[JsonProperty]` öznitelik başvurusunu çözümlemek için *modeller/Book. cs* ' nin en üstüne aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6837a-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="6837a-407">[Web API 'Sini test](#test-the-web-api) etme bölümünde tanımlanan adımları yineleyin.</span><span class="sxs-lookup"><span data-stu-id="6837a-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="6837a-408">JSON Özellik adlarındaki farka dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="6837a-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="6837a-409">Web API 'sine kimlik doğrulama desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="6837a-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="6837a-410">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="6837a-410">Next steps</span></span>

<span data-ttu-id="6837a-411">ASP.NET Core web API'leri oluşturmaya daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="6837a-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="6837a-412">Bu makalenin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="6837a-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
