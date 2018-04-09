---
uid: mvc/overview/older-versions/getting-started-with-aspnet-mvc4/adding-a-controller
title: Bir denetleyici ekleme | Microsoft Docs
author: Rick-Anderson
description: 'Not: Bu öğreticide güncelleştirilmiş bir sürümünü burada ASP.NET MVC 5 ve Visual Studio 2013 kullanan kullanılabilir. Bu daha güvenli, daha kolay izleyin ve gösteri...'
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/28/2012
ms.topic: article
ms.assetid: 0267d31c-892f-49a1-9e7a-3ae8cc12b2ca
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-aspnet-mvc4/adding-a-controller
msc.type: authoredcontent
ms.openlocfilehash: bb76c0a87d935322406b9d8e18fbdb3e41f327f5
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
---
<a name="adding-a-controller"></a><span data-ttu-id="4aff1-104">Bir denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="4aff1-104">Adding a Controller</span></span>
====================
<span data-ttu-id="4aff1-105">tarafından [Rick Anderson](https://github.com/Rick-Anderson)</span><span class="sxs-lookup"><span data-stu-id="4aff1-105">by [Rick Anderson](https://github.com/Rick-Anderson)</span></span>

> > [!NOTE]
> > <span data-ttu-id="4aff1-106">Bu öğretici güncelleştirilmiş bir sürümü kullanılabilir [burada](../../getting-started/introduction/getting-started.md) ASP.NET MVC 5 ve Visual Studio 2013'ü kullanır.</span><span class="sxs-lookup"><span data-stu-id="4aff1-106">An updated version of this tutorial is available [here](../../getting-started/introduction/getting-started.md) that uses ASP.NET MVC 5 and Visual Studio 2013.</span></span> <span data-ttu-id="4aff1-107">Daha güvenli, izlemek çok daha kolaydır ve daha fazla özelliklerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="4aff1-107">It's more secure, much simpler to follow and demonstrates more features.</span></span>


<span data-ttu-id="4aff1-108">MVC anlamına gelir *model-view-controller*.</span><span class="sxs-lookup"><span data-stu-id="4aff1-108">MVC stands for *model-view-controller*.</span></span> <span data-ttu-id="4aff1-109">MVC, iyi tasarlanmış, test edilebilir ve bakımını kolay uygulamaları geliştirmek için bir desen olur.</span><span class="sxs-lookup"><span data-stu-id="4aff1-109">MVC is a pattern for developing applications that are well architected, testable and easy to maintain.</span></span> <span data-ttu-id="4aff1-110">MVC tabanlı uygulamalar içerir:</span><span class="sxs-lookup"><span data-stu-id="4aff1-110">MVC-based applications contain:</span></span>

- <span data-ttu-id="4aff1-111">**M** odels: uygulama verilerini temsil eden ve bu verilere iş kurallarını zorunlu tutmak için doğrulama mantığını kullanan sınıfları.</span><span class="sxs-lookup"><span data-stu-id="4aff1-111">**M** odels: Classes that represent the data of the application and that use validation logic to enforce business rules for that data.</span></span>
- <span data-ttu-id="4aff1-112">**V** iews: dinamik olarak HTML yanıtları oluşturmak için uygulamanızın kullandığı şablon dosyalarını.</span><span class="sxs-lookup"><span data-stu-id="4aff1-112">**V** iews: Template files that your application uses to dynamically generate HTML responses.</span></span>
- <span data-ttu-id="4aff1-113">**C** ontrollers: gelen tarayıcı istekleri işleyen sınıflar model verileri almak ve tarayıcıya bir yanıt döndürür görünüm şablonları belirtin.</span><span class="sxs-lookup"><span data-stu-id="4aff1-113">**C** ontrollers: Classes that handle incoming browser requests, retrieve model data, and then specify view templates that return a response to the browser.</span></span>

<span data-ttu-id="4aff1-114">Biz, Bu öğretici serisinde, tüm bu kavramları kapsayan olması ve bunları bir uygulama oluşturmak için nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="4aff1-114">We'll be covering all these concepts in this tutorial series and show you how to use them to build an application.</span></span>

<span data-ttu-id="4aff1-115">Denetleyici sınıfı oluşturarak başlayalım.</span><span class="sxs-lookup"><span data-stu-id="4aff1-115">Let's begin by creating a controller class.</span></span> <span data-ttu-id="4aff1-116">İçinde **Çözüm Gezgini**, sağ *denetleyicileri* klasörünü ve ardından **denetleyici Ekle**.</span><span class="sxs-lookup"><span data-stu-id="4aff1-116">In **Solution Explorer**, right-click the *Controllers* folder and then select **Add Controller**.</span></span>

![](adding-a-controller/_static/image1.png)

<span data-ttu-id="4aff1-117">Yeni denetleyicinize ad &quot;HelloWorldController&quot;.</span><span class="sxs-lookup"><span data-stu-id="4aff1-117">Name your new controller &quot;HelloWorldController&quot;.</span></span> <span data-ttu-id="4aff1-118">Varsayılan şablon olarak bırakın **boş MVC denetleyicisi** tıklatıp **Ekle**.</span><span class="sxs-lookup"><span data-stu-id="4aff1-118">Leave the default template as **Empty MVC controller** and click **Add**.</span></span>

![Denetleyici ekleme](adding-a-controller/_static/image2.png)

<span data-ttu-id="4aff1-120">Fark **Çözüm Gezgini** yeni bir dosya adlandırılmış oluşturulduğunu *HelloWorldController.cs*.</span><span class="sxs-lookup"><span data-stu-id="4aff1-120">Notice in **Solution Explorer** that a new file has been created named *HelloWorldController.cs*.</span></span> <span data-ttu-id="4aff1-121">Dosya IDE içinde açılmış durumda.</span><span class="sxs-lookup"><span data-stu-id="4aff1-121">The file is open in the IDE.</span></span>

![](adding-a-controller/_static/image3.png)

<span data-ttu-id="4aff1-122">Dosya içeriğini aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="4aff1-122">Replace the contents of the file with the following code.</span></span>

[!code-csharp[Main](adding-a-controller/samples/sample1.cs)]

<span data-ttu-id="4aff1-123">Denetleyici yöntemleri HTML dizesi örnek olarak döndürür.</span><span class="sxs-lookup"><span data-stu-id="4aff1-123">The controller methods will return a string of HTML as an example.</span></span> <span data-ttu-id="4aff1-124">Denetleyici adlı `HelloWorldController` ve yukarıdaki ilk yöntem adlı `Index`.</span><span class="sxs-lookup"><span data-stu-id="4aff1-124">The controller is named `HelloWorldController` and the first method above is named `Index`.</span></span> <span data-ttu-id="4aff1-125">Şimdi bir tarayıcıdan çağırır.</span><span class="sxs-lookup"><span data-stu-id="4aff1-125">Let's invoke it from a browser.</span></span> <span data-ttu-id="4aff1-126">Uygulama (F5 veya Ctrl + F5'e basın) çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4aff1-126">Run the application (press F5 or Ctrl+F5).</span></span> <span data-ttu-id="4aff1-127">Tarayıcıda append &quot;HelloWorld&quot; adres çubuğundaki yolu.</span><span class="sxs-lookup"><span data-stu-id="4aff1-127">In the browser, append &quot;HelloWorld&quot; to the path in the address bar.</span></span> <span data-ttu-id="4aff1-128">(Örneğin, aşağıdaki, çizimdeki `http://localhost:1234/HelloWorld.`) sayfasını tarayıcıda aşağıdaki ekran görüntüsüne benzeyen arar.</span><span class="sxs-lookup"><span data-stu-id="4aff1-128">(For example, in the illustration below, it's `http://localhost:1234/HelloWorld.`) The page in the browser will look like the following screenshot.</span></span> <span data-ttu-id="4aff1-129">Yukarıdaki yöntemi kodu doğrudan bir dize döndürdü.</span><span class="sxs-lookup"><span data-stu-id="4aff1-129">In the method above, the code returned a string directly.</span></span> <span data-ttu-id="4aff1-130">Yalnızca bazı HTML döndürmek için sistem söylediyse ve olduğu!</span><span class="sxs-lookup"><span data-stu-id="4aff1-130">You told the system to just return some HTML, and it did!</span></span>

![](adding-a-controller/_static/image4.png)

<span data-ttu-id="4aff1-131">ASP.NET MVC, gelen URL bağlı olarak farklı bir denetleyici sınıfları (ve bunların içindeki farklı eylem yöntemleri) çağırır.</span><span class="sxs-lookup"><span data-stu-id="4aff1-131">ASP.NET MVC invokes different controller classes (and different action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="4aff1-132">ASP.NET MVC tarafından kullanılan varsayılan URL yönlendirme mantığı çağırmak için kodu nedir belirlemek için bu gibi bir biçim kullanır:</span><span class="sxs-lookup"><span data-stu-id="4aff1-132">The default URL routing logic used by ASP.NET MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="4aff1-133">URL ilk bölümü yürütmek için denetleyici sınıfını belirler.</span><span class="sxs-lookup"><span data-stu-id="4aff1-133">The first part of the URL determines the controller class to execute.</span></span> <span data-ttu-id="4aff1-134">Bu nedenle */HelloWorld* eşlendiği `HelloWorldController` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="4aff1-134">So */HelloWorld* maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="4aff1-135">URL ikinci bölümü eylem yöntemine yürütmek için sınıf belirler.</span><span class="sxs-lookup"><span data-stu-id="4aff1-135">The second part of the URL determines the action method on the class to execute.</span></span> <span data-ttu-id="4aff1-136">Bu nedenle */HelloWorld/dizin* neden olacağından `Index` yöntemi `HelloWorldController` yürütmek için sınıf.</span><span class="sxs-lookup"><span data-stu-id="4aff1-136">So */HelloWorld/Index* would cause the `Index` method of the `HelloWorldController` class to execute.</span></span> <span data-ttu-id="4aff1-137">Biz yalnızca Gözat gerekiyordu bildirimi */HelloWorld* ve `Index` yöntemi, varsayılan olarak kullanıldı.</span><span class="sxs-lookup"><span data-stu-id="4aff1-137">Notice that we only had to browse to */HelloWorld* and the `Index` method was used by default.</span></span> <span data-ttu-id="4aff1-138">Bir yöntem adlı olmasıdır `Index` bir açıkça belirtilmezse, bir denetleyicisinde çağrılacak varsayılan yöntemdir.</span><span class="sxs-lookup"><span data-stu-id="4aff1-138">This is because a method named `Index` is the default method that will be called on a controller if one is not explicitly specified.</span></span>

<span data-ttu-id="4aff1-139">Gözat `http://localhost:xxxx/HelloWorld/Welcome`.</span><span class="sxs-lookup"><span data-stu-id="4aff1-139">Browse to `http://localhost:xxxx/HelloWorld/Welcome`.</span></span> <span data-ttu-id="4aff1-140">`Welcome` Yöntemi çalışır ve bir dize döndürür &quot;Hoş Geldiniz eylem yöntem budur... &quot;.</span><span class="sxs-lookup"><span data-stu-id="4aff1-140">The `Welcome` method runs and returns the string &quot;This is the Welcome action method...&quot;.</span></span> <span data-ttu-id="4aff1-141">Varsayılan MVC eşlemesi `/[Controller]/[ActionName]/[Parameters]`.</span><span class="sxs-lookup"><span data-stu-id="4aff1-141">The default MVC mapping is `/[Controller]/[ActionName]/[Parameters]`.</span></span> <span data-ttu-id="4aff1-142">Bu URL için denetleyicisidir `HelloWorld` ve `Welcome` eylem yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="4aff1-142">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="4aff1-143">Kullandığınız parolalardan `[Parameters]` URL henüz parçası.</span><span class="sxs-lookup"><span data-stu-id="4aff1-143">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![](adding-a-controller/_static/image5.png)

<span data-ttu-id="4aff1-144">Böylece denetleyiciye URL'den bazı parametre bilgilerini geçirebilirsiniz örnek biraz değiştirelim (örneğin, */HelloWorld/Hoş Geldiniz? adı Scott =&amp;numtimes = 4*).</span><span class="sxs-lookup"><span data-stu-id="4aff1-144">Let's modify the example slightly so that you can pass some parameter information from the URL to the controller (for example, */HelloWorld/Welcome?name=Scott&amp;numtimes=4*).</span></span> <span data-ttu-id="4aff1-145">Değişiklik, `Welcome` yöntemi iki parametre aşağıda gösterildiği gibi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4aff1-145">Change your `Welcome` method to include two parameters as shown below.</span></span> <span data-ttu-id="4aff1-146">Kod belirtmek için C# isteğe bağlı parametre özelliği kullandığına dikkat edin `numTimes` Bu parametre için değer iletilmezse, parametresi 1 olarak varsayılan.</span><span class="sxs-lookup"><span data-stu-id="4aff1-146">Note that the code uses the C# optional-parameter feature to indicate that the `numTimes` parameter should default to 1 if no value is passed for that parameter.</span></span>

[!code-csharp[Main](adding-a-controller/samples/sample2.cs)]

<span data-ttu-id="4aff1-147">Uygulamanızı çalıştırın ve örnek URL'sine gidin (`http://localhost:xxxx/HelloWorld/Welcome?name=Scott&numtimes=4)`.</span><span class="sxs-lookup"><span data-stu-id="4aff1-147">Run your application and browse to the example URL (`http://localhost:xxxx/HelloWorld/Welcome?name=Scott&numtimes=4)`.</span></span> <span data-ttu-id="4aff1-148">İçin farklı değerler deneyin `name` ve `numtimes` URL.</span><span class="sxs-lookup"><span data-stu-id="4aff1-148">You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="4aff1-149">[ASP.NET MVC model bağlama sistem](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx) adres çubuğundaki sorgu dizesi yönteminizi parametrelerinde adlandırılmış parametreleri otomatik olarak eşlenir.</span><span class="sxs-lookup"><span data-stu-id="4aff1-149">The [ASP.NET MVC model binding system](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx) automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span>

![](adding-a-controller/_static/image6.png)

<span data-ttu-id="4aff1-150">Her iki Bu örneklerde denetleyicisi bulunurken &quot;VC&quot; MVC kısmı — diğer bir deyişle, Görünüm ve denetleyici çalışma.</span><span class="sxs-lookup"><span data-stu-id="4aff1-150">In both these examples the controller has been doing the &quot;VC&quot; portion of MVC — that is, the view and controller work.</span></span> <span data-ttu-id="4aff1-151">Denetleyici HTML doğrudan döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="4aff1-151">The controller is returning HTML directly.</span></span> <span data-ttu-id="4aff1-152">Normalde kodu çok kullanışsız hale beri HTML doğrudan döndürerek denetleyicileri istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="4aff1-152">Ordinarily you don't want controllers returning HTML directly, since that becomes very cumbersome to code.</span></span> <span data-ttu-id="4aff1-153">Bunun yerine genellikle ayrı görünüm şablon dosyası HTML yanıtı oluşturmak yardımcı olmak için kullanacağız.</span><span class="sxs-lookup"><span data-stu-id="4aff1-153">Instead we'll typically use a separate view template file to help generate the HTML response.</span></span> <span data-ttu-id="4aff1-154">Nasıl biz bunu yapmak için sonraki olarak bakalım.</span><span class="sxs-lookup"><span data-stu-id="4aff1-154">Let's look next at how we can do this.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4aff1-155">[Önceki](intro-to-aspnet-mvc-4.md)
> [sonraki](adding-a-view.md)</span><span class="sxs-lookup"><span data-stu-id="4aff1-155">[Previous](intro-to-aspnet-mvc-4.md)
[Next](adding-a-view.md)</span></span>
