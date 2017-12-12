---
uid: mvc/overview/getting-started/introduction/accessing-your-models-data-from-a-controller
title: Bir denetleyicisinden modelinizin verilere | Microsoft Docs
author: Rick-Anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/17/2013
ms.topic: article
ms.assetid: caa1ba4a-f9f0-4181-ba21-042e3997861d
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/introduction/accessing-your-models-data-from-a-controller
msc.type: authoredcontent
ms.openlocfilehash: b60913cef4b62745cf167e6074834bf7d0c228d1
ms.sourcegitcommit: d1d8071d4093bf2444b5ae19d6e45c3d187e338b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2017
---
<a name="accessing-your-models-data-from-a-controller"></a><span data-ttu-id="48aa7-102">Bir denetleyicisinden modelinizin verilerine erişme</span><span class="sxs-lookup"><span data-stu-id="48aa7-102">Accessing Your Model's Data from a Controller</span></span>
====================
<span data-ttu-id="48aa7-103">Tarafından [Rick Anderson](https://github.com/Rick-Anderson)</span><span class="sxs-lookup"><span data-stu-id="48aa7-103">by [Rick Anderson](https://github.com/Rick-Anderson)</span></span>

[!INCLUDE[Tutorial Note](sample/code-location.md)]

<span data-ttu-id="48aa7-104">Bu bölümde, yeni oluşturacağınız `MoviesController` sınıfı ve film verileri alır ve bir görünüm şablonu kullanarak tarayıcıda görüntüleyen kod yazın.</span><span class="sxs-lookup"><span data-stu-id="48aa7-104">In this section, you'll create a new `MoviesController` class and write code that retrieves the movie data and displays it in the browser using a view template.</span></span>

<span data-ttu-id="48aa7-105">**Uygulamayı derleme** sonraki adıma geçmeden önce.</span><span class="sxs-lookup"><span data-stu-id="48aa7-105">**Build the application** before going on to the next step.</span></span> <span data-ttu-id="48aa7-106">Uygulamayı yapılandırdıysanız olmayan bir denetleyici eklenirken bir hata alırsınız.</span><span class="sxs-lookup"><span data-stu-id="48aa7-106">If you don't build the application, you'll get an error adding a controller.</span></span>

<span data-ttu-id="48aa7-107">Çözüm Gezgini'nde sağ *denetleyicileri* klasörünü ve ardından **Ekle**, ardından **denetleyicisi**.</span><span class="sxs-lookup"><span data-stu-id="48aa7-107">In Solution Explorer, right-click the *Controllers* folder and then click **Add**, then **Controller**.</span></span>

![](accessing-your-models-data-from-a-controller/_static/image1.png)

<span data-ttu-id="48aa7-108">İçinde **İskele Ekle** iletişim kutusu, tıklatın **Entity Framework kullanarak MVC 5 denetleyici, görünümleri olan**ve ardından **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="48aa7-108">In the **Add Scaffold** dialog box, click **MVC 5 Controller with views, using Entity Framework**, and then click **Add**.</span></span>

![](accessing-your-models-data-from-a-controller/_static/image2.png)

- <span data-ttu-id="48aa7-109">Seçin **film (MvcMovie.Models)** Model sınıfı için.</span><span class="sxs-lookup"><span data-stu-id="48aa7-109">Select **Movie (MvcMovie.Models)** for the Model class.</span></span>
- <span data-ttu-id="48aa7-110">Seçin **MovieDBContext (MvcMovie.Models)** veri bağlamı sınıfı için.</span><span class="sxs-lookup"><span data-stu-id="48aa7-110">Select **MovieDBContext (MvcMovie.Models)** for the Data context class.</span></span>
- <span data-ttu-id="48aa7-111">Denetleyici adı **MoviesController**.</span><span class="sxs-lookup"><span data-stu-id="48aa7-111">For the Controller name enter **MoviesController**.</span></span>

 <span data-ttu-id="48aa7-112">Aşağıdaki görüntü, tamamlanan iletişim kutusunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="48aa7-112">The image below shows the completed dialog.</span></span>  
  
![](accessing-your-models-data-from-a-controller/_static/image3.png)   

<span data-ttu-id="48aa7-113">**Ekle**'yi tıklatın.</span><span class="sxs-lookup"><span data-stu-id="48aa7-113">Click **Add**.</span></span> <span data-ttu-id="48aa7-114">(Bir hata alırsanız, büyük olasılıkla denetleyicisi ekleme başlatmadan önce uygulamayı oluşturabilirsiniz alamadık.) Visual Studio, aşağıdaki dosyaları ve klasörleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="48aa7-114">(If you get an error, you probably didn't build the application before starting adding the controller.) Visual Studio creates the following files and folders:</span></span>

- <span data-ttu-id="48aa7-115">*Bir MoviesController.cs* dosyasını *denetleyicileri* klasör.</span><span class="sxs-lookup"><span data-stu-id="48aa7-115">*A MoviesController.cs* file in the *Controllers* folder.</span></span>
- <span data-ttu-id="48aa7-116">A *Views\Movies* klasör.</span><span class="sxs-lookup"><span data-stu-id="48aa7-116">A *Views\Movies* folder.</span></span>
- <span data-ttu-id="48aa7-117">*Create.cshtml, Delete.cshtml, Details.cshtml, Edit.cshtml*, ve *Index.cshtml* yeni *Views\Movies* klasör.</span><span class="sxs-lookup"><span data-stu-id="48aa7-117">*Create.cshtml, Delete.cshtml, Details.cshtml, Edit.cshtml*, and *Index.cshtml* in the new *Views\Movies* folder.</span></span>

<span data-ttu-id="48aa7-118">Visual Studio otomatik olarak oluşturulan [CRUD](http://en.wikipedia.org/wiki/Create,_read,_update_and_delete) (oluşturma, okuma, güncelleştirme ve silme) eylem yöntemleri ve sizin için görünümleri (CRUD eylem yöntemleri ve görünümler otomatik olarak oluşturulmasını yapı iskelesi olarak bilinir).</span><span class="sxs-lookup"><span data-stu-id="48aa7-118">Visual Studio automatically created the [CRUD](http://en.wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views for you (the automatic creation of CRUD action methods and views is known as scaffolding).</span></span> <span data-ttu-id="48aa7-119">Artık oluşturma listesinde, düzenleme ve film girişleri silmek olanak sağlayan bir tam olarak işlevsel bir web uygulaması sahipsiniz.</span><span class="sxs-lookup"><span data-stu-id="48aa7-119">You now have a fully functional web application that lets you create, list, edit, and delete movie entries.</span></span>

<span data-ttu-id="48aa7-120">Uygulamayı çalıştırın ve tıklayın **MVC film** bağlantısı (veya Gözat `Movies` ekleyerek denetleyicisi */Movies* tarayıcınızın adres çubuğundaki URL'ye).</span><span class="sxs-lookup"><span data-stu-id="48aa7-120">Run the application and click on the **MVC Movie** link (or browse to the `Movies` controller by appending */Movies* to the URL in the address bar of your browser).</span></span> <span data-ttu-id="48aa7-121">Varsayılan yönlendirme uygulama bağlı olduğundan (tanımlanan *uygulama\_Start\RouteConfig.cs* dosyası), tarayıcı isteğini `http://localhost:xxxxx/Movies` varsayılan yönlendirilen `Index` Eylemyöntemi`Movies` denetleyicisi.</span><span class="sxs-lookup"><span data-stu-id="48aa7-121">Because the application is relying on the default routing (defined in the *App\_Start\RouteConfig.cs* file), the browser request `http://localhost:xxxxx/Movies` is routed to the default `Index` action method of the `Movies` controller.</span></span> <span data-ttu-id="48aa7-122">Diğer bir deyişle, tarayıcı isteğini `http://localhost:xxxxx/Movies` tarayıcı isteğini etkili bir şekilde aynıdır `http://localhost:xxxxx/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="48aa7-122">In other words, the browser request `http://localhost:xxxxx/Movies` is effectively the same as the browser request `http://localhost:xxxxx/Movies/Index`.</span></span> <span data-ttu-id="48aa7-123">Sonuç boş bir liste film, çünkü henüz eklemediniz.</span><span class="sxs-lookup"><span data-stu-id="48aa7-123">The result is an empty list of movies, because you haven't added any yet.</span></span>

![](accessing-your-models-data-from-a-controller/_static/image4.png)

### <a name="creating-a-movie"></a><span data-ttu-id="48aa7-124">Bir filmi oluşturma</span><span class="sxs-lookup"><span data-stu-id="48aa7-124">Creating a Movie</span></span>

<span data-ttu-id="48aa7-125">Seçin **Yeni Oluştur** bağlantı.</span><span class="sxs-lookup"><span data-stu-id="48aa7-125">Select the **Create New** link.</span></span> <span data-ttu-id="48aa7-126">Bir filmi bazı ayrıntılarını girin ve ardından **oluşturma** düğmesi.</span><span class="sxs-lookup"><span data-stu-id="48aa7-126">Enter some details about a movie and then click the **Create** button.</span></span>


![](accessing-your-models-data-from-a-controller/_static/image5.png)

> [!NOTE]
> <span data-ttu-id="48aa7-127">Ondalık ayırıcıların veya virgül Fiyat alanına girmeniz mümkün olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="48aa7-127">You may not be able to enter decimal points or commas in the Price field.</span></span> <span data-ttu-id="48aa7-128">bir virgül İngilizce dışındaki yerel ayarlar için jQuery doğrulamasına desteklemek için (&quot;,&quot;) ondalık ve ABD İngilizcesi dışındaki tarih biçimleri için içermelidir *globalize.js* ve özel  *cultures/globalize.cultures.js* dosyası (gelen [https://github.com/jquery/globalize](https://github.com/jquery/globalize) ) ve kullanmak için JavaScript'i `Globalize.parseFloat`.</span><span class="sxs-lookup"><span data-stu-id="48aa7-128">To support jQuery validation for non-English locales that use a comma (&quot;,&quot;) for a decimal point, and non US-English date formats, you must include *globalize.js* and your specific *cultures/globalize.cultures.js* file(from [https://github.com/jquery/globalize](https://github.com/jquery/globalize) ) and JavaScript to use `Globalize.parseFloat`.</span></span> <span data-ttu-id="48aa7-129">Sonraki öğreticide bunu nasıl göster.</span><span class="sxs-lookup"><span data-stu-id="48aa7-129">I'll show how to do this in the next tutorial.</span></span> <span data-ttu-id="48aa7-130">Şimdilik, yalnızca tam sayılar 10 gibi girin.</span><span class="sxs-lookup"><span data-stu-id="48aa7-130">For now, just enter whole numbers like 10.</span></span>


<span data-ttu-id="48aa7-131">Tıklatarak **oluşturma** düğmesi sunucuya film bilgileri veritabanında kaydedildiği postalama forma neden olur.</span><span class="sxs-lookup"><span data-stu-id="48aa7-131">Clicking the **Create** button causes the form to be posted to the server, where the movie information is saved in the database.</span></span> <span data-ttu-id="48aa7-132">Ardından için yönlendirilirsiniz */Movies* burada görebilirsiniz listesinde yeni oluşturulan film URL.</span><span class="sxs-lookup"><span data-stu-id="48aa7-132">You're then redirected to the */Movies* URL, where you can see the newly created movie in the listing.</span></span>

![](accessing-your-models-data-from-a-controller/_static/image6.png)

<span data-ttu-id="48aa7-133">Daha fazla birkaç film girişleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="48aa7-133">Create a couple more movie entries.</span></span> <span data-ttu-id="48aa7-134">Deneyin **Düzenle**, **ayrıntıları**, ve **silmek** tüm işlev bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="48aa7-134">Try the **Edit**, **Details**, and **Delete** links, which are all functional.</span></span>

## <a name="examining-the-generated-code"></a><span data-ttu-id="48aa7-135">Oluşturulan kod İnceleme</span><span class="sxs-lookup"><span data-stu-id="48aa7-135">Examining the Generated Code</span></span>

<span data-ttu-id="48aa7-136">Açık *Controllers\MoviesController.cs* dosya ve oluşturulan inceleyin `Index` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="48aa7-136">Open the *Controllers\MoviesController.cs* file and examine the generated `Index` method.</span></span> <span data-ttu-id="48aa7-137">Film denetleyicisiyle bir kısmı `Index` yöntemi aşağıda gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="48aa7-137">A portion of the movie controller with the `Index` method is shown below.</span></span>

[!code-csharp[Main](accessing-your-models-data-from-a-controller/samples/sample1.cs)]

<span data-ttu-id="48aa7-138">Bir istek `Movies` denetleyicisi döndürür tüm girişler `Movies` tablo ve sonuçları geçirir `Index` görünümü.</span><span class="sxs-lookup"><span data-stu-id="48aa7-138">A request to the `Movies` controller returns all the entries in the `Movies` table and then passes the results to the `Index` view.</span></span> <span data-ttu-id="48aa7-139">Aşağıdaki satır `MoviesController` sınıfı, daha önce açıklandığı gibi bir filmi veritabanı bağlamını başlatır.</span><span class="sxs-lookup"><span data-stu-id="48aa7-139">The following line from the `MoviesController` class instantiates a movie database context, as described previously.</span></span> <span data-ttu-id="48aa7-140">Film veritabanı bağlamı, sorgu, düzenlemek ve filmler silmek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="48aa7-140">You can use the movie database context to query, edit, and delete movies.</span></span>

[!code-csharp[Main](accessing-your-models-data-from-a-controller/samples/sample2.cs)]

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="48aa7-141">Modelleri'kesin türü belirtilmiş ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="48aa7-141">Strongly Typed Models and the @model Keyword</span></span>

<span data-ttu-id="48aa7-142">Bu öğreticide daha önce nasıl bir denetleyici veri veya nesneler görünümü kullanarak şablonu geçirebilirsiniz gördüğünüzü `ViewBag` nesnesi.</span><span class="sxs-lookup"><span data-stu-id="48aa7-142">Earlier in this tutorial, you saw how a controller can pass data or objects to a view template using the `ViewBag` object.</span></span> <span data-ttu-id="48aa7-143">`ViewBag` Bir görünüme bilgi geçirmek için kullanışlı bir geç bağlama yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="48aa7-143">The `ViewBag` is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="48aa7-144">MVC geçirmek olanağı da sağlar *kesinlikle* belirlenmiş nesnelerin şablonu görüntüleme için.</span><span class="sxs-lookup"><span data-stu-id="48aa7-144">MVC also provides the ability to pass *strongly* typed objects to a view template.</span></span> <span data-ttu-id="48aa7-145">Kesin türü belirtilmiş bu yaklaşım daha iyi derleme zamanı kodunuzun denetleme ve daha zengin sağlar [IntelliSense](https://msdn.microsoft.com/en-us/library/hcw1s69b(v=vs.120).aspx) Visual Studio düzenleyicisinde.</span><span class="sxs-lookup"><span data-stu-id="48aa7-145">This strongly typed approach enables better compile-time checking of your code and richer [IntelliSense](https://msdn.microsoft.com/en-us/library/hcw1s69b(v=vs.120).aspx) in the Visual Studio editor.</span></span> <span data-ttu-id="48aa7-146">Bu yaklaşım Visual Studio yapı iskelesi yönteminde kullanılan (diğer bir deyişle, geçirme bir *kesinlikle* yazılı modeli) ile `MoviesController` yöntemleri ve görünümler oluşturduğunuzda sınıfı ve görünüm şablonları.</span><span class="sxs-lookup"><span data-stu-id="48aa7-146">The scaffolding mechanism in Visual Studio used this approach (that is, passing a *strongly* typed model) with the `MoviesController` class and view templates when it created the methods and views.</span></span>

<span data-ttu-id="48aa7-147">İçinde *Controllers\MoviesController.cs* dosyasını inceleyin oluşturulan `Details` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="48aa7-147">In the *Controllers\MoviesController.cs* file examine the generated `Details` method.</span></span> <span data-ttu-id="48aa7-148">`Details` Yöntemi aşağıda gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="48aa7-148">The `Details` method is shown below.</span></span>

[!code-csharp[Main](accessing-your-models-data-from-a-controller/samples/sample3.cs)]

<span data-ttu-id="48aa7-149">`id` Parametre genellikle geçirilir rota verileri, örneğin `http://localhost:1234/movies/details/1` denetleyicisi film denetleyici için eylem ayarlar `details` ve `id` 1.</span><span class="sxs-lookup"><span data-stu-id="48aa7-149">The `id` parameter is generally passed as route data, for example `http://localhost:1234/movies/details/1` will set the controller to the movie controller, the action to `details` and the `id` to 1.</span></span> <span data-ttu-id="48aa7-150">Ayrıca, bir sorgu dizesi ile kimliği gibi geçirebilirdiniz:</span><span class="sxs-lookup"><span data-stu-id="48aa7-150">You could also pass in the id with a query string as follows:</span></span>

`http://localhost:1234/movies/details?id=1`

<span data-ttu-id="48aa7-151">Varsa bir `Movie` bulunursa, bir örneğini `Movie` modeli iletilir `Details` görünümü:</span><span class="sxs-lookup"><span data-stu-id="48aa7-151">If a `Movie` is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

[!code-csharp[Main](accessing-your-models-data-from-a-controller/samples/sample4.cs)]

<span data-ttu-id="48aa7-152">İçeriğini incelemek *Views\Movies\Details.cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="48aa7-152">Examine the contents of the *Views\Movies\Details.cshtml* file:</span></span>

[!code-cshtml[Main](accessing-your-models-data-from-a-controller/samples/sample5.cshtml?highlight=1-2)]

<span data-ttu-id="48aa7-153">Ekleyerek bir `@model` deyimini dosyanın üst kısmındaki görünüm şablonu görünümü bekliyor nesne türünü belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="48aa7-153">By including a `@model` statement at the top of the view template file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="48aa7-154">Film denetleyicisini oluşturduğunuzda, Visual Studio otomatik olarak aşağıdaki dahil `@model` deyimi en üstündeki *Details.cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="48aa7-154">When you created the movie controller, Visual Studio automatically included the following `@model` statement at the top of the *Details.cshtml* file:</span></span>

[!code-cshtml[Main](accessing-your-models-data-from-a-controller/samples/sample6.cshtml)]

<span data-ttu-id="48aa7-155">Bu `@model` yönergesi kullanarak denetleyici görünüm tarafından geçirilen film erişmenize olanak sağlayan bir `Model` kesin türü belirtilmiş nesnesi.</span><span class="sxs-lookup"><span data-stu-id="48aa7-155">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="48aa7-156">Örneğin, *Details.cshtml* şablonu, kodu her film alanına geçirir `DisplayNameFor` ve [DisplayFor](https://msdn.microsoft.com/en-us/library/system.web.mvc.html.displayextensions.displayfor(VS.98).aspx) HTML Yardımcıları ile güçlü şekilde yazılan `Model` nesnesi.</span><span class="sxs-lookup"><span data-stu-id="48aa7-156">For example, in the *Details.cshtml* template, the code passes each movie field to the `DisplayNameFor` and [DisplayFor](https://msdn.microsoft.com/en-us/library/system.web.mvc.html.displayextensions.displayfor(VS.98).aspx) HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="48aa7-157">`Create` Ve `Edit` yöntemleri ve görünüm şablonları da film model nesnesi geçirin.</span><span class="sxs-lookup"><span data-stu-id="48aa7-157">The `Create` and `Edit` methods and view templates also pass a movie model object.</span></span>

<span data-ttu-id="48aa7-158">İncelemek *Index.cshtml* şablonu görüntüleme ve `Index` yönteminde *MoviesController.cs* dosya.</span><span class="sxs-lookup"><span data-stu-id="48aa7-158">Examine the *Index.cshtml* view template and the `Index` method in the *MoviesController.cs* file.</span></span> <span data-ttu-id="48aa7-159">Kodu nasıl oluşturduğunu fark bir [ `List` ](https://msdn.microsoft.com/en-us/library/6sh2ey19.aspx) nesne çağırdığında `View` yardımcı yönteminin `Index` eylem yöntemi.</span><span class="sxs-lookup"><span data-stu-id="48aa7-159">Notice how the code creates a [`List`](https://msdn.microsoft.com/en-us/library/6sh2ey19.aspx) object when it calls the `View` helper method in the `Index` action method.</span></span> <span data-ttu-id="48aa7-160">Kodu daha sonra bu geçirir `Movies` dan listesinde `Index` eylem yöntemi görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="48aa7-160">The code then passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[Main](accessing-your-models-data-from-a-controller/samples/sample7.cs?highlight=3)]

<span data-ttu-id="48aa7-161">Film denetleyicisini oluşturduğunuzda, Visual Studio otomatik olarak aşağıdaki dahil `@model` deyimi en üstündeki *Index.cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="48aa7-161">When you created the movie controller, Visual Studio automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

[!code-cshtml[Main](accessing-your-models-data-from-a-controller/samples/sample8.cshtml)]

<span data-ttu-id="48aa7-162">Bu `@model` yönergesi kullanarak denetleyici görünüm tarafından geçirilen filmler listesi erişmenize olanak sağlayan bir `Model` kesin türü belirtilmiş nesnesi.</span><span class="sxs-lookup"><span data-stu-id="48aa7-162">This `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="48aa7-163">Örneğin, *Index.cshtml* şablon kodu döngüler filmler yaparak bir `foreach` kesin türü belirtilmiş üzerinden deyimi `Model` nesnesi:</span><span class="sxs-lookup"><span data-stu-id="48aa7-163">For example, in the *Index.cshtml* template, the code loops through the movies by doing a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[Main](accessing-your-models-data-from-a-controller/samples/sample9.cshtml?highlight=1,4,7,10,13,16,19-21)]

<span data-ttu-id="48aa7-164">Çünkü `Model` nesne kesin türü belirtilmiş (olarak bir `IEnumerable<Movie>` nesnesi), her `item` döngüsünde nesne türü olarak `Movie`.</span><span class="sxs-lookup"><span data-stu-id="48aa7-164">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each `item` object in the loop is typed as `Movie`.</span></span> <span data-ttu-id="48aa7-165">Diğer avantajlar arasında bu kod derleme zamanı denetimi Al ve kod düzenleyicisinde IntelliSense desteği tam anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="48aa7-165">Among other benefits, this means that you get compile-time checking of the code and full IntelliSense support in the code editor:</span></span>

![ModelIntellisene](accessing-your-models-data-from-a-controller/_static/image8.png)

## <a name="working-with-sql-server-localdb"></a><span data-ttu-id="48aa7-167">SQL Server yerel veritabanı ile çalışma</span><span class="sxs-lookup"><span data-stu-id="48aa7-167">Working with SQL Server LocalDB</span></span>

<span data-ttu-id="48aa7-168">Entity Framework Code First algılanan sağlanan veritabanı bağlantı dizesi için işaret bir `Movies` Code First veritabanı otomatik olarak oluşturulan, henüz yoksa veritabanı.</span><span class="sxs-lookup"><span data-stu-id="48aa7-168">Entity Framework Code First detected that the database connection string that was provided pointed to a `Movies` database that didn't exist yet, so Code First created the database automatically.</span></span> <span data-ttu-id="48aa7-169">Bakarak oluşturulduktan olmadığını doğrulayabilirsiniz *uygulama\_veri* klasör.</span><span class="sxs-lookup"><span data-stu-id="48aa7-169">You can verify that it's been created by looking in the *App\_Data* folder.</span></span> <span data-ttu-id="48aa7-170">Görmüyorsanız, *Movies.mdf* dosya, tıklatın **tüm dosyaları göster** düğmesini **Çözüm Gezgini** araç, tıklatın **yenileme** düğmesine tıklayın ve ardından *uygulama\_veri* klasör.</span><span class="sxs-lookup"><span data-stu-id="48aa7-170">If you don't see the *Movies.mdf* file, click the **Show All Files** button in the **Solution Explorer** toolbar, click the **Refresh** button, and then expand the *App\_Data* folder.</span></span>

![](accessing-your-models-data-from-a-controller/_static/image9.png)

<span data-ttu-id="48aa7-171">Çift tıklatın *Movies.mdf* açmak için **sunucu GEZGİNİ**, ardından genişletin **tabloları** klasörü filmler tabloya bakın.</span><span class="sxs-lookup"><span data-stu-id="48aa7-171">Double-click *Movies.mdf* to open **SERVER EXPLORER**, then expand the **Tables** folder to see the Movies table.</span></span> <span data-ttu-id="48aa7-172">Kimliği yanındaki anahtar simgesine unutmayın</span><span class="sxs-lookup"><span data-stu-id="48aa7-172">Note the key icon next to ID.</span></span> <span data-ttu-id="48aa7-173">Varsayılan olarak, birincil anahtar kimliği adlı bir özellik EF hale getirir.</span><span class="sxs-lookup"><span data-stu-id="48aa7-173">By default, EF will make a property named ID the primary key.</span></span> <span data-ttu-id="48aa7-174">EF ve MVC hakkında daha fazla bilgi için zel Dykstra'nın mükemmel bkz [MVC ve EF](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="48aa7-174">For more information on EF and MVC, see Tom Dykstra's excellent tutorial on [MVC and EF](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span></span>

<span data-ttu-id="48aa7-175">![DB_explorer](accessing-your-models-data-from-a-controller/_static/image10.png "DB_explorer")</span><span class="sxs-lookup"><span data-stu-id="48aa7-175">![DB_explorer](accessing-your-models-data-from-a-controller/_static/image10.png "DB_explorer")</span></span>

<span data-ttu-id="48aa7-176">Sağ `Movies` tablo ve seçin **Show Table Data** oluşturduğunuz verileri görmek için.</span><span class="sxs-lookup"><span data-stu-id="48aa7-176">Right-click the `Movies` table and select **Show Table Data** to see the data you created.</span></span>

![](accessing-your-models-data-from-a-controller/_static/image11.png) 

![](accessing-your-models-data-from-a-controller/_static/image12.png)

<span data-ttu-id="48aa7-177">Sağ `Movies` tablo ve seçin **açık tablo tanımı** bu Entity Framework kod sizin için oluşturduğu ilk yapısı tablosunu görmek için.</span><span class="sxs-lookup"><span data-stu-id="48aa7-177">Right-click the `Movies` table and select **Open Table Definition** to see the table structure that Entity Framework Code First created for you.</span></span>

![](accessing-your-models-data-from-a-controller/_static/image13.png)

![](accessing-your-models-data-from-a-controller/_static/image14.png)

<span data-ttu-id="48aa7-178">Bildirim nasıl şeması `Movies` tablo eşlenir `Movie` daha önce oluşturduğunuz sınıfı.</span><span class="sxs-lookup"><span data-stu-id="48aa7-178">Notice how the schema of the `Movies` table maps to the `Movie` class you created earlier.</span></span> <span data-ttu-id="48aa7-179">Entity Framework Code First otomatik olarak oluşturulan bu şemayı dayanarak için `Movie` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="48aa7-179">Entity Framework Code First automatically created this schema for you based on your `Movie` class.</span></span>

<span data-ttu-id="48aa7-180">İşlemi tamamladığınızda, bağlantıyı sağ tıklayarak kapatın *MovieDBContext* ve seçerek **kapatmak bağlantı**.</span><span class="sxs-lookup"><span data-stu-id="48aa7-180">When you're finished, close the connection by right clicking *MovieDBContext* and selecting **Close Connection**.</span></span> <span data-ttu-id="48aa7-181">(Bağlantı kapatmayın, projenin bir sonraki çalıştırmanızda bir hata alabilirsiniz).</span><span class="sxs-lookup"><span data-stu-id="48aa7-181">(If you don't close the connection, you might get an error the next time you run the project).</span></span>

<span data-ttu-id="48aa7-182">![](accessing-your-models-data-from-a-controller/_static/image15.png "CloseConnection")</span><span class="sxs-lookup"><span data-stu-id="48aa7-182">![](accessing-your-models-data-from-a-controller/_static/image15.png "CloseConnection")</span></span>

<span data-ttu-id="48aa7-183">Bir veritabanı ve görüntüleme, düzenleme, güncelleştirme ve verileri silmek için sayfaları artık sahipsiniz.</span><span class="sxs-lookup"><span data-stu-id="48aa7-183">You now have a database and pages to display, edit, update and delete data.</span></span> <span data-ttu-id="48aa7-184">Sonraki öğreticide biz kurulmuş kodu kalan inceleyin ve ekleme bir `SearchIndex` yöntemi ve `SearchIndex` bu veritabanında filmler arama yapmanıza olanak tanıyan görünümü.</span><span class="sxs-lookup"><span data-stu-id="48aa7-184">In the next tutorial, we'll examine the rest of the scaffolded code and add a `SearchIndex` method and a `SearchIndex` view that lets you search for movies in this database.</span></span> <span data-ttu-id="48aa7-185">Entity Framework MVC ile kullanma hakkında daha fazla bilgi için bkz: [bir ASP.NET MVC uygulaması için Entity Framework veri modeli oluşturma](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="48aa7-185">For more information on using Entity Framework with MVC, see [Creating an Entity Framework Data Model for an ASP.NET MVC Application](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="48aa7-186">[Önceki](creating-a-connection-string.md)
[sonraki](examining-the-edit-methods-and-edit-view.md)</span><span class="sxs-lookup"><span data-stu-id="48aa7-186">[Previous](creating-a-connection-string.md)
[Next](examining-the-edit-methods-and-edit-view.md)</span></span>
