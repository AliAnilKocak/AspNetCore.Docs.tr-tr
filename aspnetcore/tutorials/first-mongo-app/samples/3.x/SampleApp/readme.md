---
page_type: sample
description: Bu öğreticide, MongoDB NoSQL veritabanı kullanarak ASP.NET Core Web API 'sinin nasıl oluşturulacağı gösterilmektedir.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 402b25f3f7c1644a52832b5c8566269773932e95
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886291"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="9640e-102">MongoDB ile ASP.NET Core ile web API'si oluşturma</span><span class="sxs-lookup"><span data-stu-id="9640e-102">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="9640e-103">Bu öğreticide web API'si temel oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri gerçekleştiren oluşturur bir [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanı.</span><span class="sxs-lookup"><span data-stu-id="9640e-103">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="9640e-104">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="9640e-104">In this tutorial, you learn how to:</span></span>

* <span data-ttu-id="9640e-105">MongoDB yapılandırın</span><span class="sxs-lookup"><span data-stu-id="9640e-105">Configure MongoDB</span></span>
* <span data-ttu-id="9640e-106">MongoDB veritabanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="9640e-106">Create a MongoDB database</span></span>
* <span data-ttu-id="9640e-107">MongoDB koleksiyonu ve şema tanımlayın</span><span class="sxs-lookup"><span data-stu-id="9640e-107">Define a MongoDB collection and schema</span></span>
* <span data-ttu-id="9640e-108">Bir web API'sini MongoDB CRUD işlemleri gerçekleştirme</span><span class="sxs-lookup"><span data-stu-id="9640e-108">Perform MongoDB CRUD operations from a web API</span></span>
* <span data-ttu-id="9640e-109">JSON serileştirmesini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="9640e-109">Customize JSON serialization</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9640e-110">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="9640e-110">Prerequisites</span></span>

* [<span data-ttu-id="9640e-111">.NET Core SDK 3,0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="9640e-111">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="9640e-112">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview)</span><span class="sxs-lookup"><span data-stu-id="9640e-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="9640e-113">MongoDB</span><span class="sxs-lookup"><span data-stu-id="9640e-113">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a><span data-ttu-id="9640e-114">MongoDB yapılandırın</span><span class="sxs-lookup"><span data-stu-id="9640e-114">Configure MongoDB</span></span>

<span data-ttu-id="9640e-115">Windows kullanıyorsanız MongoDB varsayılan olarak *C:\\Program Files\\MongoDB* konumunda yüklüdür.</span><span class="sxs-lookup"><span data-stu-id="9640e-115">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="9640e-116">Ortam değişkenine *C\\: program\\Files MongoDB\\\\Server\<Version_Number\\> bin* ekleyin. `Path`</span><span class="sxs-lookup"><span data-stu-id="9640e-116">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="9640e-117">Bu değişiklik yerden MongoDB erişim sağlar, geliştirme makinenizde.</span><span class="sxs-lookup"><span data-stu-id="9640e-117">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="9640e-118">Mongo kabuğunu veritabanı oluşturma, koleksiyonları yapın ve belgeleri depolamak için aşağıdaki adımları kullanın.</span><span class="sxs-lookup"><span data-stu-id="9640e-118">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="9640e-119">Mongo Kabuğu komutları hakkında daha fazla bilgi için bkz. [mongo kabuğunu çalışma](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="9640e-119">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="9640e-120">Geliştirme makinenizde verilerin depolanması için bir dizin seçin.</span><span class="sxs-lookup"><span data-stu-id="9640e-120">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="9640e-121">Örneğin, *C:\\Windows üzerinde booksdata* .</span><span class="sxs-lookup"><span data-stu-id="9640e-121">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="9640e-122">Yoksa dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9640e-122">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="9640e-123">Mongo kabuğunu yeni dizinleri oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="9640e-123">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="9640e-124">Bir komut kabuğunu açın.</span><span class="sxs-lookup"><span data-stu-id="9640e-124">Open a command shell.</span></span> <span data-ttu-id="9640e-125">Varsayılan bağlantı noktası 27017 mongodb'ye bağlanmak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="9640e-125">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="9640e-126">Değiştirmeyi unutmayın `<data_directory_path>` önceki adımda seçtiğiniz dizini.</span><span class="sxs-lookup"><span data-stu-id="9640e-126">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="9640e-127">Başka bir komut kabuğu örneği açın.</span><span class="sxs-lookup"><span data-stu-id="9640e-127">Open another command shell instance.</span></span> <span data-ttu-id="9640e-128">Aşağıdaki komutu çalıştırarak varsayılan test veritabanı'na bağlanma:</span><span class="sxs-lookup"><span data-stu-id="9640e-128">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="9640e-129">Bir komut kabuğu'nda aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9640e-129">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="9640e-130">Adlı bir veritabanı zaten mevcut olmayan halinde *BookstoreDb* oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9640e-130">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="9640e-131">Veritabanı mevcut değilse, bağlantı işlemleri için açılır.</span><span class="sxs-lookup"><span data-stu-id="9640e-131">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="9640e-132">Oluşturma bir `Books` koleksiyon aşağıdaki komutu kullanarak:</span><span class="sxs-lookup"><span data-stu-id="9640e-132">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="9640e-133">Aşağıdaki sonucu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="9640e-133">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="9640e-134">İçin bir şema tanımlayabilir `Books` toplama ve ekleme iki belge aşağıdaki komutu kullanarak:</span><span class="sxs-lookup"><span data-stu-id="9640e-134">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="9640e-135">Aşağıdaki sonucu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="9640e-135">The following result is displayed:</span></span>

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
  > <span data-ttu-id="9640e-136">Bu makalede gösterilen KIMLIK, bu örneği çalıştırdığınızda kimliklerle eşleşmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="9640e-136">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="9640e-137">Aşağıdaki komutu kullanarak veritabanında belgelerini görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="9640e-137">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="9640e-138">Aşağıdaki sonucu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="9640e-138">The following result is displayed:</span></span>

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

    <span data-ttu-id="9640e-139">Şema girmiş ekler `_id` türünün özelliği `ObjectId` her belge için.</span><span class="sxs-lookup"><span data-stu-id="9640e-139">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="9640e-140">Veritabanı hazırdır.</span><span class="sxs-lookup"><span data-stu-id="9640e-140">The database is ready.</span></span> <span data-ttu-id="9640e-141">ASP.NET Core web API'si oluşturmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9640e-141">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="9640e-142">ASP.NET Core web API projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="9640e-142">Create the ASP.NET Core web API project</span></span>

1. <span data-ttu-id="9640e-143">Git **dosya** > **yeni** > **proje**.</span><span class="sxs-lookup"><span data-stu-id="9640e-143">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="9640e-144">**ASP.NET Core Web uygulaması** proje türünü seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="9640e-144">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="9640e-145">Projeyi *Booksapı*olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="9640e-145">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="9640e-146">**.NET Core** hedef çerçevesini ve **3,0 ASP.NET Core**seçin.</span><span class="sxs-lookup"><span data-stu-id="9640e-146">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="9640e-147">**API** proje şablonunu seçin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="9640e-147">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="9640e-148">[NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) için .net sürücüsünün en son kararlı sürümünü belirleme.</span><span class="sxs-lookup"><span data-stu-id="9640e-148">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="9640e-149">İçinde **Paket Yöneticisi Konsolu** penceresinde proje kök dizinine gidin.</span><span class="sxs-lookup"><span data-stu-id="9640e-149">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="9640e-150">MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9640e-150">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a><span data-ttu-id="9640e-151">Varlık modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="9640e-151">Add an entity model</span></span>

1. <span data-ttu-id="9640e-152">Ekleme bir *modelleri* proje kök dizini.</span><span class="sxs-lookup"><span data-stu-id="9640e-152">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="9640e-153">Ekleme bir `Book` sınıfının *modelleri* aşağıdaki kod ile dizin:</span><span class="sxs-lookup"><span data-stu-id="9640e-153">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="9640e-154">Önceki sınıfta, `Id` özelliği:</span><span class="sxs-lookup"><span data-stu-id="9640e-154">In the preceding class, the `Id` property:</span></span>

    * <span data-ttu-id="9640e-155">Ortak dil çalışma zamanı (CLR) nesnesini MongoDB koleksiyonuna eşlemek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="9640e-155">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="9640e-156">Bu özelliği belgenin birincil anahtarı olarak belirlemek için [[Bsonıd]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) ile açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="9640e-156">Is annotated with [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="9640e-157">[[Bsongösterimi (bsontype. ObjectID)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) ile, parametreyi `string` [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) yapısı yerine tür olarak geçirmeyi sağlamak için açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="9640e-157">Is annotated with [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="9640e-158">Mongo, ' den `string` ' ye `ObjectId`dönüştürmeyi işler.</span><span class="sxs-lookup"><span data-stu-id="9640e-158">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

    <span data-ttu-id="9640e-159">Özelliği [ [bsonelement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) özniteliğiyle açıklama eklenir. `BookName`</span><span class="sxs-lookup"><span data-stu-id="9640e-159">The `BookName` property is annotated with the [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="9640e-160">Özniteliğinin değeri `Name` , MongoDB koleksiyonundaki özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9640e-160">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="9640e-161">Yapılandırma modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="9640e-161">Add a configuration model</span></span>

1. <span data-ttu-id="9640e-162">Aşağıdaki veritabanı yapılandırma değerlerini *appSettings. JSON*öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9640e-162">Add the following database configuration values to *appsettings.json*:</span></span>

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. <span data-ttu-id="9640e-163">*Modeller* dizinine aşağıdaki kodla bir *BookstoreDatabaseSettings.cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9640e-163">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    <span data-ttu-id="9640e-164">Yukarıdaki `BookstoreDatabaseSettings` sınıf, `BookstoreDatabaseSettings` *appSettings. JSON* dosyasının özellik değerlerini depolamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9640e-164">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="9640e-165">JSON ve C# Özellik adları, eşleme sürecini kolaylaştırmak için aynı şekilde adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9640e-165">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="9640e-166">Aşağıdaki Vurgulanan kodu öğesine `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9640e-166">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    <span data-ttu-id="9640e-167">Önceki kodda:</span><span class="sxs-lookup"><span data-stu-id="9640e-167">In the preceding code:</span></span>

    * <span data-ttu-id="9640e-168">*AppSettings. JSON* dosyasının `BookstoreDatabaseSettings` bölüm bağlandığı yapılandırma örneği, bağımlılık ekleme (dı) kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9640e-168">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="9640e-169">Örneğin `BookstoreDatabaseSettings` , bir `ConnectionString` nesnenin `BookstoreDatabaseSettings:ConnectionString` özelliği *appSettings. JSON*içindeki özelliği ile doldurulur.</span><span class="sxs-lookup"><span data-stu-id="9640e-169">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="9640e-170">Arabirim `IBookstoreDatabaseSettings` , tek bir [hizmet ömrü](xref:fundamentals/dependency-injection#service-lifetimes)ile dı 'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9640e-170">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="9640e-171">Eklenen arabirim örneği bir `BookstoreDatabaseSettings` nesne olarak çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="9640e-171">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="9640e-172">`BookstoreDatabaseSettings` Ve`IBookstoreDatabaseSettings` başvurularını çözümlemek için aşağıdaki kodu Startup.cs 'in en üstüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9640e-172">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="9640e-173">CRUD işlemleri hizmeti ekleme</span><span class="sxs-lookup"><span data-stu-id="9640e-173">Add a CRUD operations service</span></span>

1. <span data-ttu-id="9640e-174">Ekleme bir *Hizmetleri* proje kök dizini.</span><span class="sxs-lookup"><span data-stu-id="9640e-174">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="9640e-175">Ekleme bir `BookService` sınıfının *Hizmetleri* aşağıdaki kod ile dizin:</span><span class="sxs-lookup"><span data-stu-id="9640e-175">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    <span data-ttu-id="9640e-176">Yukarıdaki kodda, bir `IBookstoreDatabaseSettings` örnek oluşturucu ekleme yoluyla dı 'den alınır.</span><span class="sxs-lookup"><span data-stu-id="9640e-176">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="9640e-177">Bu teknik, [yapılandırma modeli ekleme](#add-a-configuration-model) bölümüne eklenen *appSettings. JSON* yapılandırma değerlerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="9640e-177">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="9640e-178">Aşağıdaki Vurgulanan kodu öğesine `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9640e-178">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    <span data-ttu-id="9640e-179">Önceki kodda, `BookService` sınıfı, tüketen sınıflarda Oluşturucu ekleme işlemini desteklemek için dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9640e-179">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="9640e-180">Tek hizmet ömrü en uygundur çünkü `BookService` doğrudan bir `MongoClient`bağımlılık alır.</span><span class="sxs-lookup"><span data-stu-id="9640e-180">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="9640e-181">Resmi [Mongo istemci yeniden kullanım yönergelerine](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)göre, `MongoClient` tek bir hizmet ömrü ile dı 'ye kaydedilmelidir.</span><span class="sxs-lookup"><span data-stu-id="9640e-181">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="9640e-182">`BookService` Başvuruyu çözümlemek için aşağıdaki kodu *Startup.cs* 'in en üstüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9640e-182">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>


    ```csharp
    using BooksApi.Services;
    ```

<span data-ttu-id="9640e-183">`BookService` Sınıfını kullanan aşağıdaki `MongoDB.Driver` veritabanında CRUD işlemleri gerçekleştirmek için üyeleri:</span><span class="sxs-lookup"><span data-stu-id="9640e-183">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="9640e-184">[Mongoclient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Veritabanı işlemlerini gerçekleştirmek için sunucu örneğini okur.</span><span class="sxs-lookup"><span data-stu-id="9640e-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="9640e-185">Bu sınıfın oluşturucusu, MongoDB bağlantı dizesini sağlanır:</span><span class="sxs-lookup"><span data-stu-id="9640e-185">The constructor of this class is provided the MongoDB connection string:</span></span>

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* <span data-ttu-id="9640e-186">[Imongodatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; İşlemleri gerçekleştirmek için Mongo veritabanını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9640e-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="9640e-187">Bu öğretici, belirli bir koleksiyondaki verilere erişim kazanmak için arabirimdeki genel [GetCollection\<tdocument > (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="9640e-187">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="9640e-188">Bu yöntem çağrıldıktan sonra, koleksiyonda CRUD işlemleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="9640e-188">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="9640e-189">İçinde `GetCollection<TDocument>(collection)` yöntem çağrısı:</span><span class="sxs-lookup"><span data-stu-id="9640e-189">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="9640e-190">`collection` Koleksiyon adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9640e-190">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="9640e-191">`TDocument` Bir koleksiyonda depolanan CLR nesne türünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9640e-191">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="9640e-192">`GetCollection<TDocument>(collection)`koleksiyonu temsil eden bir [Mongocollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) nesnesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="9640e-192">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="9640e-193">Bu öğreticide, aşağıdaki yöntemlerden koleksiyonunda çağrılır:</span><span class="sxs-lookup"><span data-stu-id="9640e-193">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="9640e-194">[Deleteone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Belirtilen arama ölçütleriyle eşleşen tek bir belgeyi siler.</span><span class="sxs-lookup"><span data-stu-id="9640e-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="9640e-195">[Tdocument >\<](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; bul, koleksiyonda belirtilen arama ölçütleriyle eşleşen tüm belgeleri döndürür.</span><span class="sxs-lookup"><span data-stu-id="9640e-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="9640e-196">[Insertone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Belirtilen nesneyi, koleksiyonda yeni bir belge olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="9640e-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="9640e-197">[Replaceone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Belirtilen arama ölçütleriyle eşleşen tek belgeyi, belirtilen nesneyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="9640e-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="9640e-198">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="9640e-198">Add a controller</span></span>

<span data-ttu-id="9640e-199">Ekleme bir `BooksController` sınıfının *denetleyicileri* aşağıdaki kod ile dizin:</span><span class="sxs-lookup"><span data-stu-id="9640e-199">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

<span data-ttu-id="9640e-200">Önceki web API denetleyicisi:</span><span class="sxs-lookup"><span data-stu-id="9640e-200">The preceding web API controller:</span></span>

* <span data-ttu-id="9640e-201">Kullanan `BookService` CRUD işlemleri gerçekleştirmek için sınıf.</span><span class="sxs-lookup"><span data-stu-id="9640e-201">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="9640e-202">GET, POST, PUT ve DELETE HTTP isteklerini desteklemek için eylem yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="9640e-202">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="9640e-203">[Http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) yanıtı döndürmek için <xref:System.Web.Http.ApiController.CreatedAtRoute*> eylemyöntemindekiçağrılar.`Create`</span><span class="sxs-lookup"><span data-stu-id="9640e-203">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="9640e-204">Durum kodu 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="9640e-204">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="9640e-205">`CreatedAtRoute`Ayrıca yanıta bir `Location` üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="9640e-205">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="9640e-206">`Location` Üst bilgi, yeni oluşturulan kitabın URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="9640e-206">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="9640e-207">Web API 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="9640e-207">Test the web API</span></span>

1. <span data-ttu-id="9640e-208">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="9640e-208">Build and run the app.</span></span>

1. <span data-ttu-id="9640e-209">Denetleyicinin parametresiz `http://localhost:<port>/api/books` `Get` eylem yöntemini sınamak için öğesine gidin.</span><span class="sxs-lookup"><span data-stu-id="9640e-209">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="9640e-210">Aşağıdaki JSON yanıtı gösterilir:</span><span class="sxs-lookup"><span data-stu-id="9640e-210">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="9640e-211">Denetleyicinin aşırı `http://localhost:<port>/api/books/{id here}` yüklenmiş `Get` eylem yöntemini sınamak için öğesine gidin.</span><span class="sxs-lookup"><span data-stu-id="9640e-211">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="9640e-212">Aşağıdaki JSON yanıtı gösterilir:</span><span class="sxs-lookup"><span data-stu-id="9640e-212">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="9640e-213">JSON serileştirme seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9640e-213">Configure JSON serialization options</span></span>

<span data-ttu-id="9640e-214">[Web API 'Sini test](#test-the-web-api) etme bölümünde döndürülen JSON yanıtları hakkında iki ayrıntı vardır:</span><span class="sxs-lookup"><span data-stu-id="9640e-214">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="9640e-215">' Varsayılan ortası büyük/küçük harf özelliği, CLR nesnesinin özellik adlarının Pascal büyük küçük harfleriyle eşleşecek şekilde değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="9640e-215">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="9640e-216">`bookName` Özelliği olarak`Name`döndürülmelidir.</span><span class="sxs-lookup"><span data-stu-id="9640e-216">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="9640e-217">Önceki gereksinimleri karşılamak için aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="9640e-217">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="9640e-218">JSON.NET, ASP.NET paylaşılan çerçevesinden kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="9640e-218">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="9640e-219">[Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9640e-219">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="9640e-220">' `Startup.ConfigureServices`De, aşağıdaki vurgulanmış kodu `AddMvc` yöntem çağrısına zincirle:</span><span class="sxs-lookup"><span data-stu-id="9640e-220">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    <span data-ttu-id="9640e-221">Önceki değişiklik ile, Web API 'sinin seri hale getirilmiş JSON yanıtındaki Özellik adları CLR nesne türündeki ilgili özellik adlarıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="9640e-221">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="9640e-222">Örneğin, `Book` `Author` sınıfın özelliği olarak `Author`serileştirir.</span><span class="sxs-lookup"><span data-stu-id="9640e-222">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="9640e-223">*Modeller/Book. cs*' de, aşağıdaki `BookName` [[jsonproperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) özniteliğiyle özelliğe not ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9640e-223">In *Models/Book.cs*, annotate the `BookName` property with the following [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    <span data-ttu-id="9640e-224">Özniteliğinin değeri, Web API 'sinin serileştirilmiş JSON yanıtında özellik adını temsileder.`Name` `[JsonProperty]`</span><span class="sxs-lookup"><span data-stu-id="9640e-224">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="9640e-225">Aşağıdaki kodu *modeller/Book. cs* ' nin üst kısmına ekleyerek `[JsonProperty]` öznitelik başvurusunu çözümleyin:</span><span class="sxs-lookup"><span data-stu-id="9640e-225">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    ```csharp
    using Newtonsoft.Json;
    ```

1. <span data-ttu-id="9640e-226">[Web API 'Sini test](#test-the-web-api) etme bölümünde tanımlanan adımları yineleyin.</span><span class="sxs-lookup"><span data-stu-id="9640e-226">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="9640e-227">JSON Özellik adlarındaki farka dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="9640e-227">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9640e-228">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="9640e-228">Next steps</span></span>

<span data-ttu-id="9640e-229">ASP.NET Core web API'leri oluşturmaya daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="9640e-229">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="9640e-230">Bu makalenin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="9640e-230">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [<span data-ttu-id="9640e-231">ASP.NET Core ile Web API 'Leri oluşturma</span><span class="sxs-lookup"><span data-stu-id="9640e-231">Create web APIs with ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)